# DDTBox Documentation

## Contents

1. Introduction to DDTBox
  1. The Decision Decoding Toolbox (DDTBox)
  2. External dependencies
  3. The tutorial dataset
2. Getting Started
  1. Installing DDTBox
3. Preparing your data for decoding in DDTBox
  1. Epoched EEG data
  2. Independent component activations
  3. Other types of data (Advanced)
  4. Creating a channel location file
4. Decoding methods
  1. Spatial decoding
  2. Temporal decoding
  3. Spatio-temporal decoding
  4. Cross-condition classification
  5. Permutation decoding with randomly-shuffled condition labels
  6. Feature weight extraction
  7. Decoding using LibLINEAR (Advanced)
  8. SVM Backend Options (Advanced)
  9. Decoding Z-scored data
  10. List of configuration settings for decoding
5. Tutorial: Multivariate pattern classification with support vector machines (SVMs)
  1. Run in EEGLab
  2. Independently from EEGLab
6. Analysing the decoded data
  1. Plotting and interpreting the decoding results
  2. Feature weight analyses
  3. Corrections for multiple comparisons
  4. Robust statistical inference tests
7. Tutorial: Plotting and analysing decoded data
  1. Run in EEGLab
  2. Independently from EEGLab
8. Troubleshooting
  1. FAQ
  2. Support



##Introduction to DDTBox##

###The Decision Decoding Toolbox (DDTBox)

Release No. 1.0

