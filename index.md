## TL;DR

Cross-transport key derivation (CTKD) is a Bluetooth standard security
mechanism allowing to pair two devices over Bluetooth Classic (BT) and
Bluetooth Low Energy (BLE) at the same time. CTKD was introduced in 2014 with
Bluetooth v4.2 as a usability feature. However, no research work analyzed the
security implications introduced by CTKD, despite its critical role for
Bluetooth security. For example, CTKD, by design, allows crossing the security
boundary between BT and BLE as security keys for both transports are generated
trusting only one transport and is completely transparent to the end-user.

In this work, we provide the first security evaluation of CTKD, and we
uncover previously unknown issues in its specification. For example, CTKD 
introduces a new way to pair two devices that can be exploited in several ways,
and it allows to tamper with Bluetooth security keys across BT and BLE. The
issues affect all devices supporting CTKD regardless of their Bluetooth version
(e.g. v4.2, 5.0, 5.1, 5.2) and security modes (e.g., Secure Connections, SSP,
strong association). To demonstrate the severity of the uncovered issues, we
successfully conducted high-impact attacks exploiting CTKD. Our attacks are the
first cross-transport attacks in the Bluetooth context. The attacks enable
impersonating arbitrary devices,  man-in-the-middle devices, and stealthily
establishing unintended BT and BLE sessions as anonymous devices.

* ***Security Impact:*** device impersonation, man-in-the-middle, malicious
  session establishment with arbitrary devices
* ***Affected Devices:*** the attack is standard compliant, so all BT/BLE
  devices supporting CTKD are likely vulnerable; all our tested devices are
  vulnerable
