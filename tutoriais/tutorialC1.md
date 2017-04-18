For medium sized data sets which are too-big for in-memory processing but too-small-for-distributed-computing files, following R Packages come in handy.


```{r}
install.packages("bigmemory")
library(bigmemory)
```

bigmemory is part of the “big” family which consists of several packages that perform analysis on large data sets. bigmemory uses several matrix objects but we will only focus on big.matrix.

big.matrix is a R object that uses a pointer to a C++ data structure. The location of the pointer to the C++ matrix can be saved to the disk or RAM and shared with other users in different sessions.

By loading the pointer object, users can access the data set without reading the entire set into R.

The following sample code will give a better understanding of how to use bigmemory:

school.matrix <- read.big.matrix(
    "./numeric_matrix_SAT__College_Board__2010_School_Level_Results.csv", 
    type ="integer", header = TRUE, backingfile = "school.bin", 
    descriptorfile ="school.desc", extraCols =NULL) 
    
str(school.matrix)

ff

ff is another package dealing with large data sets similar to bigmemory. It uses a pointer as well but to a flat binary file stored in the disk, and it can be shared across different sessions.
One advantage ff has over bigmemory is that it supports multiple data class types in the data set unlike bigmemory.

```{r}
library(ff)
                                 
# creating the file
school.ff <- read.csv.ffdf(file="/Users/sundar/dev/mixed_matrix_SAT__College_Board__2010_School_Level_Results.csv")

#creates a ffdf object 
class(school.ff)
```



We are here because we love R. Despite our enthusiasm, R has two
major limitations, and some people may have a longer list.
1
Regardless of the number of cores on your CPU, R will only
use 1 on a default build.
2
R reads data into memory by default.  Other packages (SAS
particularly) and programming languages can read data from
files on demand.
Easy to exhaust RAM by storing unnecessary data.
The OS and system architecture can only access
2
32
1024
2
= 4GB
of physical memory on a 32 bit system, but typically R will
throw an exception at 2GB


There are a couple of solutions
Build from source and use special build options for 64 bit and
a parallelization toolkit.

Rmpi
snowfall
foreach
multicore


A CPU is the main processing unit in a system.  Sometimes CPU
refers to the processor, sometimes it refers to a
core
on a
processor, it depends on the author.  Today, most systems have one
processor with between one and four cores.  Some have two
processors, each with one or more cores.

A
cluster
is a set of machines that are all interconnected and share
resources as if they were one giant computer.

The
master
is the system that controls the cluster, and a
slave
or
worker
is a machine that performs computations and responds to
the master’s requests


b
igmemory
Provides the 
core matrix
-
like 
support. 
Functions include: 
nrow,ncol,dim,tail,head,apply,big.matrix
,
read.big.matrix
,
mwhich
.

biganalytics
Provides routine analysis on big matrix. 
Functions include: 
s
um,range,mean,colsum,colrange,colmean
,
biglm.big.matrix,bigglm.big.matrix

bigtabulate
Adds table and split
-
like support for 
big.matrix
objects

bigalgebra
Provides linear algebra operations on R 
matrix
as well as 
big.matrix
.