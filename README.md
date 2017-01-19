# ML Quantized classifier in GO#

A general purpose, high performance machine learning classifier. 

#### Basic Test results

| Prediction Type                          | Quantized Classifier | Quant  time ms | Tensorflow CNN | TF time ms |
| ---------------------------------------- | -------------------: | -------------: | -------------: | ---------: |
| Classify breast cancer                   |               96.10% |             60 |         94.81% |      7,080 |
| Predict death or survival of Titanic passengers |               80.90% |             80 |         77.53% |      5,520 |
| Predict Diabetes                         |               69.41% |             80 |         65.89% |      7,330 |
| Predict Liver disorder                   |               62.50% |             70 |         67.35% |      6,160 |

> > * Tests measures Precision at 100% recall. Recall forced to 100% by choosing highest Prob Class from answer as the class chosen.
> > * Tensorflow n_epoch = 30, batch=55.   Tensorflow CUDA = GeForce GTX 960M.  QuantProb is only using system CPU and uses no optimizer.
> > * Run includes both train and test stage. Timing from cygwin time command
> > * All source and data to duplicate test is in repository

Please send me data sets you would like to add  to the test.

**I Offer Consulting services see: http://BayesAnalytic.com/contact**

### Quantized Classifier ###
>The Quantized classifier uses a clever grouping mechanism to identify similar groups of items for each feature.  This mechanism is very fast, yields good classification accurcy and delivers unique data discovery capabilities.  The design was  inspired by  KNN, Bayesian, K-Means, SVM and Ensemble techniques.  
>
>The key benefit is accuracy and recall that rivals [Deep learning Convolutional Neural Networks](https://www.tensorflow.org/tutorials/deep_cnn/) while providing faster training and  classification and consuming less RAM.  It has no dependencies other than the GO compiler which makes deployment easy.  
>
>The quantized optimizer provides the unique ability to identify features that are most important for accurate classification.  It can identify the best grouping for each feature. For example: It can determine that dividing age in two groups between 0..50, 51..100 are important for some predictions while a much finer breakdown is more important for other predictions.   It can identify features that negatively contribute to classification accuracy.    These findings can be surfaced to the user who can use them to help guide their research.
>
>The quantized optimizer can identify important data grouping such as determining that  bit 1,19,37 in a particular genome slice are critical inputs when predicting a condition such as risk of early cancer while bits 2,17,32 contribute negatively when identifying the same condition.     It can identify specific clusters of data values that drive prediction such as a Man purchasing a plane ticket with one other traveler it may be important to predict whether that user is likely to purchase a vacation package.  The optimizer could identify that ticket history is only important when they have purchased more than 3 tickets in the last 3 years  AND more than 2 tickets during the last 2 years AND more than 1 in the last 9 months AND none during the last 5 months.    This kind of data discovery can be valuable to help marketing people and domain experts formulate marketing campaigns.  
>
>####What is a Quantized Classifier####
>The easiest way to understand the quantized classier is to compare it to others.   It draws a notion of similarity from KNN.   It statistical base probability similar to Bayesian engines.    It uses a multi-feature assembly strategy similar to weighted ensembles.   The optimizer draws on techniques from randomized genetic algorithms.
>
>In KNN we find similar records for a given feature by finding  those with the most similar value compared to the current record.   The classes of those similar records are used to classify the current reord.      This works but consumes  space and run-time because it requires the unique values for all the training data to be retained in a form that makes finding the most similar records for each feature fast.      The  Quantized approach looks at the range of data and attempts to group data of similar values based on numeric ranges.   
>>If all records in the training data sets have a feature with a value between 0.0 to 1.0 then a 10 bucket (quanta) system would be divided into 10 groups based on range of values found in all training records.   Group1=0.0 to 0.1,  Group2= 0.1 to 0.2,  Group3= 0.2 to 0.3.  All records that have a value that maps to the same group would be considered similar.  Records with a value between 0.1 to 0.2 would be similar in Group2.     Each unique grouping bucket is considered a quanta.     Each feature for each row is analyzed and assigned a quanta.  The quanta for each feature are a discrete set and do not influence the quanta for other features.  
>>
>>The system keeps  statistics for each class found for each quanta.  We may have 100 total training records that map to the quanta 1 (0.0 to 0.1).   70 of these records are tagged as class 1 while 30 records are tagged as class  0.   This is used to compute a base probability.   During a future classification request  a record that has a value that is between 0.0 and 0.1 has a 70% probability of belonging to class 1 and a 30% probability of belonging to class 0. 
>>
>>This process is repeated across all features.  The set of base probabilities are combined using ensemble techniques to produce a probability by class for each row classified.   In the simple version all features have the same weight and the same number of quanta.  In the optimized version the system is free to change the weight and number of quanta for each feature.  
>
>Quantizing the data allows a small memory foot print to deliver fast training and fast classification without the need to keep all the training records in memory. Retaining only the statistics allows very large training sets with moderate memory use.   The trade off is loosing KNN's ability to adjust the number of closest neighbors considered at runtime without retraining.  Memory use is so much smaller than KNN that we can afford to keep multiple models with different quanta sizes loaded and updated simultaneously. 
>
>Choosing the number of quanta is important for choosing the best prediction results.  The default of 10 works for many applications but we have found situations where 90 quanta works better and there are a few where 5 quanta work better.   In general a larger number of quanta delivers more precise results at reduced recall.  There are applications where reducing the number of quanta improved precision because it was able to reduce the influence of noisy training data.    The Optimizer is allowed to change the number of quanta by feature to improve prediction accuracy. 
>
>Whenever using statistical techniques outliers in the data can yeild a negative impact on classification accuracy.  In a quantized engine outlier values affect results because quanta ID are computed based on the absolute range of training data.  This can cause values in the center of the distribution to be forced into a smaller number of quanta which reduces the discrimination the majority data that also tends to be closer to the center of the distribution.   The Quantized classifier handels this by computing an effective range for the values in each Quanta.   The effective range is determined by removing 1.5% of the training values from the low and high end and then computing the range between min and max for the remaining records.  We use a clever mechanism for outlier removal that avoids the need to sort the values because we wanted to handle training sets larger than physical memory.    The quanta are indexed in a sparse matrix so outlier values still get their own quanta and can participate in classification but the effective range mechanism prevents outliers from negatively affecting precision for the majority dataset. 
>
####Tensor Flow Comparison####
>One goal of this project was to compare the classification and machine performance of the Quantized Classifier against Tensorflow when ran against the same data.    The repository includes TensorFlow Deep Learning implementation of classifiers. [readme](tlearn)  

>CNNClassify.py](tlearn/CNNClassify.py) Provides Python code that will read our [Machine learning CSV Files](data) and produce classification output without changing the code.  It provides a nice way to think about generalized use of Tensorflow.

