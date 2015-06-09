# jELC
jELC is a Java library that implements elastic linear classifiers for time series classification. Elastic linear classifiers extend linear classifiers to time series under dynamic time warping. This library currently supports the following algorithms:

* elastic perceptron 
* elastic margin perceptron
* elastic logistic regression
* elastic linear support vector machine.

jELC uses the one-against-all method for multi-class classification problems.
Additional features are

* L1 and L2 regularization
* cross-validation and leave-one-out validation
* automated parameter selection
* supports experiments on UCR time series datasets.

Requirements: 

* Java 7 and higher
* no dependencies to other libraries


## Table of Contents

- Quick Start
- Data Format
- Datasets
- Classifier Options
- Usage of ELC
    - Command Line Usage
    - Examples 
- Usage of UCR
    - Command Line Usage
    - Examples
- Usage of API
- Reference
- Contact


## Quick Start

1. For application and development integrate *jELC.jar* into an IDE (e.g. Eclipse). 

2. For quick application using the command line, specify options and call 

    ```java -jar elc.jar options```

3. For experiments using UCR datasets, specify options and call

    ```java -jar ucr.jar options```
       
See **Classifier Options** and **Command Line Usage** for a detailed description.
 
 
## Data Format

A data file contains one or more labeled time series. Each line of a data file contains a class label y and a time series (x_1, ..., x_n) in the following format:

    y x_1 x_2 ... x_n

The class label y must be an integer. The elements x_i of the time series are real numbers. All numbers are separated by space.

If labels are not available, for example when predicting the class of unseen time series, include arbitrary integer values as labels. 


## Datasets

