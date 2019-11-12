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

All Topaz compute nodes are configured as a shared resource, i.e. multiple users can run jobs on the same node. This is different than configuration of Zeus P100 GPU nodes.

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
### Batch jobs
### Interactive mode
