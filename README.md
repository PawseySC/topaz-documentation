## System configuration 
Topaz most important hardware characteristics:
* 22 compute nodes, each node is equipped with:
* 2x Intel Xeon Silver 4215 CPUs (8 cores in each CPU, 16 cores in total), 
* 2x NVIDIA V100 GPU cards (16GB HBM2 memory each),
* 192 GB RAM.
* 100Gbit Infiniband between compute nodes. 

## Queueing system configuration
Topaz resources are managed by SLURM queueing system. For detailed information about SLURM usage please refer to: https://support.pawsey.org.au/documentation/display/US/Job+Scheduling

There are two partitions (queues) on Topaz:
* gpuq - for all compute jobs with 20 compute nodes (t001 - t020), max. 24h walltime and max. 8 running jobs per user,
* gpuq-dev - for development purposes with 2 compute nodes (t021, t022), max. 1h walltime and max. 1 running job. 

Topaz compute nodes in the gpuq partition are configured as a shared resource, i.e. multiple users can run jobs on the same node. This is different than configuration of Zeus P100 GPU nodes.

## Available software
Topaz most important software characteristics: 
* Centos 7.6 OS,
* SLURM 18.08.6 queueing system,
* Compilers: gcc/8.3.0, intel/19.0.5, pgi/19.7, clang/9.0.0
* Python/3.6.3
* CUDA/10.1
* MPI: OpenMPI/4.0.2, IntelMPI/19.0.5
* Singularity/3.3.0
* Profilers: ARM Forge/19.1.3, Intel VTune/19.0.4, NVIDIA Nsight

## Submitting jobs 
Topaz compute nodes in the gpuq partition are configured as a shared resource. This means that it is especially important to specify number of GPUs, number of tasks and ammount of memory required by the job. If not specified, by default job will be allocated with a single CPU core, no GPUs and around 10GB of RAM.

Please refer to the following examples which demonstrate different job allocation modes.
### Batch jobs examples
#### Non-MPI code utilising a single GPU
In the following example we assume that the ``gpu_code`` is a non-MPI application and can utilise a single GPU. We adjust the ammount of memory available for the job since by default we would be given only about 10gb per process. 
```
#!/bin/bash -l
#SBATCH --nodes=1
#SBATCH --gres=gpu:1
#SBATCH --ntasks-per-node=1
#SBATCH --mem=90gb
#SBATCH --time=00:01:00
#SBATCH --partition=gpuq
#SBATCH --account=[your-project]
#SBATCH --export=NONE
module load cuda
srun -n 1 --export=all ./gpu_code
```
#### MPI code utilising 2 GPUs
In the following example we assume that the ``gpu_code`` is a MPI application and can utilise a single GPU per process. We run two processes per node, we adjust the ammount of memory per node single by default we would be given only 10gb per process. We also use ``--ntasks-per-socket`` option to run a single process per socket in order to maximise memory badwidth between CPU and GPU memory.
```
#!/bin/bash -l
#SBATCH --nodes=1
#SBATCH --gres=gpu:2
#SBATCH --ntasks-per-node=2
#SBATCH --ntasks-per-socket=1
#SBATCH --mem=180gb
#SBATCH --time=00:01:00
#SBATCH --partition=gpuq
#SBATCH --account=[your-project]
#SBATCH --export=NONE
module load cuda
srun -n 2 --export=all ./gpu_code
```
#### OpenMP code utilising a single GPU and a single CPU
In the following example we assume that the ``gpu_code`` is an OpenMP code utilising a single GPU and all CPU cores. We run a single process per node and 8 OpenMP threads (single CPU) leaving the other resources (CPU and GPU) available for other jobs. We also adjust the amount of memory to 90gb since by default we would be given only 10gb per process. In this example there we will be allocated with CPU cores and memory of a single CPU (single NUMA node). 
```
#!/bin/bash -l
#SBATCH --nodes=1
#SBATCH --gres=gpu:1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=90gb
#SBATCH --time=00:01:00
#SBATCH --partition=gpuq
#SBATCH --account=[your-project]
#SBATCH --export=NONE
module load cuda
export OMP_NUM_THREADS=8
srun -n 1 --export=all ./gpu_code
```
#### MPI + OpenMP code utilising two GPUs and all available CPU cores per node
In the following example we assume that ``gpu_code`` ia a MPI + OpenMP code utilising a single GPU per process and capable of using OpenMP mutlithreading to additionally utilise all CPU cores available within the node. We run the code on two nodes with two processes per node, each using a single GPU. We use ``--ntasks-per-socket`` option to run a single process per socket in order to maximise memory badwidth between CPU and GPU memory. We also adjust the amount of memory to 180gb since by default we would be given only 10gb per process.
```
#!/bin/bash -l
#SBATCH --nodes=2
#SBATCH --gres=gpu:2
#SBATCH --ntasks-per-node=2
#SBATCH --ntasks-per-socket=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=180gb
#SBATCH --time=00:01:00
#SBATCH --partition=gpuq
#SBATCH --account=[your-project]
#SBATCH --export=NONE
module load cuda
export OMP_NUM_THREADS=8
srun -n 4 --export=all ./gpu_code
```
### Interactive mode
As on other Pawsey systems, ``salloc`` command can be used to run interactive sessions. ``#SBATCH`` options mentioned above can be used to specify various interactive job parameters, e.g. to run a MPI code utilising 2 GPUs one can open an interactive session with the following command:   
```
salloc --nodes=1 --gres=gpu:2 --ntasks-per-node=2 --ntasks-per-socket=1 --mem=180gb --time=00:05:00 --partition=gpuq --account=[your-project]
```

For all interactive sessions, after ``salloc`` has run and you are on a compute node, you will need to use the ``srun`` command to execute your commands. This is valid for all commands, for instance ``srun`` needs to be used in order to run ``nvidia-smi`` command on the interactive node:
```
$ salloc -N 1 -pgpuq --gres=gpu:1 --ntasks-per-node=1
salloc: Granted job allocation 1861
salloc: Waiting for resource configuration
salloc: Nodes t016 are ready for job
bash-4.2$ nvidia-smi
No devices were found
bash-4.2$ srun -n1 nvidia-smi
Tue Nov 12 11:54:58 2019
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.67       Driver Version: 418.67       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla V100-PCIE...  On   | 00000000:18:00.0 Off |                    0 |
| N/A   28C    P0    25W / 250W |      0MiB / 16130MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```
