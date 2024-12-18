# Homework 1

In Homework 1, I practiced foundational CUDA programming skills, including GPU memory allocation, data transfer between the host CPU and GPU, and kernel launching. Each exercise provided a code skeleton, which I completed by filling in essential CUDA functions and logic.

## Exercise Overview
* **Hello World** - [`hello.cu`](./hello.cu) - A simple parallel "Hello World" program using CUDA.
* **Vector Add** - [`vector_add.cu`](./vector_add.cu) - A basic element-wise vector addition program.
* **Matrix Multiply (Naive)** - [`matrix_mul.cu`](./matrix_mul.cu) - A naive implementation of matrix multiplication, using 2D threadblock/grid indexing.


## **1. Hello World**
The goal of this exercise was to create a simple CUDA "Hello World" program that prints a unique message from each thread. The initial code skeleton was provided in `hello.cu`.

### Insights

1. **CUDA Kernel (`__global__` keyword)**:
   - The `__global__` keyword before the `hello` function defines it as a CUDA kernel, which means it will run on the GPU instead of the CPU.
   - When we call `hello<<<2, 2>>>();`, we're launching this kernel on the GPU with specific grid and block dimensions.

2. **Grid and Block Configuration**:
   - The `<<<2, 2>>>` syntax is used to specify the **number of blocks and threads per block**. Here, we use 2 blocks and 2 threads per block, giving a total of 4 threads.
   - Each thread will execute the code inside the `hello` kernel function. The exact message printed will depend on the block and thread indices.

3. **Indices (`blockIdx` and `threadIdx`)**:
   - `blockIdx.x` and `threadIdx.x` are built-in variables in CUDA, providing the **current block index** and **thread index** in the x-dimension.
   - These indices help each thread know its position in the grid and can be essential for tasks where each thread needs to work on specific data.

4. **`cudaDeviceSynchronize()`**:
   - CUDA operations are **asynchronous**, meaning that by default, the CPU will not wait for the GPU kernel to finish execution.
   - `cudaDeviceSynchronize()` forces the CPU to wait until all GPU operations have completed, ensuring that the program doesn’t terminate before the kernel finishes and prints its output.

5. **Output on GPU**:
   - Unlike standard `printf` on the CPU, printing from the GPU may produce output in a non-deterministic order because threads may execute in parallel.

6. **Compilation and Execution**:
   - To compile and run the program use commands like below:
    ```
    nvcc -o hello hello.cu
    ./hello.exe
    ```
   - `nvcc` is the NVIDIA CUDA compiler, similar to `gcc/g++` for `C++` code. This will generate 3 files with extensions - `.exe`, `.exp`, `.lib`. Running hello.exe outputs unique messages from each thread.


### Expected Output
The expected output, with thread and block identifiers, looks like this (ordering may vary):
```
Hello from block: 0, thread: 0
Hello from block: 0, thread: 1
Hello from block: 1, thread: 0
Hello from block: 1, thread: 1
```


## **2. Vector Add**

This code performs element-wise vector addition on the GPU using CUDA. Here's an explanation of the key sections:

### Insights

1. **Error Checking Macro**:
   - The **`cudaCheckErrors`** macro is defined to check and report for any CUDA errors after every CUDA API call.
   - If an error is encountered, it prints the message, error description, and location in the file, and then exits the program.
   - Good practice to rigorously check these error codes. The provided macro makes this job easier.

2. **Constant Definitions**:
   - **`DSIZE`**: Defines the size of the vectors being added, i.e., the number of elements in each vector.
   - **`block_size`**: Sets the number of threads per block (256 in this case).

3. **CUDA Kernel `vadd`**:
   - The `vadd` kernel performs element-wise addition of two vectors, A and B, storing the result in C. Each thread is responsible for one element.
   - **`ds`**: Kernel parameter in the vadd function used to pass the size of the vectors to the kernel.
   - **Global thread index `idx = threadIdx.x + blockDim.x * blockIdx.x`**:
     - `threadIdx.x`: The offset (local position) of a thread within its block.
     - `blockDim.x * blockIdx.x`: Computes the total number of threads in all preceding blocks. This product gives the offset needed to find the correct position of the first thread in the current block within the global array.

