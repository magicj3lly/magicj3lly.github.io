---
layout: page
title: Security Analysis of UPI
description: security analysis of India's new mobile payments infrastructure
img: /assets/img/upiattack.png
importance: 2
category: work
---

Since 2016, with a strong push from the Government of India, smartphone-based payment apps have become mainstream, with billions of dollars transacted through these apps. Many of these apps use a common infrastructure introduced by the Indian government, called the Unified Payments Interface (UPI), that facilitates instant and free bank-to-bank  micropayments at scale.  In this research we conduct a detailed security analysis of the UPI protocol, an unpublished application layer protocol, by reverse-engineering its design through seven popular UPI apps that have a combined market share of about 90%. We discover previously-unreported multi-factor authentica- tion design-level flaws in the UPI 1.0 specification that can lead to significant attacks when combined with an installed attacker-controlled application. In an extreme version of the attack, the flaws could allow a victim’s bank account to be linked and emptied, even if a victim had never used a UPI app. The potential attacks were scalable and could be done remotely. This work resulted in several CVEs, and the National Payments Corporation of India addressed a key attack vector that we reported in UPI 2.0.

### Objectives and Approach

A key challeng in analyzing UPI 1.0 is that the protocol details are not available, though millions of users in India use it. We also did not have access to the UPI servers. We thus had to reverse-engineer the UPI protocol through the UPI apps that used it and had to bypass various security defenses of each app, including code obfuscation and anti-emulation techniques. Thus, our approach to extract the protocol details varies based on the defenses the apps use. We aimed to do the following:

1. Uncover the client-server handshake step-by-step
2. Collect from each step credentials required and any leaked user-specific attributes
3. Find alternate workflows that can be exploited 
4. Triage the findings to determine plausible attack vectors

***Setup:*** To extract UPI protocol, we use India's flagship app, BHIM, which was released along with UPI as the references implementation of a mobile payments app that uses UPI. We instrument and repackage BHIM to extract the client-server handshake between the app and the UPI server, and to map the GUI of the app that generates the traffic. Once the details have been extracted, we confirm our findings on other popular app. The set of all apps we studied is shown below. We study the Android versions of these apps. We re-examined our findings when UPI 2.0 was released. Amazon Pay was not available on Play Store at the time of this study.

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
    <th class="tg-0lax">App Name</th>
    <th class="tg-0lax">Launched</th>
    <th class="tg-0lax">Versions</th>
    <th class="tg-0lax">Installs</th>
    <th class="tg-0lax">Rating</th>
    <th class="tg-0lax">UPI Version</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax">BHIM</td>
    <td class="tg-0lax">Dec, 2016</td>
    <td class="tg-0lax">1.3, 1.4, 1.5</td>
    <td class="tg-0lax">10M+</td>
    <td class="tg-0lax">4.1</td>
    <td class="tg-0lax">1.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">Ola Money</td>
    <td class="tg-0lax">Nov, 2015</td>
    <td class="tg-0lax">1.8.1, 1.8.2, 1.9.0</td>
    <td class="tg-0lax">1M+</td>
    <td class="tg-0lax">3.8</td>
    <td class="tg-0lax">1.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">Phonepe</td>
    <td class="tg-0lax">Dec, 2015</td>
    <td class="tg-0lax">3.0.6, 3.3.23</td>
    <td class="tg-0lax">100M+</td>
    <td class="tg-0lax">4.5</td>
    <td class="tg-0lax">1.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">SamsungPay</td>
    <td class="tg-0lax">Aug, 2016</td>
    <td class="tg-0lax">2.8.49, 2.9.3</td>
    <td class="tg-0lax">50M+</td>
    <td class="tg-0lax">4.7</td>
    <td class="tg-0lax">1.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">Paytm</td>
    <td class="tg-0lax">Aug, 2010</td>
    <td class="tg-0lax">8.2.12</td>
    <td class="tg-0lax">100M+</td>
    <td class="tg-0lax">4.4</td>
    <td class="tg-0lax">2.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">Google Pay (Tez)</td>
    <td class="tg-0lax">Sept, 2017</td>
    <td class="tg-0lax">39.0.001</td>
    <td class="tg-0lax">100M+</td>
    <td class="tg-0lax">4.4</td>
    <td class="tg-0lax">2.0</td>
  </tr>
  <tr>
    <td class="tg-0lax">Amazon Pay</td>
    <td class="tg-0lax">Feb, 2019</td>
    <td class="tg-0lax">18.15.2</td>
    <td class="tg-0lax"></td>
    <td class="tg-0lax"></td>
    <td class="tg-0lax">2.0</td>
  </tr>
