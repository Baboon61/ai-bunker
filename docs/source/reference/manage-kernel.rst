Verify and manage Jupyter kernels
=================================

Use this reference after creating a kernel with ``uv`` or ``conda``. It covers
how to confirm that Jupyter can see the kernel, inspect its kernelspec, rename
it, remove it from the picker, and clean up underlying environments when that
is appropriate.

Verify the kernel
-----------------

List available kernels from the HPC:

.. code-block:: console

   $ jupyter kernelspec list

Start or refresh the remote Jupyter server, then select the expected kernel
from VS Code or Cursor, such as ``Python (ai-bunker)`` or ``R (ai-bunker)``.

How kernels are registered
--------------------------

Jupyter kernels are registered as kernel specifications. A kernelspec is a
small metadata directory that tells Jupyter which command starts the runtime.
Removing a kernelspec removes the entry from Jupyter, but it does not delete the
project environment or conda environment behind it.

Inspect a kernel specification
------------------------------

List the registered kernels:

.. code-block:: console

   $ jupyter kernelspec list

Inspect the kernelspec file for a Python or R kernel:

.. code-block:: console

   $ cat ~/.local/share/jupyter/kernels/py-ai-bunker/kernel.json
   $ cat ~/.local/share/jupyter/kernels/r-ai-bunker/kernel.json

Rename a kernel
---------------

Rename a uv-managed Python kernel by reinstalling its kernelspec with a new
name or display name:

.. code-block:: console

   $ uv run ipython kernel install \
       --user \
       --name py-ai-bunker-v2 \
       --display-name "Python (ai-bunker v2)"

Rename a conda-managed Python kernel:

.. code-block:: console

   $ conda activate py-ai-bunker
   $ python -m ipykernel install \
       --user \
       --name py-ai-bunker-v2 \
       --display-name "Python (ai-bunker v2)"

Rename an R kernel from the conda environment:

.. code-block:: console

   $ conda activate r-ai-bunker
   $ R -e "IRkernel::installspec(name = 'r-ai-bunker-v2', displayname = 'R (ai-bunker v2)', user = TRUE)"

If you no longer need the old entry, remove it after reinstalling the new one.

Remove a kernel from Jupyter
----------------------------

Remove old entries from the Jupyter kernel picker:

.. code-block:: console

   $ jupyter kernelspec remove py-ai-bunker
   $ jupyter kernelspec remove r-ai-bunker

This removes the kernelspec only.

Clean up underlying environments
--------------------------------

For uv-managed projects, removing the kernelspec does not delete the project
environment. Manage the project dependencies through ``pyproject.toml``,
``uv.lock``, and ``uv sync``.

For conda-managed kernels, delete an environment only when no kernels or jobs
still use it:

.. code-block:: console

   $ conda env remove -n py-ai-bunker
   $ conda env remove -n r-ai-bunker
