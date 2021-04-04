---
layout: post
title: GPU Hackathon training
subtitle: GPU-CUDA Hackathon with Julich Supercomputers
#cover-img: /assets/img/path.jpg
#thumbnail-img: /assets/img/thumb.png
#share-img: /assets/img/path.jpg
tags: [GPU, CUDA, hacking]
---

# Some tips

## Generate the Nsight report using :
1.	`nsys profile application.o`
2.	open the report file in nsight systems on the local machine to visualize the report.

## CUDA fortran
Compile instructions::   CUDA fortran is based on the PGI compiler
There are two ways in  which CUDA can be used in the Fortran code. 
1. using cudafortran directly
2. using C bindings

## First Way using only cublas API
`pgfortran -Mcudalib=cublas -Mcuda test01_dgemm.f90 -o test_dgemm`

Program::
```fortran
        program cublas_dgemm_test
        use cublas
        implicit none
        real*8, allocatable         :: a(:,:),b(:,:),c(:,:)
        real*8, device, allocatable :: a_d(:,:),b_d(:,:),c_d(:,:)
        integer                     :: n=20480, i
        real*8                      :: alpha=1.0d0, beta=2.0d0

        allocate(a(n,n), b(n,n), c(n,n), a_d(n,n), b_d(n,n), c_d(n,n))

        a = 0.0d0; forall(i = 1:n) a(i,i) = 1.0 
        a_d = a

        b = 0.0d0; forall(i = 1:n) b(i,i) = 1.0
        b_d = b

        c = 0.0d0; forall(i = 1:n) c(i,i) = 1.0
        c_d = c


        call dgemm('N','N',n,n,n,alpha,a_d,n,b_d,n,beta,c_d,n) 
        c=c_d
        write(*,*) "done; printing first element ", c(1,1) 
        
        deallocate (a,b,c,a_d,b_d,c_d)
        
        end program cublas_dgemm_test

```

## Submit script for Juwels booster system
```bash
#!/bin/bash -x
#SBATCH --account=training2105
#SBATCH --partition=develbooster
#SBATCH --reservation=gpu-hack-2021-day3
#SBATCH --gres=gpu:1
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --output=test_dgemm_ravindra.out.%j
#SBATCH --error=test_dgemm_ravindra.err.%j
#SBATCH --time=00:20:00


module load CUDA/11.0 NVHPC/20.11-GCC-9.3.0 
./test_dgemm

```


## Non-thunking (user defined data allocation, movement, and deallocation)

```fortran
        program example_sgemm
        real, dimension(:,:),allocatable:: A(:,:),B(:,:),C(:,:)
        integer*8:: devPtrA, devPtrB, devPtrC
        integer:: n=20480, size_of_real=16
        allocate (A(n,n),B(n,n),C(n,n))

        call cublas_Alloc(n*n,size_of_real, devPtrA)
        call cublas_Alloc(n*n,size_of_real, devPtrB)
        call cublas_Alloc(n*n,size_of_real, devPtrC)

        ! Initialize A, B and C
        A = 0.0d0; forall(i = 1:n) A(i,i) = 1.0 
        B = 0.0d0; forall(i = 1:n) B(i,i) = 1.0
        C = 0.0d0; forall(i = 1:n) C(i,i) = 1.0


        ! Copy data to GPU
        call cublas_Set_Matrix(n,n,size_of_real,A,n,devPtrA,n)
        call cublas_Set_Matrix(n,n,size_of_real,B,n,devPtrB,n)
        call cublas_Set_Matrix(n,n,size_of_real,C,n,devPtrC,n)

        ! Call SGEMM in CUBLAS library
        call cublas_SGEMM('n','n', n,n,n,1.,devPtrA,n,devPtrB,n,1.,devPtrC,n)

        ! Copy data from GPU
        call cublas_Get_Matrix(n,n,size_of_real,devPtrC,n,C,n)

        print *,c(1,1)

        call cublas_Free(devPtrA)
        call cublas_Free(devPtrB)
        call cublas_Free(devPtrC)

        end program example_sgemm
```