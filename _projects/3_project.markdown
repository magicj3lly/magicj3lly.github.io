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

