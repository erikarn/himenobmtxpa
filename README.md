This is a benchmark that showed up as part of the phoronix test suite.
It shows a dramatic difference in behaviour between Linux and FreeBSD/DragonflyBSD
(and likely other BSDs.)

It turns out that most of the difference is due to memory layout by the
allocator.  The matrices are large, and jemalloc page aligns these.
So there's significant cache line aliasing effects.

The 'master' branch is the original benchmark, fetched from:

http://leaf.dragonflybsd.org/~alexh/himenobmtxpa.c

The 'local/freebsd' branch is my local version that has an 'offset'
parameter at the command line.  You can use this to check the
differences in performance due to cache line aliasing.

An example:

CPU: Intel(R) Xeon(R) CPU E7-L8867  @ 2.13GHz (2128.05-MHz K8-class CPU)

```
adrian@:~ % cpuset -l 79 ./himenobmtxpa L 127
mimax = 256 mjmax = 256 mkmax = 512
imax = 255 jmax = 255 kmax =511
Start rehearsal measurement process.
Measure the performance in 3 times.

MFLOPS: 1056.411870 time(s): 3.145761 4.882812e-04

Now, start the actual measurement process.
The loop will be excuted in 57 times
This will take about one minute.
Wait for a while

Loop executed for 57 times
Gosa : 4.882812e-04 
MFLOPS measured : 1056.496858  cpu : 59.764651
Score based on Pentium III 600MHz using Fortran 77: 12.884108

adrian@:~ % cpuset -l 79 ./himenobmtxpa L 0
mimax = 256 mjmax = 256 mkmax = 512
imax = 255 jmax = 255 kmax =511
Start rehearsal measurement process.
Measure the performance in 3 times.

MFLOPS: 673.107097 time(s): 4.937133 4.882812e-04

Now, start the actual measurement process.
The loop will be excuted in 36 times
This will take about one minute.
Wait for a while

Loop executed for 36 times
Gosa : 4.882812e-04 
MFLOPS measured : 673.042446   cpu : 59.251287
Score based on Pentium III 600MHz using Fortran 77: 8.207835
```
