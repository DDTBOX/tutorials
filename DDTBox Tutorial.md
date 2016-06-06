# DDTBox Tutorial

## Contents

1. Introduction to DDTBox
  1. The Decision Decoding Toolbox (or Dan Dan Toolbox or whatever we call it)
  2. Introduction to MVPA with EEG: Approach and examples
  3. The tutorial dataset
2. Preparing your data for decoding in DDTBox
  1. Epoched EEG data
  2. Independent component activations
  3. Other types of data (Advanced)
  4. Configuration settings
3. Decoding methods
  1. Spatial decoding
  2. Temporal decoding
  3. Spatio-temporal decoding
  4. Cross-condition classification
  5. Permutation decoding with randomly-shuffled labels
  6. Feature weight extraction
  7. Decoding using LibLINEAR (Advanced)
  8. SVM Backend Options (Advanced)
  9. Decoding Z-scored data
4. Example: Multivariate pattern classification with support vector machines (SVMs)
5. Example: Support vector regression
6. Analysing the decoded data
  1. Plotting and interpreting the decoding results
  2. Feature weight analyses
  3. Corrections for multiple comparisons
  4. Robust statistical inference tests




##Introduction to DDTBox##

###The Decision Decoding Toolbox (DDTBox)

A quick description of the toolbox, functions and relevant contributors.

Original DDTBOX scripts were written by Stefan Bode.
The toolbox was written with contributions from: Daniel Bennett, Jutta Stahl, Daniel Feuerriegel, Phillip Alday.
The author further acknowledges helpful conceptual input/work from: Simon Lilburn, Philip L. Smith, Elaine Corbett, Carsten Murawski, Carsten Bogler, John-Dylan Haynes




###Introduction to multivariate pattern analysis (MVPA) with EEG: Approach and examples

A general overview of MVPA with EEG, including examples of situations where MVPA gives additional information to standard ERP analyses.

###The tutorial dataset

A short description of the tutorial dataset, including the experiment and data characteristics. 
For now this would be the Faces and Chairs epoched data in Feuerriegel et al. (2015) but a different dataset might be more appropriate.

##Preparing your data for decoding in DDTBox##

###Epoched EEG data

Give an example EEG data processing pipeline, but direct users to the EEGLab tutorials for more detailed information.
Mention current source density (CSD) transformations and uses in EEG decoding.

###Independent component activations

Show how selected ICs can be used as input for decoding analyses.

###Other types of data (Advanced)

Describe the data structure used for DDTBox and how other types of data (e.g. behavioural data) can be prepared for decoding.

DDTBox can also be used for multivariate decoding of other data types than EEG, for example patterns of eye movements or the trajectory of the subject's hand in a behavioural reaching task.

The EEG data structure used by DDTBox is of the form:

EEGdata{run, condition}(timepoints, channels, trials)

This means that matrices of timepoint x channel x trial are stored in each cell denoting a different condition/run combination.
In this data, a run refers to a block of trials. If using single-trials, there is usually only one run in the dataset.
For other types of data once can substitute timepoints and channels for dimension 1 and dimension 2 of the relevant dataset.
For example, in an eye tracking experiment the dimension "timepoints" may still refer to a time after stimulus onset, whereas one could use two "channels" to record the x and y coordinates of the subject's gaze fixation.



###Configuration settings

Outline the main configuration settings that can be input into DDTBox, with a clear explanation of each one.


##Decoding methods##

###Spatial decoding

Describe spatial decoding across electrodes.

###Temporal decoding

Describe temporal decoding across time points at each electrode.

###Spatio-temporal decoding

Describe spatio-temporal decoding.

###Cross-condition classification

Describe cross-condition classification and give an example of when it would be relevant to a research question.

###Permutation decoding with randomly-shuffled labels

Describe permutation decoding with randomly-shuffled labels

###Feature weight extraction

Define feature weights and their uses.

###Decoding using LibLINEAR (Advanced)

Describe LibLINEAR, its capabilities relative to LibSVM, and show an example of improved speed when using LibLINEAR

###SVM Backend Options (Advanced)

Describe the backend options available to the user (accessible through the GUI or by scripting). Emphasize that people must know exactly what they are doing when they change these settings.

DDTBox uses LibSVM and LibLINEAR for multivariate pattern classification and support-vector regression. 
LibSVM and LibLINEAR require input flags to specify the type of statistical model that will be used for decoding. 
We strongly recommend using the default flags unless you have a thorough understanding of support vector machines and LibSVM.
Do not change these parameters unless you really know what you are doing!!!

**LibSVM Flags**