The folder *./data/* contains the following datasets from the UCR time series datasets provided by E. Keogh:

* CBF
* Coffee
* Beef

Each dataset consists of a directory containing a training and a test file dataset. When using these datasets for publication, please follow the rules as described in section **Reference**.


## Classifier Options 

CLF_OPTIONS are classifier options for setting the experimental protocol and for specifying elastic linear classifiers. These options are valid for *elc.jar* and *ucr.jar*.

**CLF_OPTIONS:**
```
-S int    : Type of solver (default S = 0)
            0 -- elastic perceptron 
            1 -- elastic margin perceptron 
            2 -- elastic logistic regression
            4 -- elastic linear SVM 
-l double : Learning rate l > 0 (default l = 0.3)
-e int    : Elasticitiy e > 0 (default e = 1)
-R int    : type of regularization (default R = 0)
            0 -- no regularization 
            1 -- L1 regularization 
            2 -- L2 regularization 
-r double : Regularization parameter r >= 0 (default r = 0.1)
            The -r option is only considered if R > 0.
-m double : Margin m >= 0 for margin perceptron (default m = 0.1)
            Only considered if S = 2 (elastic margin perceptron).
-x int    : Cross validation x >= 0 (default x = 0)
            0 -- hold out validation (train-test split)
            1 -- leave-one-out validation
            k -- k-fold cross-validation when k > 1
            Leave-one-out and cross-validation are performed on the train set.
-v double : Splits validation set of size (1-v) from train set (default v = 0)
            If 0 < v < 1, a subset of size (1-v)*|train| is sampled from the
            train set for validation.
-T int    : Maximum number of epochs T > 0 (default T = 5000) 
-t int    : Maximum number of stable epochs t > 0 (default t = 200)
            Learning terminates after t epochs without improvement.     
-p int    : Toggles parameter selection (default p = 0)
            0 -- no parameter selection 
            1 -- parameter selection 
-o int    : Output mode(default o = 1)
            0 -- quiet mode 
            1 -- results
            2 -- fitting
            3 -- parameter selection
            4 -- solver progress info by dots
            5 -- solver detailed progress info
```

## Usage of elc.jar

### Command Line Usage

**USAGE:**

    ```java -jar ELC -train train_file [-test test_file] [-Z {0|1}] [clf_opts]```

**OPTIONS:**
```
-train <string> : File name of train set (mandatory)
-test <string>  : File name of test set (optional)
-Z              : Toggles z-normalization of each time series with mean zero 
                  and unit standard deviation (default Z = 0).
                  0 -- OFF: no normalization 
                  1 -- ON : performs normalization
clf_opts        : list of options as described in CLF_OPTIONS
```

Examples
--------

In the following examples, *train_file* and *test_file* refer to the paths of the training and test data, respectively. 

1. ```java -jar elc.jar -train train_file -test test_file```
   
   Uses train_file to fit elastic perceptron. Estimates predictive accuracy on test_file. 

2. ```java -jar elc.jar -train train_file -test test_file -S 2```
   
   Uses train_file to fit elastic logistic regression. Estimates predictive accuracy on test_file. 

3. ```java -jar elc.jar -train train_file -test test_file -S 2 -l 0.1```

   Uses train_file to fit elastic logistic regression with initial learning rate l = 0.1. Estimates predictive accuracy on   test_file.  

4. ```java -jar elc.jar -train train_file -test test_file -S 2 -l 0.1 -R 1 -r 0.05```

   Uses train_file to fit elastic  L1-regularized logistic regression with regularization parameter r = 0.05 and initial learning rate l = 0.1. Estimates predictive accuracy on test_file.  

5. ```java -jar elc.jar -train train_file -test test_file -S 3 -l 0.1 -m 1 -R 2 -r 0.2```

   Uses train_file to fit elastic linear SVM with initial learning rate l = 0.1 and L2-regularization parameter r  = 0.2. Estimates predictive accuracy on test_file.   

6. ```java -jar elc.jar -train train_file -x 10```

   Applies 10-fold cross-validation on train_file using an elastic perceptron. 

7. ```java -jar elc.jar -train train_file -x 1```

   Applies leave-one-out validation on train_file using an elastic perceptron. 

8. ```java -jar elc.jar -train train_file -test test_file -S 0 -p 1```

   Performs parameter selection on train_file. Since option -R is not set and -m is not valid for elastic perceptron, parameters for -e and -l are tuned. Uses train_file to fit elastic perceptron with best parameters found. Estimates predictive accuracy on test_file. 

9. ```java -jar elc.jar -train train_file -test test_file -S 2 -R 1 -r 0.1 -p 1```

   Performs parameter selection on train_file for elastic margin perceptron. Tunes parameters -e, -l, -m, and -r. Uses train_file to fit elastic margin perceptron with best parameters found. Estimates predictive accuracy on test_file.   


## Usage of ucr.jar

jELC supports experiments on the [UCR time series datasets](http://www.cs.ucr.edu/~eamonn/time_series_data/). The UCR time series datasets are a widely used benchmark for classification and clustering of time series. 

Each UCR dataset consists of a train and test set. The name convention for a dataset with name *Foo* is 

- *Foo_TRAIN* for the training set
- *Foo_TEST* for the test set

Training and test file must be both stored in a directory with name *Foo*. 


Command Line Usage
------------------

A single experiment consists of model selection and model assessment. Different 
protocols are possible:

    - hold-out validation
    - cross-validation
    - leave-one-out validation

Each protocol optionally admits automated parameter selection.

Command Line Usage
    java -jar ucr -dir path -ucr data [-N num] [-K num] [clf_opts]
    
OPTIONS:
-dir string : Path to directory containing UCR datasets.
-ucr string : Name of time series dataset. 
-Z int      : Toggles z-normalization of each time series with mean zero and 
              unit standard deviation (default Z = 0).
              0 -- OFF: no normalization 
              1 -- ON : normalization
-N <int>    : Number of trials of an experiment (default N = 1).
-K <double> : Sets experimental protocol (default K = 0). 
              Let X be the union of the train and test set.
              -- K <= 0    : hold-out validation using original train & test 
                             split as provided by UCR.
              -- 0 < K < 1 : hold out validation using modified train & test 
                             split. Splits union X to obtain a new train & test
                             set, where K*|X| is the size of the new train set.
              -- K = 1     : Leave-one-out validation on the union X.
              -- K > 1     : K-fold cross-validation on the union X. K is 
                             rounded to the next integer. 

clf_opts    : list of options as described in CLF_OPTIONS

The -x option for setting the number of cross-validation folds is disabled. 
Use option -K for setting the experimental protocol. The difference between both
options is that, -x > 1 performs cross-validation on the original training set 
only, whereas -K > 0 first merges training and test set and then performs the 
experimental protocol as described above. 


Examples
--------

> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 0 -S 0 -l 0.3

Uses Coffee_TRAIN to fit elastic perceptron with initial learning rate l = 0.3. 
Estimates predictive accuracy using Coffee_TEST. Experiment is repeated N = 10
times. 


> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 0 -S 0 -l 0.3 -p 1

Uses Coffee_TRAIN to fit elastic perceptron with initial learning rate l = 0.3. 
Estimates predictive accuracy using Coffee_TEST. Experiment is repeated N = 10
times. 


> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 0.75 -S 0 -l 0.3

Merges Coffee_TRAIN and Coffee_TEST to a single dataset X. Splits X into a new
train and test set. The size of the train set is K*|X| = 0.75*|X|. Uses new
train set to fit elastic perceptron with initial learning rate l = 0.3. 
Estimates predictive accuracy using new test set. Experiment is repeated N = 10
times. 


> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 1 -S 0 -l 0.3

Merges Coffee_TRAIN and Coffee_TEST to a single dataset X. Applies leave-one-out 
validation on X using elastic perceptron with initial learning rate l = 0.3. 
Experiment is repeated N = 10 times. 


> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 10 -S 0 -l 0.3

Merges Coffee_TRAIN and Coffee_TEST to a single dataset X. Applies K-fold cross-
validation on X using elastic perceptron with initial learning rate l = 0.3.
Experiment is repeated N = 10 times. 


> java -jar ucr.jar -dir path -ucr Coffee -N 10 -K 10 -S 0 -l 0.3 -p 1

Merges Coffee_TRAIN and Coffee_TEST to a single dataset X. Applies K-fold cross-
validation on X using elastic perceptron with initial learning rate l = 0.3. 
Performs parameter selection on each train set train_k, where 1 <= k <= K. 
Tunes parameters -e and -l. Uses train set train_k to fit elastic perceptron 
with best parameters found. Estimates predictive accuracy on k-th test set.
Experiment is repeated N = 10 times.



Usage of API
============

Integrate jELC.zip into an IDE (e.g. Eclipse). Class apps.Tutorial shows 
examples of how to use the library. See API documentation. 

For experiments on UCR datasets, set the following variables in class apps.UCR:

    String path;    corresponds to option -dir 
    String ucr;     corresponds to option -ucr
    int N = 1;      corresponds to option -N
    int Z;          corresponds to option -Z
    double K;       corresponds to option -K
    String opts;    corresponds to list of options clf_options


References
==========

If you find jELC helpful, please cite it as

    B. Jain, Generalized Gradient Learning on Time Series. Machine Learning, 
    2015. Software available at http:xxx

If you use any of the UCR time series datasets provided with this release, 
please consult the website 

    http://www.cs.ucr.edu/~eamonn/time_series_data/
	
and consult the terms of use. Please cite the UCR datasets as 

    Keogh, E., Zhu, Q., Hu, B., Hao. Y.,  Xi, X., Wei, L. & Ratanamahatana, C.A. 
    (2011). The UCR Time Series Classification/Clustering Homepage: 
    www.cs.ucr.edu/~eamonn/time_series_data/



Contact
=======

For any questions and comments, please email brijnesh.jain@gmail.com


