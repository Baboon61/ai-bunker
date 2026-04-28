Connect from VS Code or Cursor
==============================

This page shows how to connect a local VS Code or Cursor window to the
forwarded Jupyter server running on the HPC and then select the remote kernel
for notebook execution.

Prerequisites
-------------

Before starting, make sure:

* JupyterLab is already running on the HPC compute node.
* The SSH tunnel from the workstation to the compute node is active.
* You have the Jupyter server token printed by JupyterLab.

Connect the editor to the Jupyter server
----------------------------------------

In VS Code or Cursor:

1. Open your local notebook project.
2. Open the command palette.
3. Run ``Jupyter: Specify Jupyter Server for Connections``.
4. Choose an existing server or enter the forwarded URL:

   .. code-block:: text

      http://127.0.0.1:<local-port>/?token=<token>

Select the notebook kernel
--------------------------

After connecting to the Jupyter server:

1. Open a notebook.
2. Use the kernel picker in the notebook toolbar.
3. Select the ``Python (ai-bunker)`` or ``R (ai-bunker)`` kernel you created on
   the HPC.

The notebook file stays local in VS Code or Cursor, but each cell runs on the
selected remote kernel. Paths such as ``<data-dir>`` are resolved on the HPC by
that kernel, not on the workstation.

Next steps
----------

After the connection works, continue with
:doc:`hpc-jupyter-tunnel` to switch kernels, manage packages, and keep the AI
environment separated from the HPC environment.
