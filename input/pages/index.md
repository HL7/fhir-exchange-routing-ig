<blockquote class="note-to-balloters">
<p>
This specification is a proposed Standard for Trial Use. It is expected to continue to evolve and improve through feedback from early adopters and continued industry development and alignment. 
</p>
</blockquote>






<p></p>

### Overview

This implementation guide provides guidance for enabling FHIR REST interactions across one or more intermediaries using a passive approach. A passive approach is one in which the intermediary is ‘passing through’ the interaction and the requesting actor is not necessarily aware of the presence of the intermediary. Intermediaries in this context are organizations such as clearinghouses, health information exchanges, and similar entities.  We recognize that there are ‘active’ intermediary use cases (TEF QHIN, aggregation services, record locator, etc.) which will be handled in future IGs.

It supports exchanges where the client and destination FHIR server interact with the same steps, content and responsibilities as in a direct connection--while enabling the destination to "sit behind" an intermediary that can provide value-add services such as cloud-scale technical infrastructure, support services, denial of service protection, and business/operational onboarding.

- Example actors for the initial IG include organizations such clinics, providers, hospitals, payers, etc. These actors may be originators of a RESTful request or the destination system for the request.
- Example intermediaries for the initial IG include organizations such as clearinghouses (i.e., Change, Availity) and HIEs ( i.e., eHealth Exchange).

<p></p>

Potential applications of this initial IG include use by implementers of payer/provider use cases such as the _Da Vinci value-based care_ use case in which intermediaries may bridge connectivity between actors. Other HL7 accelerators (CARIN, Gravity, etc.) are developing disparate actor use cases in which intermediaries may be involved.

The community recognizes that direct point-to-point RESTful interaction is a primary interaction pattern. However, we also recognize that intermediaries play important roles for some healthcare actors and having a set of best practices so that we don’t put additional burdens on the client actors is key to running FHIR at scale. This is called the ‘hybrid’ model approach and this IG documents a set of best practices to enable connectivity both in point-to-point and intermediary-facilitated exchange without the client actor needing to have knowledge of what model is executing.

<p></p>

### Background

As the range of healthcare actors using FHIR has grown, so has the need to route exchanges across intermediaries such as clearinghouses, HIEs, national networks, and others. An example of this scenario is the situation in which a payer uses a clearinghouse intermediary as their 'gateway' for receiving FHIR requests. 

Stakeholders use intermediaries for technical, operational and business reasons. The intermediary model was adopted by many payer and provider systems with the original X12 transaction set and is expected to continue as FHIR REST API integration evolves. Other networks, including HIEs and national networks, have emerged as brokering intermediaries for document access/exchange, e-prescribing and other purposes, and may also engage in FHIR-based interoperability.

This implementation guide defines conventions for certain classes of FHIR exchanges that involve such intermediaries. It establishes a basic foundation that will be enhanced and built on over time as stakeholders encounter additional needs.

<p></p>

### Scope of This Guide

**In scope**

The implementation guide focuses on exchanges where...

- the originating system may be unaware that its request will be routed through an intermediary to the final destination for processing; from the perspective of the originating system its intended destination is the responding system

  and

- an intermediary accepts the request on the destination's behalf and then routes it--directly or through another intermediary--to the destination's system

  and

- the originator and destination have established trust, with participating intermediaries passively conveying resulting tokens or other security artifacts

The guide aims to support all RESTful FHIR interaction types (GET, POST, etc.) within this set of scenarios.

This implementation guide was initially created for US Realm but is certainly open to use anywhere it provides value.

<p></p>

**Out of scope of this version of the guide**

The guide does not address all intermediary exchange scenarios that exist today, and does not aim to support emerging network needs such as those posed by the Trusted Exchange Framework and Common Agreement (TEFCA). Environments and use cases supported by active intermediaries are recognized as valid, but are not the focus of the initial version of this guide.

Excluded from scope are environments or scenarios where:

- trust is not negotiated between the originating client and the destination. For example, scenarios where the client establishes trust with an intermediary instead of the destination are not supported
- the originating client addresses requests to anything but the destination’s public URL (for example, instead submitting to a different, network-assigned URL representing the destination)
- the destination does not respond using its public FHIR service base URL in elements that reference the destination's server within returned FHIR resources (for example, populating elements such as fullUrl with a non-public system URL which would require URL rewriting)
- one or more intermediaries rewrite URLs representing the destination server in returned FHIR resources.

In addition, this guide does not provide guidance for the use of [FHIR Messaging](https://www.hl7.org/fhir/messaging.html), which also includes routing features that can be used to exchange message content through intermediaries.

<p></p>

The guide may be expanded to cover additional environments and scenarios in the future. 

<p></p>

### Solution Features

- Common mechanisms used for many years in healthcare and other industries
- Lightweight
- Works when performing all REST interactions including GET (e.g., for a search or retrieval) ensuring that the intermediary routing can be accomplished even if no FHIR resource is being submitted
- Universally usable, regardless of FHIR content. Resource-type agnostic
- Requires no special handling by the originator; the originating system is unaware that an intermediary will play a role in routing the request

<p></p>

### Content and Organization

The guide is organized into the following sections:

- [Use Cases and Roles](use-cases.html) gives an overview of the guide's goals and participants.
- [Exception Handling](exceptions.html) describes expectations for conveying destination-reported exceptions as well as those detected by an intermediary.
- [Security](security.html) identifies aspects needed to support the IG's flows and addtional guidance defined elsewhere.
- [Specification](specification.html) describes the solution in detail.

<p></p>

### Authors

  <table class="grid">
    <tbody>
	  <tr>
		<td colspan="2">HL7 FHIR Infrastructure Workgroup</td>
  	  </tr>
	  <tr>
		<td colspan="2">ONC FHIR at Scale Taskforce (FAST) - Exchange Tiger Team</td>
  	  </tr>
	  <tr>
		<td>Patrick Murta</td>
		<td><a href="mailto:pmurta@humana.com">pmurta@humana.com</a></td>
	  </tr>
	  <tr>
		<td>Frank McKinney</td>
		<td><a href="mailto:frank.mckinney@pocp.com">frank.mckinney@pocp.com</a></td>
	  </tr>
	</tbody>
  </table>



<br /><br />