</tbody>
</table>

## About UPI

The Unified Payments Interface is backend infrastructure that facilitated free and instant micropayments at scale from a mobile platform. Prior to UPI, Indian payment apps were predominantly wallet applications, where in a user deposited money to a wallet hosted by a service provider and the money transfer was carried out wallet-to-wallet. Besides payments via wallet apps, banks in India also provided Internet banking services with select ecommerce sites providing payments via internet banking. Figure below shows the differences between Internet banking and UPI-based banking.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/internetbanking.png' | relative_url }}" alt="" title="internet banking"/>
    </div>
    <div class="col-sm mt-2 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/upitransfer.png' | relative_url }}" alt="" title="upi transfer workflow"/>
    </div>
</div>
<div class="caption">
    On the left is shown how traditional internet banking is carried out between Alice and Bob. Alice enter Bob's bank account number and IFSC code to transfer money. In UPI, Alice enters Bob's UPI ID, which in majority cases is a user's cell number. Internall, UPI maps the UPI ID to Bob's bank account number and IFSC code to initiate a bank-to-bank transfer. In case of payment apps, the transfer happens wallet to wallet. 
</div>

### Known Guidelines

The National Payments Corporation of India has published a few "broad" guidelines for users of UPI. The UPI payment system requires Alice to register her primary cell number with her bank account(s) out-of-band to send or receive money. UPI uses the cell number (i) as a proxy for a user’s digital identity with the bank to look up a bank account given a cell number; (ii) as a factor in authentication via SMS one-time passcodes (OTP); and (iii) to alert users on transactions. To register for UPI services, Alice must set up her UPI user profile, add a bank account, and enable transactions on that bank account. 

#### Setup User profile
To set up her UPI user profile, Alice must first create a profile with UPI via a UPI app installed on her bank-registered cell phone. 

**Factor 1: Device fingerprint.** Once a UPI app gets a user’s cell number, the app must send an outbound encrypted SMS from Alice’s phone to the UPI server. This process is automated and does not involve the user in order to guarantee a strong association between a user’s cell phone and her device. According to UPI, this is the “most critical security requirement” of the protocol since all money transactions from a user’s device are first verified based on this association. UPI calls this association of a user’s device with her cell number as device hard-binding. The combined cell number and device information (that represents this binding) is called the device fingerprint, which per the UPI spec is the first factor of authentication.

**Factor 2: Passcode.** The UPI spec considers application passcode as optional and leaves it up to a UPI app vendor to authenticate the passcode. However, most apps make use of a passcode. For instance, BHIM uses a 4-digit passcode.

#### Add Bank Account
A user’s request to add a bank must be from the device registered with UPI. Internally, UPI fetches the chosen bank’s account number and IFSC code based on a user’s cell number for later transactions through the UPI app.

#### Authorize Transactions
For Alice to be able to transact on an added bank account, she has to set up a UPI PIN for that account before the first transaction. The UPI PIN is Alice’s secret to authorize any future transactions. To set the UPI PIN, Alice must furnish information printed on the debit card— the last six digits of her bank’s ATM or debit card number and expiration date. Alice must also enter an OTP she receives from the UPI server. The UPI PIN is a highly sensitive factor since the UPI server uses it to prevent unauthorized transactions on Alice’s bank account.

Thus, the responsibility to completely authenticate a user is shared between two servers---the UPI server (that verifies device fingerprint and UPI PIN), and a payment app server (that verifies an app passcode).

### Threat Model
We assume a normal user, Alice, who installs payment apps from official sources such as Google Play. Alice has a properly configured phone with Internet facility and prevents physical access to it by untrusted parties. On the other hand, the attacker, Eve, uses a rooted phone. Eve can use any tool at her disposal to reverse engineer the payment apps. We assume that Eve releases an apparently useful unprivileged app called Mally that requests the following two permissions—android.permission.INTERNET and android.permission.RECEIVE_SMS. Alice finds the app useful and installs it, granting it the necessary permissions.

We consider our threat model to be realistic for the following reasons. First, according to the Android security review for the last two years, India is among the top three countries with the highest rate of potentially harmful applications such as trojans and backdoors, sometimes pre-installed on Android devices. Google has also recently released a warning stating that 53% of the major attacks are because of malicious apps that come pre-installed on low-cost smartphones.

