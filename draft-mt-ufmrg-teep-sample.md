---
title: "A Usable Formal Methods Sample Problem from TEEP"
abbrev: "TEEP Sample"
category: info

docname: draft-mt-ufmrg-teep-sample-latest
submissiontype: IRTF
number:
date:
consensus: true
v: 3
workgroup: "Usable Formal Methods Proposed Research Group"
venue:
  group: "Usable Formal Methods"
  type: "Research Group"
  mail: "ufmrg@irtf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ufmrg"
  github: "cfm/draft-mt-ufmrg-teep-sample"

author:
 - fullname: Cory Myers
   email: cfm@acm.org
 - fullname: Hannes Tschofenig
   email: hannes.tschofenig@gmx.net

normative:

informative:
  suit:
    title: Software Updates for the Internet of Things
    target: https://datatracker.ietf.org/wg/suit/about/
    author:
      organization: IETF
    date: 2023
  suit-interim:
    title: interim-2023-suit-01
    target: https://datatracker.ietf.org/doc/agenda-interim-2023-suit-01-sessa/
    author:
      name: Dave Thaler
    date: 2023-09-11
  teep:
    title: Trusted Execution Environment Provisioning
    target: https://datatracker.ietf.org/wg/teep/about/
    author:
      organization: IETF
    date: 2023
  thaler:
    title: "TEEP Protocol: draft-ietf-teep-protocol-16"
    target: https://datatracker.ietf.org/meeting/interim-2023-suit-01/materials/slides-interim-2023-suit-01-sessa-teep-protocol-00.pdf
    author:
      name: Dave Thaler
    date: 2023-09-11

--- abstract

This draft follows the invitation of {{?I-D.farrell-ufmrg-sample}} to propose
another sample problem for demonstration, training, and evaluation of formal
methods in IETF work.  It draws on recent work from the Software Updates for the
Internet of Things {{suit}} and Trusted Execution Environment Provisioning
{{teep}} working groups to define a sample modeling problem for a novel rather
than a familiar IETF protocol.


--- middle

# Introduction

In this draft we take the Trusted Execution Environment Provisioning protocol
{{?I-D.ietf-teep-protocol}} to be a good domain from which to draw a sample
modeling problem for slightly different reasons than those proposed in
{{?I-D.farrell-ufmrg-sample}}.

1. TEEP is a proposed standard under active development, at working-group
   consensus as of this writing.  Formal modeling, even for demonstration or
   training purposes, can therefore increase familiarity with the TEEP protocol,
   including outside of the SUIT and TEEP working groups directly involed in its
   development.

2. The TEEP protocol is expressly concerned with security, so it has security
   properties amenable to formal description, modeling, and analysis.  Any
   findings may have valuable security implications.

3. The TEEP protocol has well-defined use cases and includes a number of options
   for flexibility, including provisions for constrained environments.  Modeling
   the entire protocol, including all its options, is a reasonable challenge for
   a learning or training exercise.  By contrast, {{?I-D.farrell-ufmrg-sample}}
   limits itself to just the `SEARCH` command of {{?RFC9051}}.

4. The TEEP protocol follows a typical protocol design in the IETF where
   various already-standardized technologies are reused. In this case, protocols
   from the Software Updates for Internet of Things (SUIT) architecture
   {{!RFC9019}} and the Remote Attestation Procedures (RATS) architecture
   {{!RFC9334}} are incorporated into the design.

6. The architecture of the TEEP protocol is also representative for IETF
   protocol development since more than two parties are involved in the protocol
   communication even in a single, self-contined deployment. Whether a formal
   model must consider all parties or can limit itself to a subset is a
   worthwhile question about the scope of this sample problem.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# The Trusted Execution Environment Provisioning (TEEP) Protocol

The Trusted Execution Environment Provisioning protocol
{{?I-D.ietf-teep-protocol}} specifies communication between a Trusted
Application Manager (TAM) {{?RFC9397 (Section 2)}} and a TEEP Agent.
Importantly, this communication is relayed by an *untrusted* TEEP Broker
{{?RFC9397 (Section 6.1)}}. Two security-sensitive payloads are communicated via
the TEEP protocol, namely:

- Trusted Applications (TAs) and Trusted Components (TCs) {{?RFC9397 (Section
  2)}}; and
- attestation evidence.

A Trusted Application is an application (or, in some implementations, an
application component) that runs in a TEE.

A Trusted Component is a set of code and/or data in a TEE that is managed as a
unit by a TAM. Trusted Applications and Personalization Data {{?RFC9397 (Section
2)}} are thus managed by being included in Trusted Components.

TCs are provided by developers or authors, which TEEP calls "Trusted Component
Signers" {{?RFC9397 (Section 2)}}.  SUIT manifests protect TCs' integrity and
may optionally protect their confidentiality as well.  Neither the TAM nor the
TEEP Broker should be able to modify TCs.  TEEP Agents only install TCs from
sources they trust.

Attestation evidence is typically communicated from the TEEP Agent to the TAM,
although there is the option to offer attestation capabilities in both
directions. In the description below, we focus only on attestation of the Device
{{?RFC9397 (Section 2)}} containing the TEEP Agent to the TAM, rather than
attestation in the other direction.