DDTBox is a toolbox for multivariate pattern analysis (MVPA) of epoched EEG and other types of data.
This toolbox can be used as a plugin for [EEGLab](http://sccn.ucsd.edu/eeglab/) or as a standalone toolbox in MATLAB.

DDTBOX core scripts were originally written by Stefan Bode.
The toolbox was written with contributions from: Daniel Bennett, Daniel Feuerriegel, Phillip Alday.
The author further acknowledges helpful conceptual input/work from: Jutta Stahl, Simon Lilburn, Philip L. Smith, Elaine Corbett, Carsten Murawski, Carsten Bogler, John-Dylan Haynes.


Copyright (c) 2013--2016 Stefan Bode and contributors.

Unless otherwise specified, code is distributed under the GNU Public License (GPL) version 2, and documentation under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />

We hope that you find the software and documentation useful.
If you publish an analysis using the toolbox, we ask that you cite us. 
For now, please cite us via GitHub, but we are currently preparing on a publication to submit to peer review. 
A sample citation would be:

Bode, S., Bennett, D., Feuerriegel, D., & Alday, P. (2016). The Decision Decoding Toolbox -- a multivariate pattern analysis toolbox for event-related potentials. DOI: 10.5281/zenodo.48143

### External Dependencies

The code in DDTBox depends on the functionality supplied by [LIBSVM](https://www.csie.ntu.edu.tw/~cjlin/libsvm/).
We also offer support for LIBSVM's specialised and often faster cousin, [LIBLINEAR](https://www.csie.ntu.edu.tw/~cjlin/liblinear/), as well as potentially other backends for other classifiers. 
You need to configure MATLAB to use these external dependencies.
Please see their respective documentation for more information. 


###The tutorial dataset

The tutorial dataset is from [Feuerriegel, Churches and Keage (2015)](http://www.sciencedirect.com/science/article/pii/S0167876015001075).
This dataset contains epoched EEG responses to visual presentations of faces and chairs.
The data is from an image category repetition experiment in which there were two stimuli sequentially presented in each trial, separated by an interstimulus interval.
Participants were required to detect a superimposed red rectangle in one of the images in the trial (red rectangles appeared in 9% of trials).
Data from 19 subjects were included. This differs from the 16 subject datasets included for analysis in the publication, as multiple subjects provided adequate numbers of epochs for the purposes of the tutorial dataset.
Only the first 250 epoched responses to the first stimulus in each trial (a face or a chair image) has been used in the dataset. 
This corresponds to at least 100 epochs for each image category per subject dataset.
EEG data was recorded using a 64-channel Neuroscan EEG system and has been downsampled to 250Hz to limit file sizes in the tutorial dataset.
EEG data was epoched from -100 to +300ms from stimulus onset, and baseline-corrected using the prestimulus interval.

*Include where the user can access the tutorial dataset, once a storage location has been determined*

##Getting Started##

###Installing and configuring DDTBox###

To use DDTBox as an EEGLab plugin copy the folder containing the DDTBOX code into the "plugins" folder in EEGLab.
When you initialise EEGLab in MATLAB the DDTBox plugin should automatically load, and can then be found in the "Tools" menu of the EEGLab GUI.

To use DDTBox independently of EEGLab you can specify the path to the code in MATLAB.
To do this click "Set Path" then click "Add Folder" and specify the folder containing the DDTBox code.

*Note: Are there any other configuration steps to mention? For example where to put chanlocs files?*

##Preparing your data for decoding in DDTBox##

###Epoched EEG data

DDTBox accepts epoched EEG data which has been preprocessed in EEGLab (if you use programs other than EEGLab to prepare your data then please see the 'Other types of data (Advanced)' section).
DDTBox can also be used with data that has been epoched using [ERPLab](http://erpinfo.org/erplab).
There are many approaches to preprocessing EEG data. Different preprocessing methods may be appropriate for different datasets. 
For a guide and example pipeline please see the workshop slides provided by Daniel Bennett in the [DDTBox tutorials repository](https://github.com/DDTBOX/tutorials).
Also, an [example EEG preprocessing pipeline](https://github.com/danielbrianbennett/eeg-preprocessing) is provided for use with EEGLab.

###Independent component activations

DDTBox can also perform MVPA on epoched component activations derived from Independent Components Analysis (ICA). 
ICA is used for blind separation of multiple sources which contribute to potentials observed at the scalp.
In some cases several distinct components can be identified that contribute to a single observed effect in the EEG data (e.g. [Onton, Delorme & Makeig, 2005](http://www.sciencedirect.com/science/article/pii/S1053811905002673)).
A tutorial on performing ICA in EEGLab can be found [here](http://sccn.ucsd.edu/wiki/Chapter_09:_Decomposing_Data_Using_ICA).

###Other types of data (Advanced)

DDTBox can also be used with epoched EEG data processed using custom scripts or other software packages.
It can also be used with other types of data, for example patterns of eye movements or the trajectory of a participant's hand.
This can be achieved by converting the data into the format accepted by DDTBox.

The EEG data format used by DDTBox is a MATLAB array with a matrix contained within each cell of the array:

example_data{run, condition}(timepoints, channels, trials)

The timepoint x channel x trial matrices are stored in each cell denoting a different condition/run combination.
A run refers to a set of trials.
For example, for one condition each block of trials in an experiment might be counted as a separate run.
For other types of data you can substitute timepoints and channels for dimension 1 and dimension 2 of the relevant dataset.
For example, in an eye tracking experiment the dimension "timepoints" may still refer to a time after stimulus onset, whereas one could use two "channels" to record the x and y coordinates of the subject's gaze fixation.

###Creating a channel locations file

A channel locations file is used for plotting the feature weights results of temporal decoding analyses.
Channel locations files can be created from the EEG structure in EEGLab by coping the EEG.chaninfo and EEG.chanlocs fields into a separate MATLAB file.

A function for creating channel locations files from loaded EEGLab datasets is provided.
Before using this function you must load an EEGLab dataset into the MATLAB workspace.
Then, you can call the function as follows:
<div>
make_chanlocs_file(EEG, 'save_filepath', location at which the channel locations file will be saved) 
</div>

##Decoding methods##

DDTBox has several options for decoding EEG signals.
These options concern how the features (data values in each dimension) are defined for MVPA, as well as the choice of datasets used for training and testing the classifier.

###Spatial decoding

Spatial decoding performs MVPA on the pattern of potential amplitudes across electrodes, averaged over the time window of interest.
The features in this decoding approach are the average potential amplitudes within a time window at each electrode. 
For example, if decoding using 64-channel EEG then there will be 64 features used for analyses (i.e. a 64 dimensions in the dataset).

###Temporal decoding

Temporal decoding performs MVPA on the pattern of potential amplitudes at each time point within a time window of interest.
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
This approach can be useful to determine whether any observed multivariate pattern differences are stable across different testing conditions.
For example, cross-condition decoding can be used to test whether differences in multivariate patterns evoked by different object categories (such as faces and chairs) are stable across different experimental tasks.

###Permutation decoding with randomly-shuffled condition labels

Permutation decoding is the same as the above MVPA approaches but with the condition labels randomly-assigned to each trial for each analysis. 
Permutation decoding can be used to generate a distribution for the null hypothesis (in this case the null hypothesis is that the patterns in each condition are exchangeable).
This approach is similar to the univariate permutation test.

###Feature weight extraction

DDTBox can extract feature weights from the SVM classification results.
Each EEG data channel is assigned a feature weight based on the importance of each channel for the SVM classification.
For more information on how to interpret feature weights results, see [Haufe et al. (2014)](http://www.sciencedirect.com/science/article/pii/S1053811913010914).

*Provide a concise description of feature weights so that they can be easily interpreted by the user*

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
 
The citation for LibSVM is Chih-Chung Chang and Chih-Jen Lin, LIBSVM : A library for support vector machines. ACM Transactions on Intelligent Systems and Technology, 2:27:1--27:27, 2011.
 
The following inputs are used as flags for LibSVM in DDTBox:

**-c** : Cost Parameter

**-s** SVM Type : set type of SVM

  0 -- C-Support Vector Classification
  
  1 -- nu-Support Vector Classification
  
  2 -- one-class SVM
  
  3 -- epsilon-Support Vector Regression
  
  4 -- nu-Support Vector Regression

**-t** Kernel Type : set type of kernel function

  0 -- linear: u' * v				Default kernel
  
  1 -- polynomial: (gamma * u' * v + coef0)^degree
  
  2 -- radial basis function: exp(-gamma * |u-v|^2)
  
  3 -- sigmoid: tanh(gamma * u' * v + coef0)
 

Additional flags can also be specified in the GUI and in the MATLAB scripts.


**LibLINEAR Flags**

For more information about LibLINEAR and the full list of LibLINEAR flags see https://www.csie.ntu.edu.tw/~cjlin/liblinear/

The citation for LibLINEAR is R.-E. Fan, K.-W. Chang, C.-J. Hsieh, X.-R. Wang, and C.-J. Lin. LIBLINEAR: A library for large linear classification Journal of Machine Learning Research 9(2008), 1871--1874.

The following inputs are used as flags for LibLINEAR in DDTBox:

**-c** cost : cost parameter

**-s** svm_type:

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

For some analyses it can be useful to convert the EEG or other data into z-scores before MVPA.
This can be useful for identifying patterns across sensors and/or time that are independent of overall amplitude differences across conditions.
Z-scoring can also be useful for cross-condition classification (training on one set of data and testing on another set of data) when the absolute amplitudes of the event-related potentials differ across the datasets.
For example, if the event-related potentials are more positive overall in the training data than the test data, then this amplitude difference can cause problems for a linear classifier.
Z-scoring is a way to remove this net amplitude difference and so may help the classifiers attain a higher decoding accuracy in these situations.

The way in which data are converted into z-scores depends on the analysis type:

**Spatial decoding**: Data are z-scored across electrodes for each time window.

**Temporal decoding**: Data are z-scored across time-points for each individual electrode.

**Spatio-temporal decoding**: Data are z-scored across time-points for each individual electrode.


###List of configuration settings for decoding

The following configuration parameters can be set within the EEGLab GUI or in MATLAB scripts when calling DDTBox functions:

**stmode**   space/time decoding type (1=spatial / 2=temporal / 3=spatio-temporal)

**avmode**   averaging mode (1=no averaging; single-trial / 2=run averaged) 

**analysis_mode** analysis mode (1=SVM classification, 2=LDA classification, 3=SVR increments, 4=SVR continous, 5=SVM with liblinear)
*Note: Update analysis mode settings when LDA option has been removed.*

**window_width_ms**  width of sliding analysis window in milliseconds

**step_width_ms**  step size with which the sliding window is moved through the epoch.

**perm_test** choose whether to run permutation decoding analyses with randomly-assigned condition labels (0=no / 1=yes)

**display_on**  choose whether to display the decoding results in a figure (0=no / 1=yes)

**perm_disp** choose whether to display the permutation results in a figure (0=no / 1=yes)

**zscore_convert**  choose whether to convert data into z-scores before decoding (0 = no / 1 = yes)

**feat_weights_mode**  choose whether to extract feature weights (0=no / 1=yes)

**cross_val_steps**  Set the number of cross-validation steps

**n_rep_cross_val**  Set the number of repetitions of full cross-validation with randomly re-ordered data

**permut_rep**  Set the number of repetitions of full cross-validation cycles using permutation decoding



To set configuration parameters in MATLAB scripts you can add the name of the parameter as a string, followed by the value of that parameter.
For example:

*Give an example of the decoding_erp function with input argument pairs*

There are no true defaults in DDTBox, so it is a good idea to set all parameters yourself when calling DDTBox functions. 




##Example: Multivariate pattern classification with support vector machines (SVMs)##

###Run in EEGLab


###Independently from EEGLab



##Analysing the decoded data##

###Plotting and interpreting the decoding results

*This section shows examples of individual- and group-level results, along with a guide for interpreting the plots.*

DDTBox uses paired-samples t tests for analyses, however an advanced user can perform other statistical tests on the data.


###Feature weight analyses

This section gives an example of feature weight analysis results, along with a guide for interpreting the plots.


###Corrections for multiple comparisons

When performing multiple tests it is usually important to control the family-wise error rate (FWER) or false discovery rate (FDR) to maintain the probability of Type 1 errors (i.e. false rejections of the null hypothesis) at the nominal level (usually 0.05).
DDTBox provides several options for multiple comparisons correction procedures.
Many of these have been adopted from techniques used in mass univariate analysis of ERPs, which take advantage of the correlation seen across time points in EEG data. 
These methods differ with regard to their ability to detect real effects but also in the inferences that can be made from each corrected family of tests.
For more information on these methods and a useful tutorial on multiple comparisons corrections see [Groppe, Urbach and Kutas (2011)](http://onlinelibrary.wiley.com/doi/10.1111/j.1469-8986.2011.01273.x/full).

The options available in DDTBox are:

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
The Bonferroni correction (Dunn, [1959](10.1214/aoms/1177706374), [1961](10.1080/01621459.1961.10482090)) divides the critical alpha level (commonly 0.05) by the number of tests.
P-values that are smaller than this adjusted alpha level are declared statistically significant.
This method controls the family-wise error rate, but can be overly conservative when there are a large number of tests (and so may fail to detect real effects).

**Holm-Bonferroni correction**
The Holm-Bonferroni correction ([Holm, 1979](http://www.jstor.org/stable/4615733)) controls the family-wise error rate in the strong sense but is less conservative than the Bonferroni correction.
One advantage of the Holm-Bonferroni method is that it does not rely on assumptions about the independence of tests at different time windows.
The method is described as follows:

1. Conduct m tests (for example using paired-samples t tests or Yuen's t test).

2.	Sort the resulting p-values from lowest to highest: p(1), p(2) ... p(m).
p(1) refers to the smallest p-value, and so H(1) refers to the null hypothesis test with the smallest p-value.
p(2) refers to the second smallest p-value, p(3) refers to the third smallest p-value, and so on.

3. Set k = 1.

4. Check whether p(k) is larger than the critical alpha level / (m + 1 - k). *Check how to do mathematical notation in markdown*

5. If p(k) is larger than this value, then do not reject the null hypothesis H(k) and stop the procedure.
If the procedure is stopped then all untested null hypotheses H(k + 1) ... H(m) are also not rejected.
If p(1) is not larger than this value, then reject null hypothesis H(k).

6. If the procedure has not been stopped then repeat steps 4 and 5 for k = 2, k = 3, ... k = m.


**Permutation testing based on maximum t statistics**
This option uses the approach described in [Blair and Karniski (1993)](dx.doi.org/10.1111/j.1469-8986.1993.tb02075.x).
Their method is guaranteed to control the family-wise error rate in the strong sense but is usually more sensitive to detect effects compared to the Bonferroni correction.
The method is implemented in DDTBox as follows:

1.	Run paired-samples t-tests for each analysis time window and record t statistics resulting from each test.
2.	For each time window generate a permutation sample of decoding accuracies (randomly switching condition labels between the actual test and permutation decoding results, or the actual results and the theoretical chance level).
3.	Repeat step 2 B times (B = number of permutations to use).
B should be set as at least 1000 for inferences using a critical alpha of p = 0.05, and at least 5000-10000 for inferences at p = 0.01.
4.	For each set of permutations (i.e. one permutation sample for each time window makes a set of tests) calculate the maximum absolute t-value (positive or negative).
For example, get the 1st permutation sample for each time window and then find the maximum absolute t-value, and then get the 2nd permutation sample for each time window and find the maximum absolute t-value, etc.
5.	Calculate the 95th percentile of the distribution of these maximum t-values. This is the adjusted statistical significance threshold.
6.	Check whether each of the original tests (not using the permutation samples) gives a t-value above the threshold.
Tests yielding larger absolute t-values (positive or negative) than the threshold are declared statistically significant.
All other tests are not declared statistically-significant. 



**Cluster-based permutation testing using cluster mass**

Cluster-based permutation testing ([Bullmore et al., 1999](dx.doi.org/10.1109/42.750253), see also [Maris & Oostenveld, 2007](dx.doi.org/10.1016/j.jneumeth.2007.03.024)) is a method that is very sensitive for detecting effects that span several adjacent time windows.
It is similar to the Blair-Karniski permutation test above, but uses test statistics derived from clusters (i.e. groups of temporally adjacent statistically significant effects).
This approach takes advantage of the fact that real effect in EEG data are often correlated over time.
Consequently, real effects may be spread over several adjacent time windows, whereas spurious chance effects are more likely to be limited to an isolated time window.
The method is implemented in DDTBox as follows:

1.	Run paired samples t-tests on each of the time windows and record t statistics from each test.
For adjacent t-tests which are statistically significant at some specified threshold (e.g. 0.05) add these t scores to generate a cluster statistic.
Isolated statistically significant tests are also treated as separate clusters.
2.	For each time window generate a permutation sample of decoding accuracies (randomly switching condition labels between the actual test and permutation results, or the actual results and the theoretical chance level).
3.	Repeat step 2 B times (B = number of permutations to use).
B should be set as at least 1000 for inferences using a critical alpha of p = 0.05, and at least 5000-10000 for inferences at p = 0.01.
4.	For each permutation find the maximum cluster mass (sum of t scores across adjacent statistically significant tests) within the set of tests.
Adjacent statistically-significant tests that change sign (i.e. a positive difference switching to a negative difference) are assigned to different clusters.
5.	Calculate the nth percentile of the distribution of maximum cluster mass statistics (for example 95th percentile for a critical alpha of 0.05).
This becomes the adjusted statistical significance threshold.
6.	Check whether each cluster in the original (non-permutation) tests gives a cluster mass above the threshold.
Tests in clusters with larger cluster masses than the threshold are declared statistically significant.
All other tests are not declared statistically-significant. 

Cluster-based permutation tests control the family-wise error rate in the weak sense.
Weak family-wise error rate control means that the type 1 error rate control is guaranteed only when all tested null hypotheses are true (i.e. there are no real effects in the data).
This means that cluster-based permutation tests are very good at detecting effects, but one cannot be sure of the exact onset or offset of the effect.
When the precise timing of an effect is important to your research question, then the Blair-Karniski permutation test will be more appropriate.
Cluster-based permutation test results are also influenced by the critical alpha level used to determine whether individual tests are statistically significant (and so whether they are included in clusters).
A threshold of p = 0.05 is useful for detecting effects that persist over time windows, but may lack the sensitivity to detect effects that are limited to a single time window.
Stricter thresholds such as p = 0.01 are useful for detecting effects isolated to a single time window when these effects give very large t statistics, but are less effective at detecting weaker broadly-distributed effects over time.

**Generalised family-wise error rate control using permutation tests**

This method controls the generalised family-wise error rate, which means, with the probability of the set critical alpha, that the number of false discoveries does not exceed a set parameter u.
As u is higher (and more false discoveries are allowed) the power of this method greatly increases, allowing more real effects to be detected.
If u parameter is set to 0, then this procedure is the same as the Blair-Karniski permutation test described above.
The method used in DDTBox is by [Korn, Troendle, McShane and Simon (2004)](http://www.sciencedirect.com/science/article/pii/S0378375803002118) as described in [Groppe, Urbach and Kutas (2011)](http://onlinelibrary.wiley.com/doi/10.1111/j.1469-8986.2011.01273.x/full).
The method is implemented as follows:

1.	For m tests, sort p-values from the entire family of tests from smallest to largest.
2.	Automatically reject the u null hypotheses with the smallest p-values. 
The p-values for the automatically-rejected null hypotheses is set to zero.
3.	For the remaining m – u hypotheses obtain the adjusted p-values via the Blair-Karniski permutation test, but for each permutation extract the u + 1th most postive/negative t statistic to create the null hypothesis distribution. 


**False discovery rate control**

False discovery rate (FDR) control is a more powerful alternative to strong family-wise error rate control, that is based on the proportion of false discoveries (mistakenly-rejected null hypotheses).
These procedures provide only weak family-wise error rate control, however the number of false discoveries is usually much smaller than the number of real effects found using this procedure.
The FDR control procedures here differ with regard to their underlying assumptions and their conservativeness.
The [Benjamini-Hochberg procedure](http://www.jstor.org/stable/2346101) assumes that individual analysis time windows (within the family of tests) are independent or positively correlated.
The [Benjamini-Krieger-Yekutieli procedure](dx.doi.org/10.1093/biomet/93.3.491) assumes that results at each time window are independent (i.e. not positively or negatively correlated).
The [Benjamini-Yekutieli procedure](dx.doi.org/10.1198/016214504000001907) does not make assumptions about the independence of results across time windows, but is more conservative than the other FDR control procedures.
For a more detailed discussion of the properties of false discovery rate control see [Groppe, Urbach and Kutas (2011)](http://onlinelibrary.wiley.com/doi/10.1111/j.1469-8986.2011.01273.x/full).

**Comparing multiple comparisons correction methods**

*Compare results from each correction method on the tutorial dataset results, showing how each method may be more/less sensitive to detect different effect*

###Robust statistical inference tests

Analyses in DDTBox can also be performed using the paired-samples version of Yuen's t test ([Yuen, 1974](dx.doi.org/10.1093/biomet/61.1.165)) which is a robust alternative to the paired-samples t test.
Paired-samples t tests can perform poorly (especially with regard to variance estimates) when the data distribution is not Gaussian/normal or when there are outliers.
Yuen's t test generally performs well under normality, but can guard against problems caused by non-Gaussian data distributions.
For an accessible introduction to robust statistical methods see [Wilcox (2012)](http://www.sciencedirect.com/science/book/9780123869838).

##Tutorial: Plotting and analysing decoded data##

*Use the tutorial dataset to show how decoding results can be plotted and interpreted*

###Run in EEGLab###

###Independently from EEGLab###




##Troubleshooting##

###FAQ###

There have not yet been questions that have been frequently asked.

*Could source FAQs from questions asked at the UniSA/Uni Melbourne Workshop*

###Support###

*Detail details of bug reports and the DDTBox mailing list here*

