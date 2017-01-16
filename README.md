# Simplified Static Gesture classifier  #

See Overview.pdf in this repository for conceptual overview of the approach.

This repository incudes code written to test ideas for static gesture recognition. It includes powerful classiers written in python that cope well with smaller training data sets.  They also handle massive training data sets with minimal memory.    Samples of using the same training data are supplied.  Includes code to feed the same data into TensorFlow to test classification using googles deep learning functionality.

* Version: 0.1
* License: (MIT) We do sell consulting services http://BayesAnalytic.com/contact

### Basic Contents ###
* overview.pdf - explains the conceptual approach behind the facet and measurement data for this
  gesture classification system.  Also includes notes for design of hardware glove to gather the data.

* quant_filt.py  - Machine learning Quantized filter classifier.  This system can provide  
   fast classification with moderate memory use and is easy to see how likely the match is to
   be accurate.

* quant_prob.py - Machine learning Quantized probability classifier. Not quite as precise under
   some conditions and quant_filt.py but it can cope with greater amounts of training noise while
   still delivering good results with moderate amounts of training data.  

* tlearn/simple_gestures.py - sample of reading CSV to  train TensorFlow Model.
   Unfortunately this program while it runs does a pour job of classification. I think
   this is the result of insufficient training data but there is a chance that I still have
   a bug in the interface to TensorFlow.

 * tlearn/tensFlowReadme.docx - Notes I made while getting tensor flow running on my windows laptop.

 * data/train/gest_train_ratio2.csv - Input training data used for these tests.  We need thousands additional training samples feel free to volunteer after your read overview.pdf in this repository.



### How do I get set up? ###

* Summary of set up
* Configuration
* Dependencies - Tested on Python 3.5 on Windows 10.
* Database configuration
* How to run tests
* Deployment instructions

### Contribution guidelines ###

* Writing tests
* Code review
* Other guidelines

### Who do I talk to? ###

* Repo owner Joseph Ellsworth
* We do sell consulting services http://BayesAnalytic.com/contact


#TODO#

##Actions for Both quant_prob and quant_filt##
* Update QuantProb to properly scale buckets to cope with outliers
* Utility to split input files into separate sets.
  
* Modify Quant_prob run as server handler. 
  * Method will use main as data set name unless &dset is specified.
  * Each named data set is unique and will not conflict with others.
  * Method to add to training data set with POST BODY
  * Method to add to training data set with URI to fetch.
    * Allow the system to skip every N records to reserve for 
    * testing.
  * Method to classify values in file at URI 
    * Allow &testEvery=X to only test every Nth
      item.  This is to support testing.     
  * Method to classify with POST multiple lines.
  * Method to classify with GET for single set of features.
  * Allow number of buckets to be set by column name
  * allow column name to be set map direct to bucket id

  *    
* Produce a version for text parsing that computes position
    indexed position of all words where each unique word gets 
    a column number.   Then when building quantized tree 
    lookup of the indexed position for that word  treat the word 
    index as the bucketId or possibly as columnNumber need to think
    that one through buck as a bucket id seems to make most sense
    nd then 
    treat all the other features as empty. So the list of cols
    may grow to several million but will only contain the hashed
    classes for those value. Allow system to pass in a list
    of clolumns n the CSV to index as text.  This would not 
    effectively use word ordering but we could use quantized buckets
    for probability of any word being that text in text string so
    a word like "the" that may occur 50 times would occur in a differnt
    bucket when it is repeated many times. 
  * Only include detail probs if requested.
  * Choose column to use as class

  * Test with following data
     * Diabeteas classification
     * 
     

* [Learn Markdown](https://bitbucket.org/tutorials/markdowndemo)