For more information about LibSVM and the full list of libSVM flags see https://www.csie.ntu.edu.tw/~cjlin/libsvm/
 
The citation for LibSVM is Chih-Chung Chang and Chih-Jen Lin, LIBSVM : a library for support vector machines. ACM Transactions on Intelligent Systems and Technology, 2:27:1--27:27, 2011.
 
The following inputs are used as flags for LibSVM in DDTBox:

-c : Cost Parameter

-s SVM Type : set type of SVM

  0 -- C-Support Vector Classification
  
  1 -- nu-Support Vector Classification
  
  2 -- one-class SVM
  
  3 -- epsilon-Support Vector Regression
  
  4 -- nu-Support Vector Regression

-t Kernel Type : set type of kernel function

  0 -- linear: u' * v				Default kernel
  
  1 -- polynomial: (gamma * u' * v + coef0)^degree
  
  2 -- radial basis function: exp(-gamma * |u-v|^2)
  
  3 -- sigmoid: tanh(gamma * u' * v + coef0)
 

Additional flags can also be specified in the GUI and in the MATLAB scripts.


**LibLINEAR Flags**

For more information about LibLINEAR and the full list of LibLINEAR flags see https://www.csie.ntu.edu.tw/~cjlin/liblinear/

The citation for LibLINEAR is R.-E. Fan, K.-W. Chang, C.-J. Hsieh, X.-R. Wang, and C.-J. Lin. LIBLINEAR: A library for large linear classification Journal of Machine Learning Research 9(2008), 1871--1874.

The following inputs are used as flags for LibLINEAR in DDTBox:

-c cost : cost parameter

-s svm_type:

  0 -- L2-regularized logistic regression (primal)
  
  1 -- L2-regularized L2-loss support vector classification (dual)
  
  2 -- L2-regularized L2-loss support vector classification (primal)
  
  3 -- L2-regularized L1-loss support vector classification (dual)
  
  4 -- support vector classification by Crammer and Singer
  
  5 -- L1-regularized L2-loss support vector classification
  
  6 -- L1-regularized logistic regression
  
  7 -- L2-regularized logistic regression (dual)
  
  11 -- L2-regularized L2-loss support vector regression (primal)
  
  12 -- L2-regularized L2-loss support vector regression (dual)
  
  13 -- L2-regularized L1-loss support vector regression (dual)

**Default Flags for LibSVM and LibLINEAR**

The following settings are used as defaults in DDTBox:

Support Vector Classification with LibSVM - '-s 0 -t 0 -c 1'

Support Vector Regression with LibSVM - '-s 3 -t 0 -c 0.1'

Support Vector Regression (continuous) with LibSVM - '-s 3 -t 0 -c 0.1'

Support Vector Classification with LibLINEAR - '-s 2 -c 1'




###Decoding Z-scored data

Describe the Z-transformation for spatial, temporal and spatio-temporal data. Give an example of when decoding on z-scores would be useful.

For some analyses it can be useful to convert the EEG or other data into z-scores before running decoding analyses.
This can be useful for identifying patterns across sensors and/or time that are independent of overall amplitude differences across conditions.
Z-scoring can also be useful for cross-condition classification (training on one set of data and testing on another set of data) when the absolute amplitudes of the event-related potentials differ across the datasets.
For example, if the event-related potentials are more positive overall in the training data than the test data, then this amplitude difference can cause problems for a linear classifier.
Z-scoring is a way to remove this net amplitude difference and so may help the classifiers attain a higher decoding accuracy in these situations.

The way in which data are converted into z-scores depends on the analysis type:

**Spatial decoding**: Data are z-scored across electrodes for each time window.

**Temporal decoding**: Data are z-scored across time-points for each individual electrode.

**Spatio-temporal decoding**: Data are z-scored across time-points for each individual electrode.


##Example: Multivariate pattern classification with support vector machines (SVMs)##

This is the section for a worked tutorial using the GUI and some advice about scripting for advanced users.



##Example: Support vector regression##

This is the section for a worked tutorial using the GUI and some advice about scripting for advanced users.



##Analysing the decoded data##

###Plotting and interpreting the decoding results

This section shows examples of individual- and group-level results, along with a guide for interpreting the plots.


###Feature weight analyses

This section gives an example of feature weight analysis results, along with a guide for interpreting the plots.


###Corrections for multiple comparisons

This section outlines the multiple comparisons problem, and the options available for correcting for this.






###Robust statistical inference tests

This section briefly introduces problems with conventional tests and outlines a robust alternative available in DDTBox: Yuen's T Test, with the associated included function.