###ASP (American Sign Language) Gesture classifier###
This engine started as a classifier designed to classify Static Gestures for VR with the idea we may be able to produce a useful tool for classifying  ASL using VR input devices.  That is still a primary focus but the core algorithms can be more broadly applied.

See **[Overview.pdf](docs/Overview.pdf)** in this repository for conceptual overview of
the approach when using this kind of classifier for gesture recognition.

This repository includes code written to test ideas for static gesture recognition. 

It also includes samples of the classifiers in python that cope
well with smaller training data sets and demonstrate using 
the Quantized classifier approach.  They also handle
massive training data sets with minimal memory.    
##Metadata##

* Version: 0.1
* License: [MIT](https://opensource.org/licenses/MIT) I reserve the right to change the license in the future but you will always be able to continue using the version you downloaded under the MIT version using the MIT license. 
* **We sell consulting services http://BayesAnalytic.com/contact**
* Dependencies: 

   - [GO Code](https://en.wikipedia.org/wiki/Go_(programming_language)) is
     cross platform and will run Linux.  This softwar was built using 
     version 1.7.3 windows/amd 64
   - Python code: Was tested with Python 3.5.2 64 bit
   - TensorFlow: Lots of crazy dependencies See: tlearn/tensflowReadme.docx 

## How to Use ##

* [Install GO](https://golang.org/doc/install)
* **setGoEvn.bat** - will set the GOHOME directory to current working directory  in a command prompt.  This is required for the GO compiler to find the  source code. 
  Tested on windows 10 but should be similar on linux.
* **[makeGO.bat](makeGO.bat)** - First install GO and ensure it has  been added to PATH.  Open a command line at  the base directory containing makeGO.bat and run it. It will build the executables based on GO that are needed to run  the tests. Tested on windows 10 but should be similar on linux.
* **[splitData.bat](splitData.bat)** - Creates sub .train.csv and  test.csv files for the files used in the classifier tests. Uses splitCSVFile.exe which is built by makeGo.  Run this before  attempting to run the classifier to ensure proper data is present.
* **go build src/classifyFiles.go**   builds executable classifyFiles from GO source.     This is done automatically by makeGO.bat but  replicated here to show how to do it manually    
* **classifyFiles data/breast-cancer-wisconsin.adj.data.train.csv   data/breast-cancer-wisconsin.adj.data.test.csv 10**  will run the GO based classifier built in GO using the first named file for training and the second named file for testing will print out results of how well classification matches actual source data class.   This is called automatically by classifyTestBCancer.bat.   Output will be written to stdout.
* **classifyFiles data/titanic.train.csv data/titanic.test.csv 6**  will run the GO based classifier against the two input files  this test attempts to predict mortality and will print out      quality of predictions from classifier compared to known  result.   This is called automatically by classifyTestTitanic.bat 
* **classifyTestBCancer.bat** - Runs classifyFiles on breast cancer data set.  Writes output to classifyTestBCanser.out.txt. 
* **classifyTestDiabetes.bat** - Runs classifyFiles on diabetes data set. Writes output to stdout
* **classifyTestLiverDisorder.bat** - Runs classifyFiles on Liver disorder data set.  Writes output to classifyTestLiverDisorder.out.txt
* **classifyTestTitanic.bat** - Runs classifyFiles on Titanic survial data set.  Writes output to classifyTestTitanic.out.txt

####For the Tensorflow tests###
* See [TensorFlow Demo][tlearn]
* [Install python](https://www.python.org/downloads/release/python-352/). We   tested 3.5.2 but should work with newer versions. Onlyneeded if you want to run TensorFlow or Python samples we supplied.
* [Install TensorFlow](https://www.tensorflow.org/get_started/os_setup), [TFLearn](http://tflearn.org/installation/) and     run their basic tests to ensure they    run correctly.  This may also require installing CUDA depending on  whether you want to use the GPU version of TensorFlow.  TFLearn requires      Python we tested ours with python 3.5.2.   Not needed if you only want      to run our GO based classier engines. 
####For Original Proof of theory samples####
* [Install python](https://www.python.org/downloads/release/python-352/). We  tested 3.5.2 but should work with newer versions.    Only needed if you want to run the Python samples we supplied.

* **python quant_filt.py** - Runs test on gesture classification data.   Shows how quantized concept can be used to implement    splay like search trees.  It acts something like a decision    tree and something like a multi layer CNN.     The more quant buckets used the more precise.  This is an    alternative to the probability  model and can provide superior results in some   instances.

* **python [quant_prob.py](quant_prob.py)** - Runs a test on  gesture classification data demonstrates quantized probability theory in smallest possible piece of python code.  A more   complete version is implemented in classify.go 
  ​       
## Basic Contents ##
Not all files are listed here. The intent is to help you find those files that are most likley to be helpful. when learning the sysem.

* **[todo.md](docs/todo.md)** - list of actions and enhancements roughly
    prioritized top down.

### GO Based Classifier ###

  src/classifyTest.go

  src/csvInfoTest.go

  src/splitCSVFile.go 

  src/qprob/classify.go

  src/classifyFiles.go

  src/qprob/csvInfo.go

  src/qprob/util.go

  

 

### Idea Test Sample Code ###
* **quant_filt.py**  - Machine learning Quantized filter classifier.  This system can provide  fast classification with moderate memory use and is easy to see how likely the match is to be accurate.

* **quant_prob.py** - Machine learning Quantized probability classifier. Not quite as precise under some conditions and quant_filt.py but it can cope with greater amounts of training noise while still delivering good results with moderate amounts of training data.  


###DATA FILES###
* **data/data-sources.txt** - Explains sources for the included data files some data files are not included and will have to be donwloaded from those sources if the usage license was unclear or restrictive.

* **data/train/gest_train_ratio2.csv** - Input training data used for these tests.  We need thousands additional training samples feel free to volunteer after your read overview.pdf in this repository.



## Contribution guidelines ##

* **[todo.md](docs/todo.md)** - list of actions and enhancements roughly prioritized top down.

* **[design-notes.md](docs/design-notes.md)** Engineering Design Notes and design thoughts.

* **[genomic-notes.md](docs/genomic-notes.md)**

* **[go-notes.html](docs/go-notes.html)** Notes and helpful links about GO that I recorded while working on the classifer.go

* Writing tests
* Code review
* Other guidelines

## Who do I talk to? ##

* Repo owner Joseph Ellsworth CTO of Bayes Analytic.  Algorithms Research,  Search, Machine Learning,  Enterprise Architecture,  High Performance, High Availability distributed architecture. 
* **I sell consulting services for Search, Machine Learning, High performance High availability distriuted architecture.  http://BayesAnalytic.com/contact**


> This work actually includes two classifiers.  The first one is the Quantized Probability classifier.  The other is a Quantized Filter classifier.  Both can classify but they tend to be optimized for different use cases.    I will add additional documenation for the Quantized Filter as it matures but wanted to finish the optimizer and web interface for the Quantized probability engine first.

### Why another Classifier

The short answer is the existing systems did not meet my needs for a high performance,  accurate and portable classifier that could be deployed with minimal dependencies at low cost.   They also did not deliver the level of optimization I wanted.  They were not built with the idea of using the classifier output to help discover important data features. 

After trying several different ML systems I found each of them had one or more of these deficiencies:  

> A) They ran too slow for training.  

> B) They could not cope with incremental data updates.  

> C) They ran too slow during classification.  

> D) They consumed too much memory.  

> E) They could not handle adequately large data sets at reasonable cost.  
>
> F) They failed to deliver adequate precision, adequate recall or both.    

I tried many libraries including Weka, Scikit-learn, Azure ML, Theano, [orange](http://orange.biolab.si/),  Etc.  I tested a wide variety of classifiers such as Bayesian, Decision Trees, SVM, KNN, CNN.    Of the libraries I tried Weka seemed to offer the best performance but it struggled to cope with the data volumes I was using.      

TensorFlow Deep Learning is the newest buzz term so I am evaluating it's performance concurrent with building and testing the Quantized classifier.    I will probably add a spark test as well. 

* [Learn Markdown](https://bitbucket.org/tutorials/markdowndemo)
