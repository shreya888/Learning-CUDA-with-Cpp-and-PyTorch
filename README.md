# Learning CUDA with C++ & PyTorch: Notes and Code
This repository documents key concepts, exercises, and insights gained from my exploration of CUDA (Compute Unified Device Architecture) programming, with examples in both C++ and PyTorch. This repo focuses on understanding and implementing CUDA to enhance code performance in C++ and PyTorch. The repository contains commented code examples, conceptual explanations, and a curated list of resources.


## Repository Structure
```
/Learning-CUDA-with-Cpp-and-PyTorch
├── README.md                          # Main README with project overview, structure, learning points, and detailed list of resources
├── Cpp                                # Code and explanations focused on CUDA in C++
│   ├── README.md                      # Overview of C++ section and how to navigate it
│   ├── CUDA Exercises                 # Folder for CUDA exercises from OLCF CUDA Training Series
│   │   ├── hw1                        # Homework 1 folder
│   │   │   ├── <file_name>.cu         # CUDA implementations for various exercises in hw1
│   │   │   └── README.md              # Notes for hw1 implementations and new code/CUDA insights learned
│   │   └── ...                        # Additional homework folders
└── PyTorch                            # Code and explanations focused on CUDA in PyTorch
    ├── README.md                      # Overview of PyTorch section and how to navigate it
    ├── GPU Mode                       # Folder for PyTorch code examples with output logs for GPU Mode lectures
    │   ├── <file_name>.py             # Various example scripts with comments
    │   ├── output_logs                # Folder for output log files
    │   └── README.md                  # Notes on CUDA using PyTorch and code implementations

```


## Terms to Remember:
1. GPU Kernel (functions)
2. Thread, Block, Grid
3. Host (CPU), Device (GPU)
4. Streaming Processors(SPs or cores)
5. Streaming Multiprocessor (SM or multiprocessor)
6. Warp (smallest unit (32 threads gen.) of execution on the device)
7. CPU: SIMD (Single Instruction, Multiple Data)
8. GPU: SIMT (Single Instruction, Multiple Threads)
9. Physical and Logical Memory
10. Data Transfer, Global memory, Local memory and registers, Constant Memory
11. Multithreading, Latency Hiding and Thread divergence

## My Current Learning List:
1. https://github.com/gpu-mode/lectures
2. https://www.youtube.com/@GPUMODE
3. https://github.com/olcf/cuda-training-series
4. https://github.com/CisMine/Parallel-Computing-Cuda-C - studied till chapter 8 today (MUST READ - very simplified topics with great analogies and examples, easy to follow)
5. https://forums.developer.nvidia.com
6. https://discuss.pytorch.org
7. https://pytorch.org/docs
8. https://wandb.ai/wandb/trace/reports/Using-the-PyTorch-Profiler-with-W-B--Vmlldzo5MDE3NjU
9. https://gist.github.com/mingfeima/e08310d7e7bb9ae2a693adecf2d8a916
10. https://users.wfu.edu/choss/CUDA/lectures.html - studied till lecture 6 (MUST READ - well explained code and concepts)
11. https://docs.nvidia.com/cuda/cuda-c-best-practices-guide
12. https://docs.nvidia.com/cuda/cuda-c-programming-guide
