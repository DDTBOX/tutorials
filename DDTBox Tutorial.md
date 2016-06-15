# DDTBox Tutorial

## Contents

1. Introduction to DDTBox
  1. The Decision Decoding Toolbox (DDTBox)
  2. External dependencies
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
  5. Permutation decoding with randomly-shuffled condition labels
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

DDTBox is a toolbox for multivariate pattern analysis (MVPA) of epoched EEG and other types of data.
This toolbox can be used as a plugin for [EEGLab](http://sccn.ucsd.edu/eeglab/) or as a standalone toolbox in MATLAB.

DDTBOX core scripts were originally written by Stefan Bode.
The toolbox was written with contributions from: Daniel Bennett, Daniel Feuerriegel, Phillip Alday.
The author further acknowledges helpful conceptual input/work from: Jutta Stahl, Simon Lilburn, Philip L. Smith, Elaine Corbett, Carsten Murawski, Carsten Bogler, John-Dylan Haynes.


Copyright (c) 2013--2016 Stefan Bode and contributors.

Unless otherwise specified, code is distributed under the GNU Public License (GPL) version 2, and documentation under a Creative Commons Attribution-Share-Alike 4.0 International license.

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

We hope that you find the software and documentation useful.
If you publish an analysis using the toolbox, we ask that you cite us. 
For now, please cite us via GitHub, but we are currently preparing on a publication to submit to peer review. 
A sample citation would be:

Bode, S., Bennett, D., Feuerriegel, D., & Alday, P. (2016). The Decision Decoding Toolbox -- a multivariate pattern analysis toolbox for event-related potentials. DOI: 10.5281/zenodo.48143

### External Dependencies

The code in the toolbox depends on the functionality supplied by [LIBSVM](https://www.csie.ntu.edu.tw/~cjlin/libsvm/).
We also offer support for LIBSVM's specialised and often faster cousin, [LIBLINEAR](https://www.csie.ntu.edu.tw/~cjlin/liblinear/), as well as potentially other backends for other classifiers. 
You need to configure MATLAB to use these external dependencies.
Please see their respective documentation for more information. 


###The tutorial dataset

The tutorial dataset is from [Feuerriegel, Churches and Keage (2015)](http://www.sciencedirect.com/science/article/pii/S0167876015001075).
This dataset contains epoched EEG responses to visual presentations of faces and chairs.
The data is from an image category repetition experiment in which there were two stimuli sequentially presented in each trial, separated by an interstimulus interval.
Participants were required to detect a superimposed red rectangle in one of the images in the trial.
Data from 19 subjects were included. This differs from the 16 subject datasets included for analysis in the publication, as multiple subjects provided adequate numbers of epochs for the purposes of the tutorial.
Only the first 250 epoched responses to the first stimulus in each trial (a face or a chair image) has been used in the dataset. 
This corresponds to at least 100 epochs for each image category per subject dataset.
EEG data was recorded using a 64-channel Neuroscan EEG system and has been downsampled to 250Hz to limit file sizes in the tutorial dataset.
EEG data was epoched from -100 to +300ms from stimulus onset, and baseline-corrected using the prestimulus interval.


##Preparing your data for decoding in DDTBox##

###Epoched EEG data

DDTBox accepts epoched EEG data which has been preprocessed in EEGLab (if you use programs other than EEGLab to prepare your data then please see the 'Other types of data (Advanced)' section). 
There are many approaches to preprocessing EEG data, and different preprocessing methods may be appropriate for different datasets. 
For a guide and example pipeline please see the workshop slides provided by Daniel Bennett in the [DDTBox tutorials repository](https://github.com/DDTBOX/tutorials).
Also, here is an [example EEG preprocessing pipeline](https://github.com/danielbrianbennett/eeg-preprocessing) for use with EEGLab.

###Independent component activations

DDTBox can also perform MVPA on epoched component activations derived from Independent Components Analysis (ICA). 
ICA is used for blind separation of multiple sources which contribute to potentials observed at the scalp.
In some cases several distinct components can be identified that contribute to a single observed effect in the EEG data (e.g. [Onton, Delorme & Makeig, 2005](http://www.sciencedirect.com/science/article/pii/S1053811905002673)).
A tutorial on performing ICA can be found [here](http://sccn.ucsd.edu/wiki/Chapter_09:_Decomposing_Data_Using_ICA).

###Other types of data (Advanced)

DDTBox can also be used with epoched EEG data other than that processed in EEGLab.
It can also be used with other types of data, for example to analyse patterns of eye movements or the trajectory of a participant's hand.
This can be achieved by converting the data into the format accepted by DDTBox.

The EEG data format used by DDTBox is a MATLAB array with a matrix contained within each cell of the array:

example_data{run, condition}(timepoints, channels, trials)

This means that matrices of timepoint x channel x trial are stored in each cell denoting a different condition/run combination.
A run refers to a block of trials. If using single-trial data, there will only be one run in the dataset.
For other types of data once can substitute timepoints and channels for dimension 1 and dimension 2 of the relevant dataset.
For example, in an eye tracking experiment the dimension "timepoints" may still refer to a time after stimulus onset, whereas one could use two "channels" to record the x and y coordinates of the subject's gaze fixation.


*Note: Do we know of a small example dataset that we could provide, to show how other data types could be used?*


###Configuration settings

*Outline the main configuration settings that can be input into DDTBox, with a clear explanation of each one.*



##Decoding methods##

DDTBox has several options for decoding EEG signals.
These options concern how the features (data points) are defined for MVPA, as well as the choice of datasets used for training and testing the classifier.

###Spatial decoding

Spatial decoding performs MVPA on the pattern of potential amplitudes across electrodes, averaged over the time window of interest.
The features in this decoding approach are the average potential amplitudes within a time window at each electrode. 
For example, if decoding using 64-channel EEG then there will be 64 features used for analyses (i.e. a 64 dimensions in the dataset).

###Temporal decoding

Temporal decoding performs MVPA on the pattern of potential amplitude at each time point within a time window of interest.
A separate decoding analysis is done for each electrode.
The features in this decoding approach are the potential amplitudes at each time point for a given electrode.
For example, if there are 10 samples within the time window of interest then there will be 10 features used for analyses (i.e. 10 dimensions in the dataset).

###Spatio-temporal decoding

Spatio-temporal decoding performs MVPA on the pattern of potential amplitudes at each time point within a time window, across all electrodes included in the analysis.
This is similar to temporal decoding, except that data from all electrodes are used to train and test the classifier (instead of one electrode at a time).
The features in this decoding approach are the potential amplitudes at each time point, for each electrode included in the analysis.
For example, if there are 10 samples within the time window of interest and 64 channels then there will be 640 features used for analyses (i.e. 640 dimensions in the dataset).

###Cross-condition decoding

Cross-condition decoding allows you to train the classifier on one set of data and then test the classifier on a separate set of data.
This approach can be useful to determine whether the observed multivariate pattern differences are stable across different testing conditions.
For example, cross-condition decoding can be used to test whether differences in multivariate patterns evoked by faces and chairs are stable across different task demands.

###Permutation decoding with randomly-shuffled condition labels

Permutation decoding is the same as the above MVPA approaches but with the condition labels randomly-assigned to each trial for each analysis. 
Permutation decoding can be used to generate a distribution for the null hypothesis (in this case the null hypothesis is that the patterns in each condition are exchangeable).
This approach is similar to the univariate permutation test.

###Feature weight extraction

*Define feature weights and their uses.*

###Decoding using LibLINEAR (Advanced)

DDTBox also supports decoding with LibLINEAR, a toolbox related to LibSVM.
LibLINEAR does not have the full range of functionality of LibSVM, but is generally much faster.

To illustrate the increase in speed we ran the same decoding analyses using LibSVM and LibLINEAR.
*Describe the analysis parameters*
Total time to complete analyses using LibSVM was XXXXX.
The total time required using LibLINEAR was XXXXX.


###SVM Backend Options (Advanced)

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

When performing multiple tests one must control the family-wise error rate (FWER) or false discovery rate (FDR) to maintain the probability of Type 1 errors (false rejections of the null hypothesis) at the nominal level (usually 0.05).
DDTBox provides several options for multiple comparisons correction procedures, which differ in their conservativeness but also in the inferences that can be made from each corrected family of tests.
For more information on each method and a useful tutorial on multiple comparisons corrections see [Groppe, Urbach and Kutas (2011)](http://onlinelibrary.wiley.com/doi/10.1111/j.1469-8986.2011.01273.x/full).

The currently-available options are:

1. No correction for multiple comparisons
2. Bonferroni correction
3. Holm-Bonferroni correction
4. Permutation testing based on maximum t statistics
5. Cluster-based permutation testing using cluster mass
6. Generalised family-wise error rate control using permutation tests
7. Benjamini-Hochberg false discovery rate control
8. Benjamini-Krieger-Yekutieli false discovery rate control
9. Benjamini-Yekutieli false discovery rate control

**No correction for multiple comparisons**
This option does not control for multiple comparisons.

**Bonferroni correction**
The Bonferroni correction (Dunn, 1959, 1961) divides the critical alpha level (commonly 0.05) by the number of tests.
P-values that are smaller than this adjusted alpha level are declared statistically significant.
This method controls the family-wise error rate, but can be overly conservative when there are a large number of tests.

**Holm-Bonferroni correction**


**Permutation testing based on maximum t statistics**

**Cluster-based permutation testing using cluster mass**

**Generalised family-wise error rate control using permutation tests**

**Benjamini-Hochberg false discovery rate control**

**Benjamini-Krieger-Yekutieli false discovery rate control**

**Benjamini-Yekutieli false discovery rate control**

###Robust statistical inference tests

This section briefly introduces problems with conventional tests and outlines a robust alternative available in DDTBox: Yuen's T Test, with the associated included function.



