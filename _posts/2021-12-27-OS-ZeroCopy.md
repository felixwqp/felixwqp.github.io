---
layout: post
title: "[Operating System] Zero Copy"
description: ""
comments: true
keywords: ""
---


zero-copy:
when exchanging data 
1. within a user space process(between two threads)
2. between two/more process(producer-consumer problem)
3. data accessed/copied/moved inside kernel space or between user space process and kernel space portions of OS

**I/O related system calls**: when user space process has to execute system operations like reading/writing data from/to device(i.e. disk, NIC) through their high level SW interfaces. it has to perform one or moresyustems calls, which are then executed in kernel space

advantages:
1. less data copy: CPU can do other tasks
2. less context switch between user space and kernel space. 



```c++
while((n = read(diskfd, buf, BUF_SIZE)) > 0)
    write(sockfd, buf , n);
```
- read: 
  - 1. read() system call, context switch from user space -> kernel space
  - 2. hw -> kernel buffer: DMA
  - 3. kernel buffer -> user buffer: cpu copy: context switch from kernel space to user space
- write: 
  - 4. write() system call, context switch from kernel space -> user space
  - 5. user buffer -> socket buffer: cpu copy
  - 6. socket buffer -> nic: DMA; finish writing context switch from kernel space to user space.

##### basic prerequisite:
3.1. user space and kernel space:
virtual mem for each process: -> user space + kernel space
- user space: cannot access to kernel space, when user program needs kernel resouce, need to make system call, context switch: from user -> kernel -> user
- kernel space: process scheudling, mem allocation, HW resouce

3.2  context switch

the process of storing the state of a process or thread, so that it can be restored and resume execution at a later point, which allows multiple processes to share a single central processing unit (CPU).

in the Linux kernel, context switching involves switching registers, stack pointer (it's typical stack-pointer register), program counter, flushing the translation lookaside buffer (TLB) and loading the page table of the next process to run 

3.3. virtual memory: 
virtual address from different space can point to the same physical memory -> reduce data copy

3.4 DMA: 
![a](/assets/images/dma.png)
1. user read() system call for IO, user blocking wait for return
2. CPU get the system call -> request DMA to IO request
3. by IO request, DMA request to disk
4. DMA copy data from disk to kernel buffer
5. DMA -> cpu: finished
6. cpu copy data from kernel buffer to user buffer
7. user has data unblocking


#### Realization:
three ways: 
1. mmap + write
2. sendfile
3. sendfile with DMA

in this page, only look at how mmap + write works together

```c
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```
mmap can map the buffer between user space and buffer in kernel space; 
![](/assets/images/mmap_write.png)

