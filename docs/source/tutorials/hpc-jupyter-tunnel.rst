Run Jupyter on an HPC through an SSH tunnel
===========================================

This walkthrough shows how to run JupyterLab on a SLURM-managed HPC compute
node, tunnel the server to a workstation, and connect VS Code or Cursor to the
remote Jupyter server. The notebook editor stays local, while code execution
and protected data access happen on the HPC.

Architecture
------------

The workflow uses three layers:

* **Workstation**: VS Code or Cursor opens notebooks and provides AI assistance.
* **SSH tunnel**: a local port forwards browser and notebook traffic to the HPC.
* **HPC compute node**: JupyterLab and the selected notebook kernel run near the
  protected data.

Notebook files can live in your local project if your workflow only stores code,
queries, and non-sensitive outputs there. Any notebook that reads protected data
must use a kernel running on the HPC, because the kernel process is what opens
files, imports packages, and executes code.

Prerequisites
-------------

Before starting, make sure you have:

* SSH access to the HPC login node.
* A SLURM partition that allows interactive or batch jobs.
* ``uv`` available on the HPC.
* VS Code or Cursor installed on the workstation with Jupyter support enabled.
* A project with a ``pyproject.toml`` file.
* Access to the HPC filesystem location that contains the protected data.

The examples below use placeholders. Replace them for your site:

.. code-block:: text

   <hpc-login>        Login node hostname, such as login.cluster.example
   <account>          SLURM account or allocation name
   <partition>        SLURM partition or queue
   <project-dir>      Project directory on the HPC
   <data-dir>         Protected data directory on the HPC
   <local-port>       Port opened on the workstation, such as 8888
   <remote-port>      Port opened by Jupyter on the HPC, such as 8888

Prepare the uv project on the HPC
---------------------------------

Log in to the HPC and move to the project directory that will provide the
runtime environment for the notebook kernel:

.. code-block:: console

   $ ssh <hpc-login>
   $ cd <project-dir>

If the project is not already managed by ``uv``, initialize it or make sure it
has a valid ``pyproject.toml``. Add the Jupyter kernel dependency to the project:

.. code-block:: console

   $ uv add --dev ipykernel

Install a named kernel for the project:

.. code-block:: console

   $ uv run ipython kernel install --user --name ai-bunker --display-name "Python (ai-bunker)"

This registers a kernel specification that Jupyter can show in the notebook
kernel picker. The kernel uses the project environment created by ``uv``.

Allocate a SLURM job
--------------------

Start an interactive job for the Jupyter server. The exact resource request
depends on your HPC policy and workload:

.. code-block:: console

   $ salloc \
       --account=<account> \
       --partition=<partition> \
       --time=02:00:00 \
       --cpus-per-task=4 \
       --mem=16G

After the allocation starts, identify the compute node:

.. code-block:: console

   $ hostname

Keep this terminal open. The reported hostname is the node that the SSH tunnel
must reach.

Start JupyterLab on the compute node
------------------------------------

From inside the SLURM allocation, start JupyterLab without opening a browser:

.. code-block:: console

   $ cd <project-dir>
   $ uv run --with jupyter jupyter lab \
       --no-browser \
       --ip=127.0.0.1 \
       --port=<remote-port>

Jupyter prints a URL that includes a token. Keep the token private. Do not paste
it into source files, shared tickets, commits, or chat logs.

Create the SSH tunnel from the workstation
------------------------------------------

In a workstation terminal, forward a local port to the Jupyter port on the HPC
compute node. Many HPC systems require a jump through the login node:

.. code-block:: console

   $ ssh -N \
       -L <local-port>:<compute-node>:<remote-port> \
       <hpc-login>

For example, if Jupyter is listening on port ``8888`` on compute node
``cn042``:

.. code-block:: console

   $ ssh -N -L 8888:cn042:8888 login.cluster.example

Keep the tunnel running while you use the notebooks.

Connect from VS Code or Cursor
------------------------------

In VS Code or Cursor:

1. Open your local notebook project.
2. Open the command palette.
3. Run ``Jupyter: Specify Jupyter Server for Connections``.
4. Choose an existing server or enter the forwarded URL:

   .. code-block:: text

      http://127.0.0.1:<local-port>/?token=<token>

5. Open a notebook and select the ``Python (ai-bunker)`` kernel.

The notebook file is edited locally, but cells run in the selected kernel on the
HPC. Paths such as ``<data-dir>`` are resolved by the remote kernel, not by the
workstation.

Switch kernels in a notebook
----------------------------

Each notebook can use a different registered kernel. Use the kernel picker in
VS Code or Cursor to switch between project environments. If you add a new
project environment later, register another named kernel:

.. code-block:: console

   $ cd <project-dir>
   $ uv add --dev ipykernel
   $ uv run ipython kernel install --user --name <kernel-name> --display-name "Python (<kernel-name>)"

Restart Jupyter or refresh the kernel list if the new kernel does not appear.

Install packages intentionally
------------------------------

Prefer changing the project environment from a terminal on the HPC:

.. code-block:: console

   $ uv add pandas matplotlib

For temporary or exploratory packages, use ``uv pip install`` inside the project
environment with care:

.. code-block:: console

   $ uv pip install <package>

Avoid ad hoc package installation from notebook cells unless your team has
agreed that notebooks may mutate their execution environment.

Security checklist
------------------

Before committing or sharing notebook work:

* Confirm protected datasets were read from HPC paths only.
* Clear outputs that may contain sensitive values, previews, or derived records.
* Keep Jupyter tokens out of committed files and shared messages.
* Stop JupyterLab when the session ends.
* Close the SSH tunnel after stopping the notebook server.
* Cancel the SLURM allocation if it remains active.

Troubleshooting
---------------

If the browser or IDE cannot connect, check that Jupyter is still running, the
SLURM allocation is still active, and the tunnel points to the compute node
reported by ``hostname``.

If the notebook cannot see protected files, verify that the selected kernel is
the HPC kernel and that the path exists on the HPC filesystem.

If the expected kernel is missing, rerun the ``ipython kernel install`` command
from the uv project and refresh the Jupyter server connection.

See :doc:`../reference/hpc-jupyter-commands` for reusable command templates and
:doc:`../concepts/execution-boundaries` for the execution and data boundary
model.
