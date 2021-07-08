---
layout: page
title: Static Control Flow Obfuscation
description: systematic study on static control flow obfuscation techniques in Java
img: /assets/img/code.png
importance: 4
category: work 
---

*Renuka Kumar, Anjana Mariam Kurian*

Control flow obfuscation (CFO) alters the control flow path of a program without altering its semantics. While many techniques exist, a quick survey reveals a lack of clarity in the number of unique techniques proposed. What is also unclear is whether there is a disparity in the theory and practice of CFO. In this work, we systematically study CFO techniques proposed for Java programs, both from papers and commercially available tools. We evaluate 13 obfuscators using a dataset of 16 programs with varying software characteristics, and different obfuscator parameters. Each program is carefully reverse engineered to study the effect of obfuscation. Our study reveals that there are 36 unique techniques proposed in the literature and 7 from tools. Three of the most popular commercial obfuscators implement only 13 of the 36 techniques in literature. Thus there appears to be a gap between the theory and practice of CFO. We propose a novel classification of the obfuscation techniques based on the underlying component of a program that is transformed. We identify the techniques that are potent against reverse engineering attacks, both from the perspective of a human analyst and an automated program decompiler. Our analysis reveals that majority of the tools do not implement these techniques, thus defeating the protection obfuscation offers. We furnish examples of select techniques and discuss our findings. To the best of our knowledge, we are the first to assemble such a research. This study will be useful to software designers to decide upon the best techniques to use based upon their needs, for researchers to understand the state-of-the-art and for commercial obfuscator developers to develop new techniques.

Find paper [here](https://arxiv.org/pdf/1809.11037.pdf).

### Approach
We assemble a list of known techniques into a single study and classify it into 5 categories based on the component (or level) of a program on which the transformation is applied. Though we use the taxonomy proposed by Colberg et al. as the basis of our research, we deviate substantially in our classification. We evaluate 13 obfuscation tools and determine that only 6 are viable for analysis. The remaining obfuscators were either unavailable for download, end-of-lived or had unresolvable program errors that made it unusable. Obfuscators used for Android programs can also be used for Java applications. However, certain Android obfuscators will not work on Java bytecode. Hence we evaluate tools that can obfuscate Java programs only.

In order to evaluate obfuscators, we choose a sample set of 16 programs with varying control flow properties. Each obfuscator is repeatedly applied to the 16 sample programs by varying their configuration parameters. The bytecode of the obfuscated program is then manually analyzed and compared with that of the original program to tabulate our findings. Wherever possible, we confirm our results with any documentation provided by the tool. We apply no lexical or data obfuscation and restrict our analysis to CFO only.

#### Tools evaluation
We identify 13 tools as listed in table 1 that support control flow obfuscation. We restrict our evaluation to control flow obfuscators and exclude lexical and data obfuscators such as Proguard. Of the 13 tools Only 6 tools are viable - DashO, Allatori, Zelix Klassmaster, Sandmark, JMOT, and JFuscator. JBCO is the only tool which we could not evaluate, yet is included since they furnish a detailed documentation of their techniques. For those that were not evaluated, we indicate why. In case of paid proprietary tools, we use their trial versions.

For evaluation, except for Zelix Klassmaster, for which we acquired a licensed version. In order to conduct the study, we use a set of 16 synthetic programs with and without control flow constructs We intended the programs to contain a heterogeneous mix of properties. The programs have one or more of the following characteristics: 1) non-nested if statements 2) non-nested if-else statements 3 one/many for or while loops 4) switch-case statements 5) nested conditional statements 6) nested try-catch blocks 7) use of complex data structures such as hashmaps 8) variable number of methods 9) accepts user inputs. In a case where the conditionals are nested, we introduce a nesting level of up to 3 levels.