### Attack Example
In the attack example, an attacker is able to compromise an existing user's BHIM account on attacker's phone.
<blockquote>Unlike mobile wallets where money may only be lost from the wallet, here the attacker can empty an existing user’s, new user's, or non-UPI user's bank account.</blockquote>

#### Attack Pre-conditions
Prior to the attack:
1. The attacker disables BHIM’s client-side defenses and installs repackaged version of BHIM.
2. The victim's device is already compromised with the attacker's trojan app.
3. The attacker learns of a user's cell number. While the cell number is not a secret and is widely circulated in India, the attacker can get the cell number starting with no knowledge of a user.

#### Attack Step-by-Step
The attack starts with the attacker trying to set up BHIM as a new user on an attacker-controlled phone.
<ul>
<li> Figure below shows UPI's default workflow, as we extract from BHIM.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/devicebinding.png' | relative_url }}" alt="" title="device binding workflow"/>
    </div>
</div>
</li>

<li> From an attacker standpoint, Step 2 of the device hard-binding workflow is hard to compromise since to compromise the send SMS functionality, the attacker has to bypass protections provided by a user's cell phone company. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/step2-2.png' | relative_url }}" alt="" title="step 2 of workflow"/>
    </div>
</div>
</li>

<li> We found an alternate workflow that will allow an attacker to bind her cell phone with a cell number registered to bank account of another user. ***An attacker can induce a failure in Step 2 of the protocol by turning on airplane mode.***

<blockquote>Alternate workflow may allow an attacker to bind her cell phone with a cell number registered to bank account of another user</blockquote>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/step2-1.png' | relative_url }}" alt="" title="step 2 of workflow"/>
    </div>
</div>

</li>

<li><b>Breaking device-binding:</b> Once send SMS fails, BHIM provides attacker an option to enter a cell number. The attacker enters Alice's cell number.
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/step3.png' | relative_url }}" alt="" title="step 3 of workflow"/>
    </div>
</div>

<blockquote>To break device binding, attacker only needs a user's cell number and one OTP from that number</blockquote>
</li>

<li><b>Leak passcode:</b> Once the attacker's device is bound to Alice's cell number, attacker is prompted for Alice's passcode. The trojan app on Alice's device aids the attacker by stealing the passcode. The paper talks about different ways to do this. One approach is to draw an overlay screen on BHIM's passcode entry screen. The stolen password is sent to the attacker.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/step4.png' | relative_url }}" alt="" title="step 3 of workflow"/>
    </div>
</div>

<blockquote>The attacker is never prompted for a bank-related secret at any point in the user registration workflow</blockquote>
</li>
<li><b>Add bank account:</b> Once the attacker's enters the password, the attacker is prompted to choose a bank account from a list of banks provided by UPI. Attacker can simply brute force through the banks. UPI server does not appear to prevent brute force attacks. Even without it, an attacker can simply start with the most popular banks.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/step5.png' | relative_url }}" alt="" title="step 3 of workflow"/>
    </div>
</div>

<blockquote> For an existing user of UPI, the user's account is sync'ed on the attacker's phone. A new user or a non-user of UPI is equally at risk given that UPI switch is enabled by default on the server-side.</blockquote>
</li>

<li><b>UPI PIN</b> The UPI PIN required to authorize transactions can be leaked the same way as the passcode. That apart, setting the UPI PIN only requires a user's last six digits of debit card number. This is a lower bar in India given that online or in-store transactions requires the complete debit card number and the PIN. Debit cards are also frequently handed out in stores and restaurants at the time of payments.
<blockquote>Setting UPI PIN requires only partial debit card info and NO secret - a lower bar in India</blockquote>
</li>
</ul>

### FAQ
1. Is UPI 2.0 secure?
UPI is an unpublished protocol and relies on security by obscurity in its design. While NPCI has addressed a core vulnerability, we believe that the protocol needs further security vetting. Other threat model have to be considered such as SIM card cloning, theft of phone etc. 

2. Do the attacks have to be synchronous?
No, the attacker can carry out the attack at any time

3. Were the vulnerabilities disclosed?
All our vulnerabilities were responsibly disclosed via multiple channels. We withheld the publication for two years to ensure sufficinet time was given to address the issues we found.

4. Is device-binding really required?
Yes. Device binding makes the attacks harder to execeute. Without it, the UPI app is an open playground.

5. UPI workflow leaks the bank account number of a user. Is that a security risk?
Besides privacy implications, leaking the bank account here is a problem since it can be used to reset UPI PIN.


