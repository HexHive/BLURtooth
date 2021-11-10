We sincerely thank the reviewers for their detailed feedback and will carefully
address the issues in the revised paper. Here, we respond to key comments and
look forward to further clarifying any remaining issues during the rebuttal
phase. We would love to work with a shepherd on the final version of the paper.

# Presentation (RevABD)
As suggested we will fix redundant, vague, incorrect statements (e.g., improving
the lessons learned). We welcome that the BT SIG has finally embraced more
inclusive terminology (i.e., central/peripheral) and will update the paper
accordingly.

# CTKD implementation (RevACD)
While it is true that each evaluated device has a concrete implementation of
CTKD, there is no easily accessible public source implementation of CTKD. Our
open-source implementation enables researchers to verify the correctness of
observed protocol traces, allowing them to check messages and validate
third-party CTKD implementations both in benign and adversarial settings.
Moreover, our implementation can be easily integrated into other open-source
projects related to Bluetooth security. For example, it can be used within a
Bluetooth attack simulator.

# CTKD Reverse engineering (RevABCD)
The Bluetooth standard only provides partial information, making reverse
engineering CTKD necessary. Specifically, we had to infer how CTKD is negotiated
from BT and BLE and how they differ by dynamically monitoring CTKD executions on
actual devices. We will clarify in Section 3.B exactly which aspects we had to
recover (e.g., what/how/when flags and parameters were used) to distinguish our
analysis from the standard. We base our information about CTKD from the
Bluetooth standard version 5.2, pages 280, 1401, 1634, 1658, 1721. These
sections miss the above-mentioned aspects. If RevB is aware of any
document/aspects we missed in the standard, we would appreciate references so
that we can update the paper accordingly.

