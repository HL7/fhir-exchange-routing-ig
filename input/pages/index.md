### Overview
As the range of healthcare actors using FHIR has grown, so has the need to route exchanges across intermediaries such as clearinghouses, HIEs, national networks, and others. An example of this scenario is the situation in which a payer uses a clearinghouse intermediary as their 'gateway' for receiving FHIR requests. 

Stakeholders use intermediaries for technical, operational and business reasons. The intermediary model was born in the world of the original X12 transaction set and is expected to continue as RESTful FHIR API integration evolves. Other networks, including HIEs and national networks, have emerged as brokering intermediaries for document access/exchange, e-prescribing and other purposes, and may also engage in FHIR-based interoperability.

This implementation guide defines conventions for certain FHIR exchanges that involve such intermediaries. It establishes a basic foundation that will be enhanced and built on over time as stakeholders encounter additional needs.

<p></p>

### Scope of This Guide

**In scope**

The implementation guide focuses on exchanges where...

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory
- an intermediary accepts the request on the destination's behalf and then routes it--directly or through another intermediary--to the destination's system.

The guide aims to support all RESTful FHIR interaction types (GET, POST, etc.) within this set of scenarios.

This implementation guide is intended be used in the United States.

<p></p>

**Out of scope of this version of the guide**

The guide does not currently address other intermediary exchange scenarios that exist today--though it may be expanded to cover those in the future. 

In particular, the guide does not address scenarios where the originator provides information or identifiers within each exchange submitted to an intermediary's single FHIR service URL--and the intermediary takes responsibility for the searching or business logic to identify a destination that fits the submitted criteria.

<p></p>

### Solution Features

- Common mechanisms used for many years in healthcare and other industries
- Lightweight
- Works when performing all REST interactions including GET (e.g., for a search or retrieval) ensuring that the intermediary routing can be accomplished even if no FHIR resource is being submitted
- Universally usable, regardless of FHIR content. Resource-type agnostic
- Requires no special handling by the originator; the submitting system is unaware that an intermediary will play a role in routing the request

<p></p>

### Content and Organization

The guide is organized into the following sections:

- [Use Cases and Roles](use-cases.html) gives an overview of the guide's goals and participants.
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

<p></p>

<br />















