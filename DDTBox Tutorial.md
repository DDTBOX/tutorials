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

**The Decision Decoding Toolbox (DDTBox**

A quick description of the toolbox, functions and relevant contributors.

**Introduction to multivariate pattern analysis (MVPA) with EEG: Approach and examples**

A general overview of MVPA with EEG, including examples of situations where MVPA gives additional information to standard ERP analyses.

**The tutorial dataset**

A short description of the tutorial dataset, including the experiment and data characteristics. 
For now this would be the Faces and Chairs epoched data in Feuerriegel et al. (2015) but a different dataset might be more appropriate.

##Preparing your data for decoding in DDTBox##

**Epoched EEG data**

Give an example EEG data processing pipeline, but direct users to the EEGLab tutorials for more detailed information.
Mention current source density (CSD) transformations and uses in EEG decoding.

**Independent component activations**

Show how selected ICs can be used as input for decoding analyses.

**Other types of data (Advanced)**

Describe the data structure used for DDTBox and how other types of data (e.g. behavioural data) can be prepared for decoding.

**Configuration settings**

Outline the main configuration settings that can be input into DDTBox, with a clear explanation of each one.


##Decoding methods##

**Spatial decoding**

Describe spatial decoding across electrodes.

**Temporal decoding**

Describe temporal decoding across time points at each electrode.

**Spatio-temporal decoding**

Describe spatio-temporal decoding.

**Cross-condition classification**

Describe cross-condition classification and give an example of when it would be relevant to a research question.

**Permutation decoding with randomly-shuffled labels**

Describe permutation decoding with randomly-shuffled labels

**Feature weight extraction**

Define feature weights and their uses.

**Decoding using LibLINEAR (Advanced)**

Describe LibLINEAR, its capabilities relative to LibSVM, and show an example of improved speed when using LibLINEAR

**SVM Backend Options (Advanced)**

Describe the backend options available to the user (accessible through the GUI or by scripting). Emphasize that people must know exactly what they are doing when they change these settings.

**Decoding Z-scored data**

Describe the Z-transformation for spatial, temporal and spatio-temporal data. Give an example of when decoding on z-scores would be useful.



##Example: Multivariate pattern classification with support vector machines (SVMs)##

This is the section for a worked tutorial using the GUI and some advice about scripting for advanced users.



##Example: Support vector regression##

This is the section for a worked tutorial using the GUI and some advice about scripting for advanced users.



##Analysing the decoded data##

**Plotting and interpreting the decoding results**

This section shows examples of individual- and group-level results, along with a guide for interpreting the plots.


**Feature weight analyses**

This section gives an example of feature weight analysis results, along with a guide for interpreting the plots.

**Corrections for multiple comparisons**

This section outlines the multiple comparisons problem, and the options available for correcting for this.

**Robust statistical inference tests**

This section briefly introduces problems with conventional tests and outlines a robust alternative available in DDTBox: Yuen's T Test, with the associated included function.



