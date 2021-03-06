# KlustaKwik version 1.5

KlustaKwik is a program for unsupervised classification of multidimensional
continuous data. It arose from a specific need - automatic sorting of neuronal
action potential waveforms (see KD Harris et al, Journal of Neurophysiology
84:401-414,2000), but works for any type of data.  We needed a program that
would:

1) Fit a mixture of Gaussians with unconstrained covariance matrices
2) Automatically choose the number of mixture components
3) Be robust against noise
4) Reduce the problem of local minima
5) Run fast on large data sets (up to 100000 points, 48 dimensions)

Speed in particular was essential.  KlustaKwik is based on the CEM algorithm of
Celeux and Govaert (which is faster than the standard EM algorithm), and also
uses several tricks to improve execution speed while maintaining good
performance.  On our data, it runs at least 10 times faster than Autoclass.


## Cluster splitting and deletion

The main improvement in version 1.5 is a cluster splitting feature.  KlustaKwik
allows for a variable number of clusters to be fit, penalized by AIC. The
program periodically checks if splitting any cluster would improve the overall
score.  It also checks to see if deleting any cluster and reallocating its
points would improve overall score.  The splitting and deletion features allow
the program to often escape from local minima, reducing sensitivity to the
initial number of clusters, and reducing the total number of starts needed for
a data set.


## Compilation

The program is written in C++.  To compile under unix, extract all files to a
single directory and type make.  That should be all you need to do.  If it
doesn't work, change the makefile to replace g++ with the name of your C++
compiler.

To check it compiled properly type `KlustaKwik test 1 -MinClusters 2` to run
the program on the supplied test file.

## Usage

The program takes a "feature file" as input, and produces two output files, the
"cluster file", and a log file.  The file formats and conventions may seem
slightly strange.  This is for historical reasons.  If you want to change the
code, go ahead, this is open source software.

The feature file should have a name like `FILE.fet.n`, where `FILE` is any string,
and `n` is a number.  The program is invoked by running `KlustaKwik FILE n`, and
will create a cluster file `FILE.clu.n` and a log file `FILE.klg.n`.  The number `n`
doesn't serve any purpose other than to let you have several files with the same
file base.

The first line of the feature file should be the number of input dimensions. 
The following lines are the data, with each line being one data instance,
consisting of a list of numbers separated by spaces.  An example `file test.fet.1`
is provided.

The first line of the cluster file will be the number of classes that the
program chose.  The following lines will be the classes asigned to the data
points.  Class 1 is a "noise cluster" modelled by a uniform distribution, which
should contain outliers, if there are any.


## Parameters

It is possible to pass the program parameters by running `KlustaKwik FILE n
params` etc.  All parameters have default values. Here are the parameters you can
use:

`-help`
Prints a short message and then the default parameter values.

`-MinClusters n   (default 20)`
The random intial assignment will have no less than `n` clusters.  The final
number may be different, since clusters can be split or deleted during the
course of the algorithm

`-MaxClusters n   (default 30)`
The random intial assignment will have no more than `n` clusters. 

`-nStarts n       (default 1)`
The algorithm will be started `n` times for each inital cluster count between
MinClusters and MaxClusters.

`-SplitEvery n    (default 50)`
Test to see if any clusters should be split every `n` steps. `0` means don't split.

`-MaxPossibleClusters n   (default 100)`
Cluster splitting can produce no more than `n` clusters.

`-RandomSeed n    (default 1)`
Specifies a seed `n` for the random number generator

`-UseFeatures STRING   (default 11111111111100001)`
Specifies a subset of the input features to use.  STRING should consist of `1`s
and `0`s with a `1` indicating to use the feature and a `0` to leave it out.  NB The
default value for this parameter is `11111111111100001` (because this is what we
use in the lab) - so if you have more than 12 dimensions you will need to change
it.

`-StartCluFile STRING   (default "")`
Treats the specified cluster file as a "gold standard".  If it can't find a
better cluster assignment, it will output this.

`-DistThresh d    (default 6.907755)`
Time-saving paramter.  If a point has log likelihood more than `d` worse for a
given class than for the best class, the log likelihood for that class is not
recalculated.  This saves an awful lot of time.

`-FullStepEvery n (default 10)`
All log-likelihoods are recalculated every `n` steps (see `DistThresh`)

`-ChangedThresh f (default 0.05)`
All log-likelihoods are recalculated if the fraction of instances changing class
exeeds `f` (see `DistThresh`)

`-MaxIter n       (default 500)`
Don't try more than `n` iterations from any starting point.

`-Log             (default 1)`
Produces .klg log file (default is yes, to switch off do -Log 0)

`-Screen          (default 1)`
Produces parameters and progress information on the console. Set to `0` to suppress 
output in batches.

`-Debug           (default 0)`
Miscellaneous debugging information (not recommended)

`-DistDump        (default 0)`
Outputs a ridiculous amount of debugging information (definately not recommended).


## Contact Information

This program is copyright Ken Harris (harris@axon.rutgers.edu), 2000-2002. It
is distributed under the GNU General Public License (www.gnu.org).  If you make
any changes or improvements, please let me know.