The description below illustrates the basic communication interaction, with
details of the TEEP protocol abstracted away.


## `NonceRequest`/`NonceRespone`

~~~
TAM -> Verifier: NonceRequest

TAM <- Verifier: NonceResponse
Nonce
~~~


## `QueryRequest`

~~~
TAM -> TEEP Agent: QueryRequest
{token, challenge, supported-teep-cipher-suites,
supported-suit-cose-profiles, data-item-requested(trusted-components,
attestation)}SK_TAM
~~~

- The `challenge` is a random number provided by the Verifier {{!RFC9334
  (Section 1)}}. It is used to demonstrate the freshness of the attestation
  evidence.
- The `token` is a random number that is used to match the `QueryRequest`
  against the `QueryResponse`.
- `supported-teep-cipher-suites` and `supported-suit-cose-profiles` offer
  cipher-suite negotation.
- `data-item-requested(X)` indicates the functionality that the TAM requests the
  TEEP Agent perform.
- `{_}SK_TAM` indicates a digital signature over the payload of the message
  using a private (or secret) key that is only known to the TAM.


## `EvidenceRequest`/`EvidenceResponse`

~~~
TEEP Agent -> Attester: EvidenceRequest
challenge

TEEP Agent <- Attester: EvidenceResponse
{challenge, claims}SK_ATTESTER
~~~

- `{challenge, claims}SK_ATTESTER` represents the evidence, which is signed by
  the Attester using its private key `SK_ATTESTER`.

  In addition to the inclusion of the `Challenge`, the random number provided by
  the Verifier, it also includes further (unspecified) claims. While those
  claims are important for the overall system, they are not in scope of this
  analysis.


## `QueryResponse`

~~~
TAM <- TEEP Agent: QueryResponse
{token, selected-teep-cipher-suite, attestation-payload-format(EAT),
attestation-payload({challenge, claims}SK_ATTESTER), tc-list}SK_AGENT
~~~

- `selected-teep-cipher-suite` indicates the selected cipher suite.
- `attestation-payload-format(EAT)` indicates the format of the attached
  attestation evidence.
- `tc-list` conveys the list of Trusted Components installed on the Device.
- `attestation-payload(_)` contains the evidence provided by the Attester in the
  previous step.
- `{_}SK_AGENT` indicates a digital signature over the payload of the message
  using a private (or secret) key that is only known to the Agent.


## `SoftwareUpdate`

~~~
Author -> TAM: SoftwareUpdate
{manifest, sequence_nr, TC}SK_AUTHOR
~~~

- The `manifest` contains instructions for how to install the software.
- `sequence_nr`, as the name indicates, allows the TEEP Agent to distingush this
  update from earlier versions of the software.
- `TC` is the Trusted Component to be installed. This could be a binary,
  configuration data, or both.
- `{_}SK_AUTHOR` indicates a digital signature over the payload of the message
  using a private (or secret) key that is only known to the author, i.e. the
  Trusted Component Signer.


## `Update`

The TAM transmits an update to the TEEP Agent containing the previously obtained payload provided by the author.

~~~
TAM -> TEEP Agent: Update
{token2, {manifest, sequence_nr, software}SK_AUTHOR}SK_TAM
~~~

- `token2` is a new random number, which is again used by the TAM to match
  requests against responses.
- `{_}SK_TAM` indicates a digital signature over the payload of the message
  using a private (or secret) key that is only known to the TAM.


## `Success`

The TEEP Agent returns this message when the software installation was
successful.

~~~
TAM <- TEEP Agent: Success
{token2}SK_AGENT
~~~

- `{_}SK_AGENT` indicates a digital signature over the payload of the message
  using a private (or secret) key that is only known to the Agent.


## Security Properties

In addition to the integrity and authentication properties, an important aspect
is replay protection.

At the {{suit-interim}} meeting, the following editorial addition was proposed
{{thaler}} in TEEP's security consideration ({{Section 10 of
!I-D.ietf-teep-protocol}}):

> The TEEP protocol supports replay protection as follows. The transport
> protocol under the TEEP protocol might provide replay protection, but may be
> terminated in the TEEP Broker which is not trusted by the TEEP Agent and so the
> TEEP protocol does replay protection itself. If attestation of the TAM is used,
> the attestation freshness mechanism provides replay protection for attested
> QueryRequest messages. If non-attested QueryRequest messages are replayed, the
> TEEP Agent will generate QueryResponse or Error messages, but the REE can
> already conduct Denial of Service attacks against the TEE and/or the TAM even
> without the TEEP protocol. QueryResponse messages have replay protection via
> attestation freshness mechanism, or the token field in the message if
> attestation is not used. Update messages have replay protection via the
> suit-manifest-sequence-number (see Section 8.4.2 of [I-D.ietf-suit-manifest]).
> Error and Success messages have replay protection via SUIT Reports and/or the
> token field in the message, where a TAM can detect which message it is in
> response to.

Any formal model of TEEP should be able to prove this replay protection.

While confidentiality protection is possible for attestation evidence as well as
for Trusted Components, it is not mandatory functionality.


# Security Considerations

This document has no security considerations of its own, although it may
contribute indirectly to the development of new security considerations
for the TEEP protocol.


# IANA Considerations

This document has no IANA actions.