The source/bytecode of each of the 16 programs are repeatedly supplied as input to each control flow obfuscator for different obfuscator parameters. We manually manually examine the bytecode output to identify the differences between the original and the transformed programs using the javap command. This is because, the decompiled output of a program may vary depending on the decompiler used. Certain obfuscations also produce programs that cannot be decompiled.

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
    <th class="tg-0lax">Obfuscator</th>
    <th class="tg-0lax">Software&nbsp;&nbsp;Version</th>
    <th class="tg-0lax">Availability</th>
    <th class="tg-0lax">Input</th>
    <th class="tg-0lax">Evaluated</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax">Zelix Klassmaster</td>
    <td class="tg-0lax">6.1</td>
    <td class="tg-0lax">Paid version</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">Allatori</td>
    <td class="tg-0lax">6.4</td>
    <td class="tg-0lax">Trial version</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">DashO</td>
    <td class="tg-0lax">8.3.0</td>
    <td class="tg-0lax">Trial version</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">JBCO</td>
    <td class="tg-0lax">2.5.0</td>
    <td class="tg-0lax">Not Available</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">JMOT</td>
    <td class="tg-0lax">3.0</td>
    <td class="tg-0lax">Open-source</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">Sandmark</td>
    <td class="tg-0lax">3.1</td>
    <td class="tg-0lax">Open-source</td>
    <td class="tg-0lax">Open-source</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">Jfuscator</td>
    <td class="tg-0lax">Unspecified</td>
    <td class="tg-0lax">Trial version</td>
    <td class="tg-0lax">Byte/Sourcecode</td>
    <td class="tg-0lax">Y</td>
  </tr>
  <tr>
    <td class="tg-0lax">JOAD</td>
    <td class="tg-0lax">2.1</td>
    <td class="tg-0lax">Open-source</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">JShield</td>
    <td class="tg-0lax">Unspecified</td>
    <td class="tg-0lax"><span style="font-weight:normal;font-style:normal;text-decoration:none">Not Available</span></td>
    <td class="tg-0lax">Source code</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">Arxan</td>
    <td class="tg-0lax">Unspecified</td>
    <td class="tg-0lax">Paid version</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">Cloakware</td>
    <td class="tg-0lax">Unspecified</td>
    <td class="tg-0lax">Not available</td>
    <td class="tg-0lax">Source code</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">Smokescreen</td>
    <td class="tg-0lax">3.11</td>
    <td class="tg-0lax">Not available</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">N</td>
  </tr>
  <tr>
    <td class="tg-0lax">Codeshield</td>
    <td class="tg-0lax">2.0</td>
    <td class="tg-0lax">Not available</td>
    <td class="tg-0lax">Bytecode</td>
    <td class="tg-0lax">N</td>
  </tr>
</tbody>
</table>
<br>
<div class="caption">
The table shows the list of obfuscator tools that we evaluated for this research. We were unable to evaluate some tools for reasons beyond our control. For instance, though JBCO has a good lineup of techniques, it has a broken code base at the time of our work. JMOT is a purely command-line tool, does not support control flow obfuscation.
</div>

### Summary of Findings
From our study, we identified a total of 43 unique techniques, 36 from literature and 7 from tools. Of the 36 techniques from literature, 12 are not implemented in any of the tools. Three of the most popular commercial obfuscators implement only 13 of the 36 techniques from the literature. Thus there appears to be a gap between the theory and practice of CFO. We assess the potency of each technique from the perspective of a human analyst and a program decompiler. We determine that techniques that are applied on a basic block are the most potent. However, majority of the techniques do not implement them, thus defeating the reliability of obfuscation. We identify the most popular techniques in tools and identify those that are incorrectly cited as control flow obfuscating.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/obfsummary.png' | relative_url }}" alt="" title="summary of obfuscation techniques"/>
    </div>
</div>
<div class="caption">
The table furnishes a classification of the techniques based on the component (or level) of a program at which an obfuscation transformation is applied. We identify 5 different components (or levels) of a program (column 1) - Expres- sion (such as conditional expressions), Statement, Basic Block, Method, Class.
</div>

The table above furnishes a classification of the techniques based on the component (or level) of a program at which an obfuscation transformation is applied. We identify 5 different components (or levels) of a program (column 1) - Expression (such as conditional expressions), Statement, Basic Block, Method, Class. We indicate whether the technique is proposed in the literature, tools or both (columns 3-5). In column 6, we note whether these techniques allow a program to be decompiled into a source code equivalent, thus facilitating reverse engineering. Finally, we also note the obfuscating paradigm used in column 7. 

Basic block transformations are difficult to implement due to the challenge entailed in preserving inter-block dependencies. However, they are also the most potent. Any technique that gen- erates a non-structured control flow at the bytecode level cannot be decompiled. We identify that the following 7 techniques causes a decompiler to fail - Goto Instruction Augmentation, Conversion of Reducible to Irreducible Flow Graphs, Intersecting Loops, Basic Block Fission, Partially Trapping Switch Statements, Disobeying Constructor Conventions and Indirecting if Instructions. Loop block- ing, though it can be decompiled, is potent, as it makes use of nested loops. Nested loops make use of nested labels and gotos that deters the readability of a program for a human analyst.

Of the 6 tools we evaluated, Zelix, Allatori, DashO and JMOT can be used by software developers to control flow obfuscate their code. We determine that Zelix is the most potent as it is the only tool that implements language breaking transformations. Though Allatori and DashO are comparable in their implementations, programs generated by Allatori mask their obfuscation techniques well. DashO relies on try-catch blocks (TCB). They insert a varying number of TCBs depending on the level of obfuscation. This approach does not substantially mar the readability of a program. Although Sandmark provides the most number of techniques, it is not a practically viable tool for commercial purposes. This is because, the tool is no longer supported and not tested for versions of Java greater than 1.4. Each tool has a unique way of inserting dead code, which is useful to identify the obfuscator. 

The following techniques are common to most tools - Extending Conditionals, Adding Redundant Operands, Opaque Predicate Insertion, Dead Code Insertion, Irrelevant Code Insertion, Reordering Expressions, Reordering Statements, Re- placing if(non)null instructions with try-catch blocks, and Goto Augmentation.