4. **Memory Management and Data Transfer**:
   - **`cudaMalloc`**: Allocates size bytes of linear memory on the GPU device and returns in `*devPtr` a pointer to the allocated memory.
     - Analogous to `malloc` in C for memory allocation on the host, but `cudaMalloc` allocates memory in the global memory space of the CUDA device.
     - **Syntax**: `cudaMalloc(void** devPtr, size_t size);`
       - `devPtr`: Pointer to the allocated device memory.
       - `size`: Requested memory allocation size in bytes.
     - e.g. `cudaMalloc(&d_A, DSIZE*sizeof(float));`: This allocates memory for `DSIZE`(=4096) floats on the device for A matrix.
     - Return Value: Returns a `cudaError_t` value that can be checked to ensure the allocation was successful. Common errors include `cudaErrorMemoryAllocation` if the GPU does not have enough memory to fulfill the request.
       
   - **`cudaMemcpy`**: Copies data (`count` bytes) between host and device memory and vice versa, depending on the direction specified by the `kind` parameter.
     - **Syntax**: `cudaMemcpy(void* dst, const void* src, size_t count, cudaMemcpyKind kind);`
       - `dst`: Destination memory address (can be either device or host memory).
       - `src`: Source memory address (can be either device or host memory).
       - `count`: Number of bytes to copy.
       - `kind`: Type of transfer, specified by one of the `cudaMemcpyKind` enum values - `cudaMemcpyHostToDevice`, `cudaMemcpyDeviceToHost`, `cudaMemcpyDeviceToDevice`, or `cudaMemcpyHostToHost`. This specifies the direction of the copy.
     - e.g. `cudaMemcpy(d_A, h_A, DSIZE*sizeof(float), cudaMemcpyHostToDevice);`: This copies `DSIZE`(=4096) floats from host memory `h_A` to device memory `d_A`.
     - e.g. `cudaMemcpy(h_C, d_C, DSIZE*sizeof(float), cudaMemcpyDeviceToHost);`: This copies `DSIZE`(=4096) floats from device memory `d_A` to host memory `h_A`.
     - Return Value: Returns a `cudaError_t` value indicating success or failure of the copy. Common errors include `cudaErrorInvalidValue`, `cudaErrorInvalidDevicePointer`, and `cudaErrorInvalidMemcpyDirection`.

### Output
The output when complete would looked like this:
```
A[0] = 0.001251
B[0] = 0.563585
C[0] = 0.564837
```


## **3. Matrix Multiply (Naive)**

This code performs naive matrix multiplication on GPU between 2 square matrices of size `DSIZE x DSIZE`. This exercise introduced 2D threadblock/grid indexing. This code included built-in error checking, so a correct result is indicated by the program on host. Here’s an explanation of the key parts of this CUDA matrix multiplication program:

### Insights

1. **Grid and Block Dimensions**:
   - The kernel launch utilizes `dim3` to define the block and grid dimensions:
     - `dim3 block(block_size, block_size);` sets both dimensions of the block to `block_size`, allowing for a 2D configuration.
     - `dim3 grid((DSIZE + block.x - 1) / block.x, (DSIZE + block.y - 1) / block.y);` calculates the number of blocks needed for both dimensions by considering any remaining elements that do not completely fill a block into a new block. This ensures all elements are covered, even if the total does not evenly divide by `block_size`.

2. **CUDA Kernel `mmul`**:
   - The `mmul` kernel performs the matrix multiplication, where each thread computes an element in matrix C by multiplying a row of A and a column of B.
   - Matrix Bounds Check:
      - Inside the kernel, the matrix bounds check `if ((idx < ds) && (idy < ds))` ensures that the threads do not access memory outside the allocated range for matrices A, B, and C.
      - This is critical to avoid segmentation faults or accessing unallocated memory.
   - Accumulating the Dot Product:
      - The loop `for (int i = 0; i < ds; i++)` computes the dot product of the row from matrix A and the column from matrix B. Each thread computes one entry of the resulting matrix C.
      - The dot product is accumulated in the `temp` variable, which is subsequently stored in `C[idy * ds + idx]`.
3. **Performance Measurement**:
   - `clock_t` and `clock()` are used to measure the time taken for 1) initialization (host memory allocation and data setup) and 2) kernel execution (including data transfers and computation). This can help in analyzing the efficiency of the GPU computation relative to other implementations.
   - Timing calculations convert the clock ticks into seconds by dividing by `CLOCKS_PER_SEC`, giving insight into the performance of the host initialization and GPU execution phases.

4. **Error Handling and Result Verification**:
   - After the kernel execution and data transfer back to the host, the program checks for errors using the `cudaCheckErrors` macro. This step ensures that any issues in the kernel execution or memory operations are caught early.
   - The final verification loop checks if each element in the result matrix `h_C` matches the expected value, calculated as `A_val * B_val * DSIZE`. This provides a simple but effective way to confirm that the matrix multiplication was performed correctly.


### Output
Upon successful execution, the output confirms both the successful computation and match verification, indicating that the matrices were multiplied correctly. If there were mismatches in the verification process, an error message would indicate the index of the mismatch and the expected vs. actual value.
```
Init took 0.115000 seconds.  Begin compute
Done. Compute took 1.087000 seconds
Success!
```