# Accepting pairing requests when a Bluetooth device is on (RevB)
The fact that a device accepts pairing requests while it is powered on depends
on several design and implementation factors. For example, by design, a BLE
device can only accept pairing requests if it is a BLE peripheral device. For BT
this rule does not hold as any device supporting pairing can send or receive
pairing requests. For concrete implementations, the vendor decides when a device
is pairable (or not). For example, Linux devices are, by default, pairable but
not discoverable, the user sets the discoverability flag in a UI and experts may
set the pairability flag in a configuration file. On the Apple BT stack, devices
are only pairable while the settings dialog remains open (see
https://support.apple.com/en-us/HT204091).

# Disabling JustWorks with CTKD as mitigation (RevB)
We are not considering disabling CTKD+JustWorks as a viable mitigation for two
reasons. First, the mitigation does not prevent all attacks as the attacker can
bypass association methods that are stronger than JustWorks. For example,
leveraging the “Method Confusion Attack on Bluetooth Pairing” from S&P 21, the
attacker can still perform a BLUR man-in-the-middle attack even if the victims
disallow CTKD+JustWorks. Second, the mitigation introduces backward
compatibility issues. Bluetooth devices that only support JustWorks association
(e.g., devices with no I/O capabilities) could no longer use CTKD. Concretely,
all CTKD-compatible headsets and speakers could no longer leverage CTKD as a
result of this mitigation. We will clarify these aspects in the paper.

# Standard compliance (RevB)
We thank the reviewer for pointing out the repetitive argument on standard
compliance and will tone down the paper accordingly. The vulnerabilities are
rooted in the (lack of) specification of CTKD and are not “just” vendor-specific
CTKD implementation bugs. Thanks to our initial report, the standard version 5.2
now includes a note on the BLUR attack vector but does not fully mitigate it. We
are continuing our disclosure process with the Bluetooth SIG to ensure it is
well discussed and mitigated. For the paper, we will follow RevB’s suggestion of
carefully rephrasing this claim. Implementations suffer from an under-specified
CTKD protocol. Ultimately the Bluetooth standard is responsible for properly
specifying security mechanisms and discussing their associated threats.
Currently, this is not the case for CTKD.

# Formal verification (RevB)
We considered formal verification through Tamarin. While Tamarin has been used
to analyze Bluetooth classic, formalizing the BT, BLE, and CTKD specifications
given the lack of formal details in the specification would have resulted in a
large set of assumptions. We, therefore, chose a reverse engineering approach at
the binary protocol level for the first inter-protocol analysis. While formal
verification would provide a more complete analysis, it also requires a complete
specification. We consider deriving such a concrete specification as orthogonal
work.

# Research Contributions (RevC)
Our key research contribution is the first analysis of CTKD, a cross-protocol
key generation technique. Both formal and manual analysis of protocol
specifications (and their implementations) have a long history at Oakland and
other top-tier venues. Under-specifications that enable such severe
vulnerabilities like BLUR immediately apply to billions of devices with broad
impact. Our attack paper is the first to evaluate cross-transport issues on
Bluetooth and analyzes the security of CTKD, an opaque but critical
cross-transport security mechanism baked in the Bluetooth standard.
Additionally, this paper concretely demonstrates an original class of attacks
allowing, for the first time, to exploit BT ***and*** BLE by just targeting one
of the two.

There are multiple lessons to be learned. Firstly, a technology introducing
cross-transport security mechanisms must update its threat model to consider a
cross-transport attacker. This is not the case for Bluetooth and the paper
provides actionable recommendations. Another important lesson is about threat
model equivalence. When designing a cross-transport security mechanism, it is
crucial that its entry points have comparable threat models and associated
security properties. This is not the case for Bluetooth, where the entry points
are BT and BLE pairing which are different protocols with different threat
models and associated security properties. This is an actionable lesson for
future cross-transport protocols.

# Lack of rigor and incorrect claims (RevD)
We are aware that some devices do not support pairing. The Background paragraph
about “all devices support Just Works” starts with “While pairing” to
contextualize the text on devices supporting pairing. We will rephrase the
sentence to clarify this message. Thank you, RevD, for pointing out the
imprecision about “the first that can be conducted in the absence of one of the
victims” statement; we will drop “the first” and clarify/rephrase this sentence
to highlight the contribution of BLUR.


# CTI1 is CTKD specific and silent pairing (RevD)
CTI1 is CTKD specific as CTKD enables a novel way to pair devices. For example,
with the advent of CTKD, we observed devices that use BT for their main
functionality (e.g., headphones and speakers) to advertise over BLE, using BLE
pairing+CTKD to improve user experience.
The fact that two devices can pair silently is not related to CTKD but depends
on the authentication requirements and I/O capabilities of the devices. For
example, you can silently pair two devices that only support BT or BLE.


# Novelty of the CTIs, especially CTI3 and CTI4 (RevD)
Other Bluetooth attacks are based on issues related to the CTIs, however, none
of those issues are cross-transport. For example, “key tampering” does not equal
“cross-transport key tampering (CTI 3)”; in the first case, the issue is limited
to one transport (either BT or BLE), while in the second case the issue affects
multiple transports (BT and BLE) and related to how keys are managed across
transports. In other words, cross-transport key tampering introduces an
alternative, novel way to overwrite keys for Bluetooth. Similar reasoning holds
for cross-transport manipulation of associations (CTI 4). We will clarify this
important distinction and refer to other key overwrite attacks in more detail.

Regarding the clarification attack example with a laptop and a pair of
headphones, our attack scenario assumes that the headset supports both BT and
BLE and therefore CTKD (see our system model), contrary as assumed by RevD in
their review.

About finding examples where legacy attacks cannot accomplish what the BLUR
attacks can, we provide concrete examples comparing our attacks to Nino and
BLESA as suggested by the reviewer. The BLUR unintended session attacks
presented in 4.E cannot be achieved using Nino and BLESA as Nino is a MitM
attack and BLESA is a spoofing attack. If we focus on spoofing and MitM attacks,
unlike the BLUR spoofing and MitM attacks, Nino and BLESA even when combined
cannot achieve a persistent compromise of BT and BLE. In particular, only with
the BLUR spoofing or MitM attacks the attacker can arbitrarily spoof and MitM
any BT or BLE device and gain persistence by overriding BT and BLE pairing keys.
With Nino the attacker can only temporarily MitM a BT pairing session, cannot
spoof single devices, and cannot override BT pairing keys. With BLESA the
attacker can only temporarily spoof a BLE server, cannot spoof the BLE client,
cannot establish a BLE MitM position, and cannot override BLE pairing keys.
