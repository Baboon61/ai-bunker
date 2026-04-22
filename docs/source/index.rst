ai-bunker documentation
=======================

**ai-bunker** is a practical guide for running AI-assisted notebook work
against protected HPC data. The main workflow starts JupyterLab on an HPC
compute node, forwards it through an SSH tunnel, and connects from a local
VS Code or Cursor notebook session.

The key boundary is intentional: data and kernel execution stay on the HPC,
while the workstation provides the editing experience and AI assistance.

Start here
----------

New users should begin with the end-to-end walkthrough:

* :doc:`Run Jupyter on an HPC through an SSH tunnel <tutorials/hpc-jupyter-tunnel>`

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

   reference/hpc-jupyter-commands
