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
  draft-farrell-ufmrg-sample:
    title: Usable Formal Methods Sample Problems
    target: https://datatracker.ietf.org/doc/draft-farrell-ufmrg-sample/
    author:
      name: Stephen Farrell
    date: 2023-06-19
  suit:
    title: Software Updates for the Internet of Things
    target: https://datatracker.ietf.org/wg/suit/about/
    author:
      organization: IETF
    date: 2023
  teep:
    title: Trusted Execution Environment Provisioning
    target: https://datatracker.ietf.org/wg/teep/about/
    author:
      organization: IETF
    date: 2023
  teep-protocol:
    title: Trusted Execution Environment Provisioning (TEEP) Protocool
    target: https://datatracker.ietf.org/doc/draft-ietf-teep-protocol/
    author:
      - name: Hannes Tschofenig
      - name: Mingliang Pei
      - name: David Wheeler
      - name: Dave Thaler
      - name: Akira Tsukamoto
    date: 2023-09-05

--- abstract

This draft follows the invitation of {{draft-farrell-ufmrg-sample}} to propose
another sample problem for demonstration, training, and evaluation of formal
methods in IETF work.  It draws on recent work from the Software Updates for the
Internet of Things {{suit}} and Trusted Execution Environment Provisioning
{{teep}} working groups to define a sample modeling problem for a novel rather
than a familiar IETF protocol.


--- middle

# Introduction

In this draft we take the Trusted Execution Environment Provisioning protocol
{{teep-protocol}} to be a good domain from which to draw a sample modeling
problem for slightly different reasons than those proposed in
{{draft-farrell-ufmrg-sample}}.

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
   learning or training exercise, whereas {{draft-farrell-ufmrg-sample}}
   limits itself to just the `SEARCH` command of {{?RFC9051}}.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# The Trusted Execution Environment Provisioning (TEEP) Protocol

The Trusted Execution Environment Provisioning protocol {{teep-protocol}}
specifies communication between a Trusted Application Manager
{{?RFC9397 (Section 2)}} and a TEEP agent.  Importantly, this communication is
relayed by an *untrusted* TEEP Broker {{?RFC9397 (Section 6.1)}}.

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


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
