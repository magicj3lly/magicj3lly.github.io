---
layout: page
title: Intel SGX-Based Malware
description: exploring the potency of SGX-based malware
img:
importance: 6
category: fun
---

*Thesis submitted by [Shredha Kishore](https://in.linkedin.com/in/shredha-kishore-6262b6a1) for her master's degree at VU in 2017, as a part of her dual master's degree with VU and Amrita University. I was a research faculty at Amrita then. This research idea was conceived at VU and major part executed there using their infrastructure. I assisted with the engineering aspects of this work.*

*Supervised by: Herbert Bos, Cristiano Giuffrida, Radhesh Krishnan, and Renuka Kumar*

This work focuses on how malware authors can use Intel SGX to develop stealth malware that are hard to reverse engineer. We assume an attacker that only has user-level access privileges. The attacker also has to operate in a constrained environment provided by Intel SGX i.e, all the security features of an SGX system are enabled. In such a case, the malware will not be able to execute any system calls inside the isolated execution environment. This work explores the following research questions:
1. Given Intel SGX’s constrained environment, how can malware authors build a more potent malware?
2. What features of the Intel SGX system can be exploited to enable any malware to execute in a trusted environment?
3. Can any existing malware be modified to run inside an Intel SGX system?
4. What techniques can an attacker employ to conceal attack code inside an Intel SGX trusted environment?

## Approach Summary
This work surveyed two existing trojan malware-Zeus and Torpig-to study their attack vectors and identify their attack code features such as DGA code, stalling code etc. Based on our findings from tthese malware, we showed two approaches to develop an SGX malware-

1. As for any SGX application, the malware is split into a trusted and untrusted component, with the attack code executed as a trusted component. Additionally, the attack code must be rewritten without using system calls 
2. The malware as a whole is executed inside the trusted environment. To do this, we use the SCONE framework. SCONE takes application source code containing system calls and generates the corresponding SGX enabled binaries. SCONE framework works around the limitations of Intel SGX that prevents execution of system calls, by allowing a separate thread to execute system calls outside the trusted environment. This leads us to explore the feasibility of converting any malware to its SGX-based variant. We investigate two approaches- 1) converting a malware binary to an SGX compatible binary 2) converting the source code of a malware to generate an SGX based variant. We find that the former approach is infeasible since there are several differences between the generated binaries, and automating address relocation was beyond the scope of this thesis. Hence, we implement a tool that takes as input any malware source code and converts it to its corresponding Intel SGX variant. However, SCONE by default does not have the provision for remote (software) attestation. We modified the SCONE framework to support remote attestation. 
3. To conceal a malware‘s attack code to make it stealthy, we use the remote attestation feature of Intel SGX to provision a malicious secret on behalf of the attacker. We explored two approaches - 1) The entire malicious payload is delivered to the infected machine as the secret 2) The attack code is encrypted and the key to decrypt it is delivered as a secret. The concealing techniques alongside Intel‘s security features, results in a malware that is more potent and difficult to reverse engineer. 

The proposed approaches, alongside Intel‘s security features, results in a malware that is more potent and difficult to reverse engineer. We find that obfuscation increases the stealth of SGX malware.
