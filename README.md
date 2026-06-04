# Dynamic Memory Allocator

Custom heap memory allocator implemented in C supporting dynamic memory management through `malloc`, `free`, and `realloc`.

## Overview

Designed and implemented a user-level memory allocator to manage heap memory allocation and deallocation while maintaining correctness and efficient memory usage.

## Features

* Custom implementation of `malloc`, `free`, and `realloc`
* Segregated free-list based allocation strategy
* Block splitting and coalescing for heap management
* Memory alignment and block metadata management
* Trace-driven evaluation for allocator validation

## Technical Highlights

* Heap Organization
* Dynamic Memory Allocation
* Free List Management
* Fragmentation Reduction
* Systems Programming in C

## Build

```bash
make
```

## Run

```bash
./mdriver
```

Verbose mode:

```bash
./mdriver -V
```

## Key Learnings

* Memory allocator design and implementation
* Heap management and block organization
* Performance tradeoffs in dynamic memory allocation
* Debugging and validation of low-level systems software
