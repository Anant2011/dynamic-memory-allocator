# Dynamic Memory Allocator

A high-performance custom memory allocator implemented in **C** as part of the  project.

This allocator recreates the behavior of standard dynamic memory management routines (`malloc`, `free`, and `realloc`) while improving allocation efficiency through **Explicit Segregated Free Lists**, **boundary tag coalescing**, **optimized block placement**, and **heap consistency verification**.

> **Note:** Core allocator implementation is contained in `mm.c`.
> Supporting files (`mm.h`, `memlib.c`, `memlib.h`, `Makefile`)
> are included only for building and testing.
---

# Table of Contents

- Overview
- Features
- Memory Layout
- Architecture
- Allocation Workflow
- APIs
- Core Optimizations
- Heap Checker
- Project Structure
- Build & Run
- Example Usage
- Results
- Concepts Learned
- Future Improvements

---

# Overview

Dynamic memory allocation is one of the fundamental services provided by an operating system and runtime environment.

This project implements a complete heap allocator capable of:

- Allocating memory dynamically
- Reusing freed memory efficiently
- Reducing fragmentation
- Supporting resizing operations
- Maintaining heap correctness

Instead of relying on libc allocation functions, memory is managed manually using metadata stored directly inside the heap.

---

# Features

## Explicit Segregated Free Lists

The allocator maintains multiple free lists where each list stores blocks of similar sizes.

```text
Segregated Lists

2^0
2^1
2^2
2^3
...
2^15
2^16+
```

### Advantages

- Faster allocation
- Reduced search overhead
- Better memory utilization
- Lower fragmentation

---

## Explicit Free Block Structure

Free blocks maintain links for traversal.

```text
FREE BLOCK

+--------------------+
| Header             |
+--------------------+
| Previous Pointer   |
+--------------------+
| Next Pointer       |
+--------------------+
| Footer             |
+--------------------+
```

Allocated blocks:

```text
ALLOCATED BLOCK

+----------------+
| Header         |
+----------------+
| Payload        |
|      ...       |
+----------------+
| Footer         |
+----------------+
```

---

# Memory Layout

Heap initialization creates:

```text
Heap

Padding
↓

Prologue Block
↓

Regular Blocks

↓

Epilogue Block
```

Block metadata:

```text
--------------------------------
| Block Size | Allocated Bit |
--------------------------------
```

Lower bits:

```text
bit 0 → allocation flag

bit 1–2 → unused
```

---

# Architecture

The allocator is composed of the following modules.

## Heap Initialization

Responsible for:

- Creating segregated free lists
- Building prologue block
- Building epilogue block
- Extending heap initially

Function:

```c
int mm_init(void)
```

---

## Allocation Engine

Responsible for:

- Alignment
- Searching free lists
- Splitting blocks
- Extending heap

Function:

```c
void* mm_malloc(size_t size)
```

---

## Free Engine

Responsible for:

- Marking blocks free
- Coalescing adjacent blocks
- Reinserting blocks

Function:

```c
void mm_free(void* ptr)
```

---

## Reallocation Engine

Responsible for:

- Expanding blocks in place
- Allocating new blocks
- Copying contents

Function:

```c
void* mm_realloc(void* ptr,size_t size)
```

---

# Allocation Workflow

## malloc()

```text
User Request
      ↓
Adjust Size
      ↓
Choose Segregated List
      ↓
Find Fit
      ↓
Split Block
      ↓
Allocate
      ↓
Return Payload
```

---

## free()

```text
Mark Free
     ↓
Merge Neighbors
     ↓
Insert Into List
```

---

## realloc()

```text
Request Resize
       ↓
Can Expand?
   ↙        ↘
 YES         NO
 ↓            ↓
Resize     Allocate
             ↓
          Copy Data
             ↓
          Free Old
```

---

# APIs

## Initialize Heap

```c
int mm_init(void);
```

Initializes:

- Segregated lists
- Prologue
- Epilogue

---

## Allocate Memory

```c
void* mm_malloc(size_t size);
```

Returns:

```text
Pointer to allocated payload
```

---

## Free Memory

```c
void mm_free(void* ptr);
```

Returns block to allocator.

---

## Resize Memory

```c
void* mm_realloc(void* ptr,size_t size);
```

Attempts in-place growth before moving.

---

## Heap Validation

```c
void mm_checkheap(int verbose,int line);
```

Performs consistency checks.

---

# Core Optimizations

## 1. Alignment

Every allocation is aligned.

```text
Alignment = 8 bytes
```

---

## 2. Block Splitting

Split only when:

```text
Remaining ≥ Minimum Block Size
```

Prevents excessive fragmentation.

---

## 3. Boundary Tag Coalescing

Supports:

```text
Case 1:
[A][FREE][A]

Case 2:
[A][FREE][FREE]

Case 3:
[FREE][FREE][A]

Case 4:
[FREE][FREE][FREE]
```

---

## 4. Optimized Placement

Large requests use custom placement logic to reduce:

- External fragmentation
- Heap growth

---

## 5. Realloc Optimization

Before allocating:

- Check adjacent free block
- Expand in place
- Avoid unnecessary copy

---

# Heap Checker

Debugging support included.

Enable:

```c
#define DEBUG
```

Checks:

✓ Alignment correctness

✓ Header/footer match

✓ Free list integrity

✓ Prologue correctness

✓ Epilogue correctness

Run:

```c
mm_checkheap(1,__LINE__);
```

---

# Project Structure

```text
dynamic-memory-allocator

├── mm.c
├── mm.h
├── memlib.c
├── memlib.h
├── mdriver.c
├── config.h
├── Makefile
└── README.md
```

---

# Build & Run

Compile:

```bash
make
```

Run:

```bash
./mdriver
```

Verbose mode:

```bash
./mdriver -V
```

Clean:

```bash
make clean
```

---

# Example Usage

```c
int* arr;

arr = mm_malloc(100*sizeof(int));

arr[0]=10;

arr = mm_realloc(
        arr,
        200*sizeof(int)
      );

mm_free(arr);
```

---

# Performance Goals

Target metrics:

| Metric | Goal |
|--------|------|
| Throughput | High |
| Utilization | High |
| Fragmentation | Low |
| Allocation Time | Fast |

---

# Concepts Learned

This project strengthened understanding of:

- Dynamic Memory Allocation
- Heap Organization
- Explicit Free Lists
- Segregated Lists
- Coalescing
- Fragmentation
- Systems Programming
- Performance Engineering

---



# References

- CS:APP (Computer Systems: A Programmer's Perspective)
- Memory Allocation Research Papers

---

# Author

**Anant Pratap Singh Chauhan**  
B.Tech Electronics & Communication Engineering  
Indian Institute of Technology Guwahati

---
