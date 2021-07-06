---
layout: page
title: Similarity analysis of Android apps
description: towards accuracy in similarity analysis of Android apps
img: /assets/img/similarityarch.png
#redirect: https://unsplash.com
importance: 3
category: work
---

*Sreesh K., Renuka Kumar, Sreeranga Rajan*

Android malware is most commonly delivered to a user through the many open app marketplaces. Several recent attacks have shown that the same malware infects different apps in the app market. Automated triaging by computing similarity of apps to known software components can help learn the evolution and propagation of malware. While the emphasis of existing research is on detecting repackaged apps, a similarity analysis system that can identify similar portions of code in dissimilar apps, is important. Only few public tools exist that furnish these details accurately. In this research, we develop a proof-of-concept of an analysis system that compares Android apps using a technique that combines class and method features of an app. We use a two-step process that first compute similar classes and then compute similar methods of those classes. To identify similar classes, we propose a novel set of software birthmarks. We use Normalized Compression Distance to compute similar methods. The birthmarks are evaluated on a set of over 65,000 classes from 60 APKs. To evaluate the performance of our tool, we establish ground truth by manually reverse engineering each app. The proposed system is compared with Google’s *androsim* (an archived project now), the only open-source tool for similarity analysis that also uses NCD. Our approach shows an improvement in accuracy in the worst-case when comapred to *androsim*. Finally, we furnish a case-study of our system to detect fake and repackaged apps by analyzing 1470 Android apps from various sources.