* BLURtooth is tracked under [CVE-2020-15802](https://kb.cert.org/vuls/id/589825)
* ***Credit:*** Daniele Antonioli and Mathias Payer
  from École Polytechnique Fédérale de Lausanne (EPFL),
  Nils Ole Tippenhauer from Helmholtz Center for Information Security (CISPA),
  and Kasper Rasmussen from University of Oxford.
* ***Contacts at EPFL:***
  [Daniele Antonioli and Mathias Payer](mailto:daniele.antonioli@epfl.ch,mathias.payer@nebelwelt.net)


## Summary

Here, we provide more details about a set of novel and standard-compliant
Bluetooth vulnerabilities affecting both Bluetooth Classic (BT) and Bluetooth
Low Energy (BLE).  The uncovered vulnerabilities affect a security mechanism
called cross-transport key derivation (CTKD). CTKD is used to improve the
usability of Bluetooth pairing by allowing to generate BT and BLE pairing keys
just by pairing two devices either on BT or BLE (rather than pairing them two
times).

However, we find that CTKD introduces cross-transport security issues and that
an attacker can abuse those issues to attack BT from BLE and vice versa.  In
particular, our attacks enable to impersonate, man-in-the-middle, and establish
malicious sessions with arbitrary devices by abusing CTKD, while defeating all
the security mechanisms put in place by BT and BLE.  Our work is named BLURtooth
and the related attacks are called BLUR attacks as they blur the security
boundary between BT and BLE.

The team behind this work consists of
[Daniele Antonioli](https://francozappa.github.io/)
and
[Mathias Payer](https://nebelwelt.net/)
from the [HexHive group](https://hexhive.epfl.ch) at
École Polytechnique Fédérale de Lausanne (EPFL),
[Nils Ole Tippenhauer](https://tippenhauer.de/)
from Helmholtz Center for Information Security (CISPA), and
[Kasper Rasmussen](https://www.cs.ox.ac.uk/people/kasper.rasmussen/)
from the University of Oxford.

In the remainder of this document, we provide information on
technical details, disclosure, impact, our proposed mitigation, the response
from the Bluetooth SIG.


## Technical Details

The Bluetooth standard specifies two transports: *Bluetooth Classic (BT)* for high-throughput wireless services and *Bluetooth Low Energy (BLE)* for very low-power scenarios. BT and BLE have dedicated pairing protocols and devices have to pair over BT and BLE to use both securely. In 2014, the Bluetooth standard (v4.2) addressed this usability issue by introducing Cross-Transport Key Derivation (CTKD). CTKD allows establishing BT and BLE pairing keys just by pairing over one of the two transports. While CTKD crosses the security boundary between BT and BLE, little is known about the internals of CTKD and its security implications.

In this work, we present the first complete description of CTKD obtained by merging the scattered information from the Bluetooth standard with the results from our reverse-engineering experiments. Then, we perform a security evaluation of CTKD and uncover four cross-transport issues in its specification. We leverage these issues to design four standard-compliant attacks on CTKD enabling new ways to exploit Bluetooth (e.g., exploiting BT and BLE by targeting only one of the two). Our attacks work even if the strongest security mechanism for BT and BLE are in place, including Numeric Comparison and Secure Connections. They allow to impersonate, man-in-the-middle, and establish unintended sessions with arbitrary devices. We refer to our attacks as BLUR attacks, as they blur the security boundary between BT and BLE. We provide a low-cost implementation of the BLUR attacks and we successfully evaluate them on 14 devices with 16 unique Bluetooth chips from popular vendors. We discuss the attacks' root causes and present effective countermeasures to fix them. We disclosed our findings and countermeasures to the Bluetooth SIG in May 2020 (CVE-2020-15802), and we reported additional unmitigated issues in May 2021. 

Those are very serious attacks that violate the security guarantees promised by
Bluetooth.  We confirmed the feasibility of our attacks by testing them on 16
common Bluetooth devices using 14 unique Bluetooth chips. All of them were
vulnerable.

You will find technical details about CTKD, our security analysis, a detailed
discussion of the threads, a discussion, and potential mitigations in our
[BLURtooth preprint](https://arxiv.org/abs/2009.11776).


## Disclosure

We discovered the vulnerability in March 2020 and responsibly disclosed our
findings along with suggested countermeasures to the Bluetooth SIG in May 2020.
We kept our findings private and the Bluetooth SIG publicly disclosed them,
without informing us, on the 10th of September of 2020.  Our work is assigned
*[CVE-2020-15802](https://kb.cert.org/vuls/id/589825)*. We reported additional
unmitigated issues in May 2021 and, in November 21 are still waiting for the
SIG's reply.


## Impact

The BLUR attacks are a *significant threat for all Bluetooth users and
the related vulnerabilities remain 0-days*. Our claim
is backed up by our experimental results where we successfully conducted
impersonation, man-in-the-middle, and malicious sessions establishment attacks
on 13 different devices. Our device sample include manufacturers such as
Dell, Google, Lenovo, Samsung, and Sony, operating systems, such as Windows
10, Linux, and Android, and Bluetooth chip manufacturers such as Cypress,
Qualcomm, Intel, Broadcom, and Cambridge Silicon Radio (CSR).


## Our Mitigations

As part of our disclosure, we provided *concrete fixes to combat the BLUR
attacks*. In particular, we recommended disabling the capability to overwrite
keys via CTKD in certain circumstances, enforce strong association and Secure
Connections and roles across BT and BLE, disable pairing over BT and/or BLE when
not needed, and add user notifications in case of odd behaviors. Our fixes can
be implemented at the standard level and do not require vendor-specific
features.


## Response from the Bluetooth SIG

At the time of writing, there are *no deployed patches* to address the BLUR
attacks on actual devices.  The Bluetooth SIG suggested that version 5.1 of the
standard will contain guidelines to mitigate the BLUR attacks (e.g., disable key
overwrites in certain circumstances as proposed in our countermeasures), but
such guidelines are not (yet) public and we cannot comment on them.  The
Bluetooth SIG provides a [public statement on the BLUR
attacks](https://www.bluetooth.com/learn-about-bluetooth/bluetooth-technology/bluetooth-security/blurtooth/).


## Publication efforts

We have submitted the paper several times and are including the full set of
reviews on this page. This may in fact be the hardest paper we've ever worked
on given the amount of resistance, randomness, and ghosting we received from
reviewers. While the first rejection from CCS2020 clearly improved the paper,
the last rejected focused primarily on small language issues and ever-extending
our set of evaluated devices. As can be seen in the diff, each time we
carefully addressed the reviewer feedback and rewrote large sections of the
paper.

The major changes between each version are the following:

* **SP22:** deeper analysis of CTKD issues; explain that we empirically verified
  our claims; test mitigations and countermeasures. We did not receive a
  response to our rebuttal.
* **NDSS22:** Given the lack of response from the CCS21MR, we focused on further
  refining the arguments and interacted with the Bluetooth SIG to clarify the
  still existing vulnerabilities. We did not receive a response to our rebuttal.
* **CCS21MR:** buy and evaluate Bluetooth 5.2 devices (that are still
  vulnerable) expand and evaluate counter measures; justify why and how the
  mitigation that was added to the standard in response to our disclosure is
  incomplete improve presentation and rewrite large parts to clarify as
  indicated by the reviewers. We did not receive a response to our revised
  manuscript.
* **CCS21:** we describe CTKD in non-adversarial settings, reimplement the CTKD
  derivation function for further analysis, and simplify the presentation of
  the BLUR attacks.
* **SEC21MR:** we provide more technical details on the protocols and our
  reverse engineering efforts; we also extended the discussion on defenses; we
  reproduced our attack on 13 devices, including several Bluetooth 5.0 devices
  and one Bluetooth 5.1 device; we were extremely surprised by the rejection
  but, in general, the reviewers wanted deeper and more extensive discussion
  of the causes of the attack, countermeasures, and context of other Bluetooth
  attacks.
* **SEC21:** we improved the presentation and used the feedback from CCS20 to
  clarify the issues to readers not familiar with the low level details of CTKD.
* **CCS20:** first version where we described the vulnerabilities and measured
  on an initial set of devices.

| Venue           | Scores | Outcome                  | Response / Changes    |
|-----------------|--------|--------------------------|-----------------------|
| Oakland 2022    | AMMR   | Rejected                 | [Paper](./22sp.pdf); [Diff](./22sp-diff.pdf); [Rebuttal](./22sp-rebuttal.md) |
| NDSS 2022       | MMMwR  | Rejected                 | [Paper](./22ndss.pdf);  |
| CCS 2021        | NNNw   | Major revision to reject | [Diff to SEC](./21ccs-diff.pdf); [Paper](./21ccs.pdf); [Revision Diff](./21ccs-diff2.pdf); [Revision](./21ccs2.pdf) |
| Usenix SEC 2021 | mMMw   | Major revision to reject | [Paper](./21sec.pdf); [Diff](./21sec-diff.pdf); [Revision](./21sec2.pdf) |
| CCS 2020        | ww     | Early rejected           | [Paper](./20ccs.pdf) |

Scores are as follows: __**A**__ccept; __**m**__inor revision;
__**M**__ajor revision; *N*eutral; __**w**__eak reject, __**R**__eject