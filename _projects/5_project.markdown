---
layout: page
title: detecting obfuscation
description: using ML to  detect obfuscation in java applications
img: /assets/img/detectobf.png
importance: 5
category: work
---

*Renuka Kumar, Anand Raj Essar Vaishak*

Code  obfuscation  was  introduced  as  a  viable  technique  to  prevent  reverse  engineering  of  software  applications.  Obfuscation  protects  an  application's  key  algorithms  and  data  structures  from  theft  by  hackers.  However,  malware  authors  use  the  same  techniques to create a malware or insert malicious logic into a legitimate application. This paper proposes an analysis system to detect lexical and string obfuscation in Java malware. We identify a set of eleven features that characterizes obfuscated code, and use  it  to  train  a  machine  learning  classifier  to  distinguish  between  obfuscated  and  non-obfuscated  malware.  The  features  are  extracted  using  a  static  analyzer  that  examines  bytecode.  Our  experimental  results  based  on  a  dataset  of  375  malware  samples  containing 182927 strings and 12721 Java classes provide an accuracy of 99%. The proposed features are effective even when a dictionary is employed for lexical obfuscation. We evaluated the robustness of our features by calculating chi-squared statistic for each feature. 

Find paper [here](https://www.sciencedirect.com/science/article/pii/S1877050916000995).

### Detection Approach

In this paper, we study lexical obfuscation, the most commonly employed obfuscation technique. Lexical obfuscation renames classes, methods and fields based on a certain pattern. An example pattern is: a*b*c*d* ...x*y*z. The star quantifier (*) indicates that the preceding letter can occur zero or more times. Hence a lexically obfuscated identifier can be renamed as a,b,c etc. or aa,ab,ac etc. Standard Java classes, however, are not obfuscated during lexical transformation. Figure below shows lexical obfuscation of methods and fields in a sample class file.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/obfex.png' | relative_url }}" alt="" title="summary of obfuscation techniques"/>
    </div>
</div>

Our analysis system is a combination of a static analyzer that extracts features indicating obfuscation, and a machine learning classifier that uses supervised learning to distinguish between obfuscated and non-obfuscated Java malware. The tool is designed to be generic and is agnostic to the obfuscation tool used to obfuscate malware. Figure below shows the architecture of our obfuscation detection system.


<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/detectobf.png' | relative_url }}" alt="" title="system for obfuscation detection"/>
    </div>
</div>
<br>

#### Static Analyzer
The static analyzer tool operates at the level of bytecode to extract lexical information from it. The input to the static analyzer is the malware Jar file. The contents of the Jar file are analyzed and the features extracted from the classes. The symbol table information for each class is stored in a section of the classfile called the constant pool. The constant pool stores literal constant values such as numbers, strings, identifiers names, class names, method names etc. Since lexical obfuscation renames identifiers, the analyzer parses through the constant pool to extract strings, class names, field names and method names. They form the basis of our feature set.

#### Feature Extraction
We identified eleven text-based features to detect lexically obfuscated and string encrypted malware as shown below.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/features.png' | relative_url }}" alt="" title="features to detect obfuscation"/>
    </div>
</div>

#### Classifier
The classifier has two phases – a training phase (where statistical information about obfuscated and non- obfuscated malware is gathered) and the test/classification phase (where the classifier classifies new malware samples as obfuscated or non-obfuscated). The malware Jar samples are labeled as obfuscated or non-obfuscated to train the classifier. Research claims that SVM is the most appropriate classifier for text-based pattern recognition and yields the smallest error rates even with a limited training set20,38. The training set data is labeled ‘True’ when the malware sample is obfuscated and ‘False’ to indicate non-obfuscated sample. We calculate the accuracy of results obtained from the classifier based on *Precision*, *Recall*, and *F-score*.

#### Experiment and Results
The static analyzer Java ASM, bytecode manipulation framework. A total of 182927 strings and 12721 class names were extracted from 375 Jar files of which 125 were obfuscated and 250, non-obfuscated. We used Weka for classification and tested four classifiers - Support Vector Machines (SVM), Naive Bayes, C4.5 Decision tree and Random forest. In our experiments, we used the 10-fold cross validation to train and evaluate the classifier results.

The test samples were categorized into two classes. The first class contained non-obfuscated samples and the second contained obfuscated samples. Moskovitch research was used for estimating the ratio of obfuscated vs. non-obfuscated files in the training set. 33 % total samples were obfuscated samples. The threshold values are calculated based on previous research findings as well as our own. For example, the length of majority of words in English has been determined to fall in the range 4-7. For our experiment, the threshold was set to 9, since obfuscated and encoded strings tend to run long. The threshold value was arrived at by empirically measuring the longest word in non-obfuscated jars, which was obtained to be of length 20. The reason for such an unusually high value is the presence of URLs and encoded strings in non-obfuscated samples. However, a threshold value that is within the range 4-7 will not change our results in any way. The order of vowels in decreasing order of frequency is obtained from Dushatsky’s work. The unigram and bigram frequencies are assigned based on Solo’s research. On continuous observation, we found that lexical obfuscation that does not use a dictionary, can be detected by setting the threshold of the length of identifier names to 2. Since there are 26 * 26 possible combinations for a 2-letter name, we assume that a single package will never contain such a large number of classes. For the metric entropy, we arrived at a threshold value 0.3 after analyzing all the extracted strings. We evaluated the robustness of the features by calculating chi-squared statistic for each feature. The five most robust features are: vowel frequency, vowel count, method name length, word size and n-gram. Table 1 shows the summary of threshold values.

The figure shows the percentage contributions of each of the ten features (excluding vowel frequency since vowel count includes the total frequency of each vowel) when 250 non-obfuscated malware and 125 obfuscated malware samples were statically analyzed.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/graph.png' | relative_url }}" alt="" title="features relevance"/>
    </div>
</div>

For example, the plot of the metric word size shows that 15.43 % of the total words in non-obfuscated samples and 50.35 % in obfuscated samples have a word length greater than the threshold we set. URLs and encoded strings account for the longer word length in non-obfuscated samples. The graphs also show a higher percentage of identifiers (class, method and field names) renamed in obfuscated malware. The frequency of vowels does not follow the standard frequency distribution of English words for obfuscated samples. This indicates that strings are being transformed into meaningless words by some form of encryption technique. There are also more occurrences of rare bigrams, special characters and non-readable characters in obfuscated strings. An increase in the randomness of characters in a string results in a corresponding increase in the entropy of the string. We calculated entropy for longer strings and divided the value by string length to obtain the metric entropy.


The evaluation of the performance of the four machine learning classifiers is shown in the table above. Case I comprise of total number of obfuscated samples classified as non-obfuscated and case II comprises of total number of non- obfuscated samples classified as obfuscated. Research claims that SVM is the most appropriate classifier for text- based pattern recognition and yields the smallest error rates even with a limited training set. However, we find that the Random Forest algorithm returned the best overall results in classification. The classifier correctly classified 99% of all instances with an F-score of 0.99. While SVM did not classify even a single obfuscated malware as non-obfuscated, it classified a large number of non-obfuscated samples as obfuscated. C4.5 and Random forest algorithms performed better than SVM and produced lesser number of false negatives.


<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/classifiertable.png' | relative_url }}" alt="" title="classifier accuracy"/>
    </div>
</div>
