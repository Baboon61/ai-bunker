Execution boundaries for AI-assisted HPC notebooks
==================================================

AI-assisted notebooks can feel local because the editor, suggestions, and file
navigation run on the workstation. The important security boundary is the
kernel: the kernel process executes code, imports packages, opens files, and
returns outputs to the notebook UI.

In the ai-bunker workflow, the trusted side is the HPC. JupyterLab and the
selected notebook kernel run on an HPC compute node, close to protected data.
The workstation connects over an SSH tunnel and acts as the user interface.

What stays local
----------------

The workstation can hold:

* Notebook source files that do not contain protected data.
* Project notes, scripts, and configuration that are safe to store locally.
* VS Code or Cursor extensions and AI-assisted editing tools.

Local AI assistance should be treated as an editor aid. Do not paste protected
records, tokens, paths with sensitive identifiers, or regulated outputs into an
external assistant unless your organization explicitly allows that.

What stays on the HPC
---------------------

The HPC should hold:

* Protected datasets.
* The Jupyter server used for protected analysis.
* uv-managed execution environments for kernels that read protected data.
* Temporary files, caches, and outputs that may contain sensitive values.

When a notebook cell runs, it runs where the selected kernel runs. If the kernel
is on the HPC, paths are resolved on the HPC filesystem and package imports come
from the HPC environment.

Why uv kernels matter
---------------------

``uv`` makes the execution environment reproducible and project-scoped. The
project can define its dependencies in ``pyproject.toml``, install
``ipykernel`` as a development dependency, and register a named kernel for
Jupyter.

This separation lets Jupyter offer multiple kernels without mixing unrelated
project dependencies. Users can switch kernels from the notebook interface while
keeping each project environment explicit.

Notebook output is data too
---------------------------

Even when raw datasets never leave the HPC, notebook outputs may contain
sensitive previews, aggregate values, error messages, paths, or identifiers.
Clear or review outputs before committing notebooks to a local repository.
