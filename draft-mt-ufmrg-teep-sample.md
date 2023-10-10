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
 - ins: C. Myers
   fullname: Cory Myers
   email: cfm@acm.org
 - ins: H. Tschofenig
   fullname: Hannes Tschofenig
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
  thaler:
    title: "TEEP Protocol: draft-ietf-teep-protocol-16"
    target: https://datatracker.ietf.org/meeting/interim-2023-suit-01/materials/slides-interim-2023-suit-01-sessa-teep-protocol-00.pdf
    author:
      name: Dave Thaler
  teep:
    title: Trusted Execution Environment Provisioning
    target: https://datatracker.ietf.org/wg/teep/about/
    author:
      organization: IETF
    date: 2023

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
   findings may have valuble security implications.

3. The TEEP protocol has well-defined use cases and includes provisions for
   constrained environments.  Modeling the entire protocol is a reasonble
   learning or training exercise, whereas {{?I-D.farrell-ufmrg-sample}}
   limits itself to just the `SEARCH` command of {{?RFC9051}}.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# The Trusted Execution Environment Provisioning (TEEP) Protocol

The Trusted Execution Environment Provisioning protocol
{{?I-D.ietf-teep-protocol}} specifies communication between a Trusted
Application Manager {{?RFC9397 (Section 2)}} and a TEEP agent.  Importantly,
this communication is relayed by an *untrusted* TEEP Broker {{?RFC9397 (Section
6.1)}}.

~~~
# TAM -> TEEP Agent: QueryRequest
{token, challenge, supported-teep-cipher-suites,
supported-suit-cose-profiles, data-item-requested(trusted-components,
attestation)}SK_TAM

# TAM -> Attester: Get Evidence (Challenge)
Challenge

# TAM <- Attester: Evidence(Challenge)
{Challenge, Claims}SK_Attester

# TAM <- TEEP Agent: QueryResponse
{token, selected-teep-cipher-suite, attestation-payload-format(EAT),
attestation-payload({Challenge, Claims}SK_Attester),tc-list,
requested-tc-list, requested-tc-list}SK_AGENT


# Author -> TAM: Software
{manifest, sequence_nr, software}SK_AUTHOR

# TAM -> TEEP Agent: Update
{token', {manifest, sequence_nr, software}SK_AUTHOR}SK_TAM

# TAM <- TEEP Agent: Success
{token'}SK_AGENT
~~~

## Security Properties

At the {{suit-interim}} meeting, the following editorial addition was proposed
{{thaler}} in TEEP's security consideration ({{Section 10 of
?I-D.ietf-teep-protocol}}):

>  The TEEP protocol supports replay protection as follows. The transport
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


# Security Considerations

This document has no security considerations of its own, although it may
contribute indirectly to the development of new security considerations
for the TEEP protocol.


# IANA Considerations

This document has no IANA actions.
