# Automatic Vectorization

## Objectives:

* Utilize auto vectorization in the Intel Compiler.
* Implement code for matrix multiplication and generate an optimization report.
* Make code changes that are needed for effective vectorization.
* Collect and analyze empirical timing data.

## Description:

* Implement matrix multiplication and tune the code as needed so that it will vectorize fully (or as fully as possible).
* Determine the (theoretical) maximum speedup from vectorization based on the hardware. 
  * The lab machines have the Intel Core i5-5250U on board. 
  * This processor supports AVX2. 
  * Calculate the actual improvement by comparing the runtime of the un-vectorized executable to the vectorized executable.

Read the following four sections of the Intel C++ Compiler User and Reference Guide - [Automatic Vectorization](https://software.intel.com/en-us/cpp-compiler-developer-guide-and-reference-automatic-vectorization)

* Programming Guidelines for Vectorization
* Using Automatic Vectorization
* Vectorization and Loops
* Loop Constructs

Read about [data alignment](https://software.intel.com/en-us/articles/data-alignment-to-assist-vectorization).

You might consider the ijk forms and/or matrix transpose for better performance.

Write a C program to perform the multiplication of two matrices of type float. Assume square matrices so based on the following equation we can assume m and l are the same. 

![Matrix Math Graphic](https://github.com/csuchico-csci551/AutomaticVectorization/raw/master/eq21.gif "Matrix Math Graphic")


Your program must be implemented in one file named mm.c and it should prompt the user and read from standard input as follows...

```html
<flag>
<n> 
<A>
<B>
```
* flag is one of
  * R => Random generation of matrices
  * I => Input matrices
* n specifies the size - each matrix is to to be n x n
* A is an n x n matrix, input in row major order (only present if flag=I)
* B is an n x n matrix, input in row major order (only present if flag=I) 

Your program should calculate the product AB.

When generating random numbers for the matrices, be sure to seed the random number generator once using the current time. Generate floats in the range [-50 .. 50].

If the Input option was selected, the result should be printed to standard out following the timing information. Put a single space between matrix elements in a row, and a newline after each row in the output. Since I will only initilize matrices with whole numbers in Input mode, print each element of the matrix using %.0f

NO matrix should be printed when the Random option is selected!

Examples:

The following inputs say to randomly generate 4800x4800 matrices...

```
R 
4800
```

and will produce NO output but the timing information.

This form is used to enter the matrices...

```
I 
2 
1 3 
2 2
4 0 
5 1
```

and should produce output...

```
19 3
18 2
```

Use the Input mode to verify that your implementation is producing the correct result. Make sure you verify correctness of both the vectorized and non-vectorized code.

## More Details:

* create a makefile with two targets
  * mmvec - vectorized executable - compile flags: **-march=core-avx2 -O2 -qopt-report=5 -qopt-report-phase=vec**
  * mmnovec - non-vectorized executable - compile flags: **-march=core-avx2 -O2 -no-vec**
* there should only be one source file - vectorization is controlled with compiler options
* use the Input mode to test both the vectorized and non-vectorized versions and verify a correct result
* use the Random mode with n = 8192 for collecting run times
* to collect timing information
  * instrument the code with a single call to getrusage for RUSAGE_SELF, and print:
    * user CPU time 
      convert timeval seconds plus microseconds to seconds
      eg. 1 second plus 1 microsecond = 1.000001 seconds
    * system CPU time 
      convert timeval seconds plus microseconds to seconds
    * maximum resident set size
  * run each executable at least three times and report the sum of user and system time for all three executions, and the max resident set size
    * This sample size may need to be larger if you have significant variation between the executions
* create a report.pdf with the following
  * theoretical maximum speedup due to vectorization and your reasoning
  * table of actual times on three runs each for mmvec and mmnovec using n=8192, report user time, system time, and the sum
  * speedup (use minimum sum of user and system time) where speedup = mmnovec-time/mmvec-time
  * maximum resident set size
  * discussion of your implementation and results, compiler options used and why, and especially why you may not have achieved the theoretical max speedup 

## Additional Requirements:

* **do not use any OpenMP or RAJA pragmas**
* do some functional decomposition - do not code everything in main()
* vectorize whatever you can
* use the novector pragma on all loops not being vectorized for a cleaner report
* document your source 
* verify that your implementation produces a correct result, both the vectorized and non-vectorized versions
* your code must compile without errors or warnings - use -Wall -W -Werror in your makefile

## Turn-in Procedure
* create a **tar.gz** file with your mm.c, makefile, optimization report, and report,pdf (and nothing else)
* submit the compressed archive file in Tyson's Turnin System
* failure to follow turn-in directions will result in a grade deduction

## Evaluation

Grading breakdown
30 - correct multiplication result with both mmnovec and mmvec
20 - multiplication loop vectorized for AVX2
20 - runtimes reported for mmnovec and mmvec
30 - discussion - max speedup