Find [paper](https://link.springer.com/content/pdf/10.1007%2F978-3-030-05171-6.pdf) here. Github link to [code](https://github.com/sreeshk692/SimDroid).


## System Design

The architecture of our system is shown in figure below. We want our analysis system to have the following capabilities:
<ul>
    <li><b>Resilience to lexical obfuscation:</b>Lexical obfuscation of apps must have no impact on analysis. Common control flow obfuscation constructs may have an impact as the control flow of a program is modified.</li>
    <li><b>Detecting plausible attack through third-party libraries:</b>We include third-party libraries in our analysis since the libraries may themselves be an attack vector. This could however boost similarity values.</li>
    <li><b>Resilience to mutable elements:</b>System must be resilient to changes to values of variables (for example, hard-coded strings) across app revisions.</li>
    <li><b>Neutrality to the order of inputs:</b>The results of analysis must be independent of the order of inputs of the APKs.</li>
</ul>
The following are not goals of our prototype system currently, though follow up research could consider one or more of these:
<ul>
    <li><b>Analysis of native code:</b>Native code is excluded from our analysis. Datasets collected from prior research show that only a very small percentage of apps contain native code.</li>
    <li><b>Analysis of Multiple DEX files:</b>There may be more than one DEX file packaged into an APK as a work-around to a hard-limit of 64K methods allowed in a DEX file. In this case, only one DEX file is analyzed. Optimized DEX files (ODEX files) are also excluded from the scope of our analysis.</li>
    <li><b>Detecting or Reversing Obfuscation:</b>Comparing two similar APKs that are obfuscated differently may result in a low similarity value. Though we are yet to see this case, we do not discount the possibility. Yet, detecting or reversing obfuscation is not a goal of this research.</li>
</ul>

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/similarityarch.png' | relative_url }}" alt="" title="architecture of similarity analysis engine"/>
    </div>
</div>
<div class="caption">
	Our system has three main modules---the APK extractor, the Static Analyzer, and the Correlation Engine. 
</div>

### APK Extractor
APK extractor is an amalgamation of a Python script, a custom Android app and a crawler. The script extracts APKs from various downloaded custom firmwares, the Android app extracts privileged (or pre-installed) and secondary (or user) APKs from any Android device and the crawler downloads APKs from app market places.

### App Repositories
They are centralized databases where APKs are stored for analysis. There are two types of repositories-baseline and test. The baseline repo contains APKs used as baselines for analysis and test repo maintains APKs that are to be evaluated. Baseline repo includes apps downloaded from Google Play, official app websites and Nexus devices. Apps from Nexus devices serve as baselines to analyze system apps such as Contacts, Dialer, etc. Prior research has also shown that Nexus apps are comparatively the safest based on three metrics-proportion of devices free from known critical vulnerabilities, proportion of devices updated to the most recent version, the number of vulnerabilities the manufacturer has not yet fixed on any device. We thus use it as the baseline for comparing system apps.

### Static Analyzer
The static analysis engine is a toolkit written in Python and consists of three components-Meta-info extractor, Class birthmarker and NCD computation engine.

#### Meta-info Extractor
Data that describes an APK is called its meta-information. This includes details such as developer information, version number, public key signature, size and permissions of the apps. The primary use of this component is to extract meta-information for use in our case-study.

#### Class Birthmarker
This component categorizes classes as similar or different based on their birthmarks. Class birthmarks are properties of a class that are invariant across any transformation applied to it. The transformation may be in the form of obfuscation, incremental updates, or augmented functionality. These classes need not be identical and a hash of the two classes will yield different values. We use the following birthmarks to determine similar classes:
1. Used Classes (UC): They are classes used within a class to implement a functionality.
2. Field Variable Type (FVT): The (data) types of the field variables used in a class.
3. Method Descriptor (MD): This is a string that represents the method, its parameters and return types.

A match percentage for each birthmark B is computed as (2B)/(Total number of all birthmarks of each type). We start with a fuzzy match threshold of 50% for each birthmark to determine similarity of classes. We do not use set intersection to compute the match percent since taking an intersection will remove repeated birthmarks.


#### NCD Computation Engine

Normalized Compression Distance (NDC) is the computable form of Kolmogorov complexity and lies in [0, 1]. Kolmogorov complexity K(x) of an object(string) is defined as the length of the shortest program that represents the object. In practice, Kolmogorov complexity is non-computable and NCD makes Kolmogorov complexity tractable. Compression algorithms are used to compute NCD. The NCD computation engine computes NCD of methods of similar classes on a signature string that describes a method. We use androsim’s grammar as the foundation for this work and modify an app's  method features to include additional information such as method descriptor, types of invoke instructions, return values and exceptions.

#### Correlation Engine
Correlation Engine is a complex event processing engine developed using a tool called ESPER that triggers an alert when a fake or repackaged app has been spotted. This is done by correlating the meta-information of apps with similarity metric. The engine was implemented solely for the case-study on detection of fake and repackaged apps.

#### Evaluation (Short)
We compare 470 apps-popular apps with their similar variants, popular apps with known fake variants and system apps with apps from other phones. For each comparison, we manually confirm the results of analysis. Table 3 shows select results for this discussion.

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0lax">Sl.#</th>
    <th class="tg-0lax">APK Name</th>
    <th class="tg-0lax">Baseline<br>Version</th>
    <th class="tg-0lax">Test<br>Version</th>
    <th class="tg-0lax">Baseline<br>Size</th>
    <th class="tg-0lax">Test<br>Size</th>
    <th class="tg-0lax">Similarity<br>Our System</th>
    <th class="tg-0lax">Similarity<br>Andosim</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax">1.</td>
    <td class="tg-0lax">Facebook</td>
    <td class="tg-0lax">77.0.0.20.66<br>(Google Play)</td>
    <td class="tg-0lax">77.0.0.20.66<br>(APK Pure)</td>
    <td class="tg-0lax">45.6</td>
    <td class="tg-0lax">38.4</td>
    <td class="tg-0lax">99.99</td>
    <td class="tg-0lax">99.99</td>
  </tr>
  <tr>
    <td class="tg-0lax">2.</td>
    <td class="tg-0lax">Chrome</td>
    <td class="tg-0lax">50.0.2661.89<br>(Google Play)</td>
    <td class="tg-0lax">50.0.2661.89<br>(APK Pure)</td>
    <td class="tg-0lax">59.1</td>
    <td class="tg-0lax">59.1</td>
    <td class="tg-0lax">100</td>
    <td class="tg-0lax">100</td>
  </tr>
  <tr>
    <td class="tg-0lax">3.</td>
    <td class="tg-0lax">Chrome</td>
    <td class="tg-0lax">49.0.2623.105<br>(Nexus)</td>
    <td class="tg-0lax">49.0.2623.91<br>(Moto G)</td>
    <td class="tg-0lax">59.1</td>
    <td class="tg-0lax">59.2</td>
    <td class="tg-0lax">99.98</td>
    <td class="tg-0lax">99.98</td>
  </tr>
  <tr>
    <td class="tg-0lax">4.</td>
    <td class="tg-0lax">Wallpaper</td>
    <td class="tg-0lax">5.1.1-2237560<br>(Nexus 4)</td>
    <td class="tg-0lax">6.0-24<br>(Moto G)</td>
    <td class="tg-0lax">1.67</td>
    <td class="tg-0lax">0.09</td>
    <td class="tg-0lax">0.38</td>
    <td class="tg-0lax">69.45</td>
  </tr>
  <tr>
    <td class="tg-0lax">5.</td>
    <td class="tg-0lax">Clock</td>
    <td class="tg-0lax">4.3(2552012)<br>(Nexus 4)</td>
    <td class="tg-0lax">4.3(2552012)<br>(Moto G)</td>
    <td class="tg-0lax">7.1</td>
    <td class="tg-0lax">7.1</td>
    <td class="tg-0lax">100</td>
    <td class="tg-0lax">100</td>
  </tr>
  <tr>
    <td class="tg-0lax">6.</td>
    <td class="tg-0lax">Calculator</td>
    <td class="tg-0lax">6.0(2715628)<br>(Nexus 4)</td>
    <td class="tg-0lax">6-1.0(Mi3)</td>
    <td class="tg-0lax">0.953</td>
    <td class="tg-0lax">0.203</td>
    <td class="tg-0lax">3.24</td>
    <td class="tg-0lax">42.21</td>
  </tr>
</tbody>
</table>
<br>
(#1) is a comparison of Facebook APKs of the same version from different app markets. Despite a 7MB difference in sizes, both the tools report 99.99% similarity on the apps. However, while androsim reported 4627 identical methods, 1 similar method and no new or deleted methods, the proposed system reported 7355 identical methods (the remaining numbers are the same). Our system detects more identical methods due to the class birthmarking feature. The difference is sizes is due to additional resource files in the larger APK.

(#2) shows a comparison of identical versions of Chrome APKs from different app stores. In this case, both the tools report a 100% similarity. (#3) is a comparison of Chrome extracted from a Nexus device with another extracted from MotoG. The APKs are of different versions and approximately the same size yielding an average similarity of 99.97%. 

(#6) is an example of a system app, from MotoG and Nexus, but of similar versions. In this case, the APKs show 100% similarity, which implies that the firmware of MotoG may have been inherited from Android stock ROM. 

(#7) shows a comparison of Calculator APK from an MI3 device with that of Nexus. Despite the comparable versions, the similarity score of 3.24% shows that MI3 apps may have no bearing on the apps on Nexus devices.

### Summary
We developed an analysis system to accurately compute similarity of Android apps even when the apps are disparate. The proposed analysis is two fold-first we identify similar classes and then we identify methods of similar classes. Similar classes are determined using class birthmarks and methods of similar classes are compared using Normalized Compression Distance applied on signature strings generated using a grammar. An evaluation of the birthmarks show a precision of 45.5% in detecting similar classes. Our proposed system shows 100% precision in detecting identical methods of similar classes. This is a substantial improvement in the worst-case over Google’s androsim. 
