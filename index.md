# BLURtooth (BLUR Attacks)

## TL;DR

Bluetooth's cross-transport key derivation (CTKD) is vulnerable to attacks
enabling to attack Bluetooth Classic (from Bluetooth Low Energy and vice versa.
A remote attacker in Bluetooth range to impersonate, man-in-the-middle, and
establish malicious sessions with arbitrary devices.


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
from École Polytechnique Fédérale de Lausanne (EPFL),
[Nils Ole Tippenhauer](https://tippenhauer.de/)
from Helmholtz Center for Information Security (CISPA), and
[Kasper Rasmussen](https://www.cs.ox.ac.uk/people/kasper.rasmussen/)
from the University of Oxford.

In the remainder of this document, we provide information on
technical details, disclosure, impact, our proposed mitigation, the response
from the Bluetooth SIG.


## Technical Details

The Bluetooth standard includes two technologies *Bluetooth Classic (BT)* (also
known as Bluetooth BR/EDR) and *Bluetooth Low Energy (BLE)*. The majority of
mobile devices, including laptops, smartphones, tablets, headphones, and
smartwatches, support both and are defined as *dual-mode* Bluetooth devices. To
securely use dual-mode devices over BT and BLE a user has to pair her devices
two times, once for BT and once for BLE. As pairing the same device is
considered *user-unfriendly*, in 2014, with the release of Bluetooth version
4.2, the Bluetooth standard introduced a security mechanism that allows a user
to pair dual-mode Bluetooth devices once (either over BT or BLE) and then
securely use them both over BT and BLE. This security mechanism is called
*cross-transport key derivation (CTKD)*, and, as the name implies, it enables
deriving pairing keys across different transports (i.e.  derive a BT pairing key
from BLE and vice versa).

Despite being a security-critical mechanism, CTKD is not part of the Bluetooth
threat model and there are no security evaluations of CTKD. Those reasons
motivated us to perform a security analysis of CTKD, resulting in our findings.
In particular, CTKD is affected by 5 major issues (i.e.  vulnerabilities)
enabling an attacker to abuse Bluetooth roles, association, security modes,
keys, and pairing states across BT and BLE. Such issues derive from the *lack of
a cross-transport threat model* in the Bluetooth standard. The standard
considers BT and BLE with separate threat models and security architectures
while, through CTKD, opens avenues for cross-transport attacks (i.e., attacks
that exploit BT by taking advantage of a vulnerability in BLE or vice versa).

We demonstrate that the identified CTKD issues can be exploited by a remote
attacker in Bluetooth range with the victims. In particular, the attacker can
perform impersonation, man-in-the-middle, and malicious session establishment
attacks while bypassing all the security mechanisms provided by BT and BLE
(including Secure Connections or strong association).  Those are very serious
attacks that violate the security guarantees promised by Bluetooth.  We
confirmed the feasibility of our attacks by testing them on 13 common Bluetooth
devices using 10 unique Bluetooth chips. All of them were vulnerable.

You will find technical details about CTKD, our security analysis, a detailed
discussion of the threads, a discussion, and potential mitigations in our
[BLURtooth preprint](https://arxiv.org/abs/2009.11776).


## Disclosure

We discovered the vulnerability in March 2020 and responsibly disclosed our
findings along with suggested countermeasures to the Bluetooth SIG in May 2020.
We kept our findings private and the Bluetooth SIG publicly disclosed them,
without informing us, on the 10th of September of 2020.  Our work is assigned
*[CVE-2020-15802](https://kb.cert.org/vuls/id/589825)*.


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
