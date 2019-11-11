## System configuration 
Topaz most important hardware characteristics:
* 20 compute nodes, each node is equipped with:
* 2x Intel Xeon Silver 4215 CPUs (8 cores in each CPU, 16 cores in total), 
* 2x NVIDIA V100 GPU cards (16GB HBM2 memory each),
* 192 GB RAM.
* 100Gbit Infiniband between compute nodes. 

## Queueing system configuration

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
