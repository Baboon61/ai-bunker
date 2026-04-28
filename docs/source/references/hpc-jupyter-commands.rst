HPC Jupyter command templates
=============================

This page collects reusable command templates for the HPC Jupyter tunnel
workflow. Replace placeholders before running the commands.

Placeholders
------------

.. code-block:: text

   <hpc-login>        Login node hostname
   <compute-node>     Compute node running JupyterLab
   <account>          SLURM account
   <partition>        SLURM partition
   <project-dir>      Project directory on the HPC
   <kernel-name>      Short Jupyter kernel name
   <local-port>       Workstation port
   <remote-port>      Jupyter port on the compute node

uv project kernel
-----------------

.. code-block:: console

   $ cd <project-dir>
   $ uv add --dev ipykernel
   $ uv run ipython kernel install \
       --user \
       --name <kernel-name> \
       --display-name "Python (<kernel-name>)"

Interactive SLURM allocation
----------------------------

.. code-block:: console

   $ salloc \
       --account=<account> \
       --partition=<partition> \
       --time=02:00:00 \
       --cpus-per-task=4 \
       --mem=16G

Show the compute node selected by SLURM:

.. code-block:: console

   $ hostname

Start JupyterLab
----------------

Run this command from inside the SLURM allocation:

.. code-block:: console

   $ cd <project-dir>
   $ uv run --with jupyter jupyter lab \
       --no-browser \
       --ip=127.0.0.1 \
       --port=<remote-port>

SSH tunnel
----------

Run this command on the workstation:

.. code-block:: console

   $ ssh -N \
       -L <local-port>:<compute-node>:<remote-port> \
       <hpc-login>

Open the forwarded server from the workstation:

.. code-block:: text

   http://127.0.0.1:<local-port>/?token=<token>

Batch job variant
-----------------

Some clusters prefer launching Jupyter from a submitted batch job. Save a script
like this as ``jupyter-lab.sbatch`` and adapt the resource request:

.. code-block:: bash

   #!/usr/bin/env bash
   #SBATCH --account=<account>
   #SBATCH --partition=<partition>
   #SBATCH --time=02:00:00
   #SBATCH --cpus-per-task=4
   #SBATCH --mem=16G
   #SBATCH --job-name=jupyter-lab

   set -euo pipefail

   cd <project-dir>

   echo "Compute node: $(hostname)"
   uv run --with jupyter jupyter lab \
       --no-browser \
       --ip=127.0.0.1 \
       --port=<remote-port>

Submit the job and inspect its log to find the compute node and Jupyter token:

.. code-block:: console

   $ sbatch jupyter-lab.sbatch
   $ squeue --me
