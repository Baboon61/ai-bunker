ai-bunker documentation
=======================

**ai-bunker** is a practical guide for running AI-assisted notebook
development against protected `HPC <https://en.wikipedia.org/wiki/High-performance_computing>`__
data. The main workflow starts
`JupyterLab <https://jupyterlab.readthedocs.io/>`__ on an HPC compute node,
forwards it through an `SSH <https://www.openssh.com/>`__ tunnel, and connects
from a local IDE like `VS Code <https://code.visualstudio.com/>`__ or
`Cursor <https://cursor.com/>`__. The remote Jupyter server can expose Python
or R kernels while keeping execution near the protected data.

The key boundary is intentional: data and kernel execution stay on the HPC,
while the workstation provides the editing experience and AI assistance.

Start here
----------

New users should begin with the end-to-end walkthrough:

* :doc:`Run JupyterLab on an HPC through an SSH tunnel <tutorials/hpc-jupyter-tunnel>`

Background and command templates are available in the concept and reference
sections.

.. note::

   This project is under active development.

.. toctree::
   :caption: Tutorials
   :hidden:

   tutorials/hpc-jupyter-tunnel

.. toctree::
   :caption: Concepts
   :hidden:

   concepts/execution-boundaries

.. toctree::
   :caption: Reference
   :hidden:

   reference/install-uv-python-kernel
   reference/install-conda-kernels
   reference/hpc-jupyter-commands
