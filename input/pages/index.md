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

### Change Log

  <table class="grid">
    <tbody>
	  <tr>
		<td>Date</td>
		<td>Change</td>
  	  </tr>
	  <tr>
		<td>Mar 1, 2021</td>
		<td>Skeleton draft</td>
  	  </tr>
	  <tr>
		<td>April 12-23, 2021</td>
		<td>Expanded Use Cases and Roles and Specification pages to reflect current group discussion. Added draft Exception Handling page</td>
  	  </tr>
	  <tr>
		<td>May 17, 2021</td>
		<td>Added draft material to the Specification page describing a fourth addressing approach that passes routing information in a query string parameter</td>
  	  </tr>
	  <tr>
		<td>June 12, 2021</td>
		<td>Adjusted Specification page based on Connectathon feedback: added "intermediary subdomain" as another option for constructing the destination’s public FHIR service address and added a note to "Intermediary’s base URL accompanied by routing metadata passed in a query string parameter" stating that Connectathon discussion deterimined that the option results in invalid FHIR URLs in some cases.    Adjusted exchange flow diagrams to consistently refer to the intermediary's URL as example-int.com and the destination's public URL as example-dest and private URL as real-dest.com.   Added a section on URL rewriting to the Specification page, and added rewriting of URLs to exchange flows.</td>
  	  </tr>
	  <tr>
		<td>July 9, 2021</td>
		<td>Focused the content on addressing options 1 (Base URL that reflects the destination's identity), 2 (Subdomain of intermediary's URL that identifies the destination) and 3 (Intermediary's base URL followed by a path indicating the destination) based on stakeholder feedback. On the Specifications page, removed options 4 (HTTP header parameter) and 5 (query string parameter). Removed the incomplete push scenario content from the Use Cases and Specifications pages. Clarified content in the scenario steps related to rewriting of URLs in content returned by the destination.</td>
  	  </tr>
	  <tr>
		<td>July 19, 2021</td>
		<td>Added more description and specifics about the guide's scope to the index page. On the Use Cases page, clarified the first out of scope row, and highlighted the second out of scope row for discussion. Adjusted Exception Handling page based on final solution approaches</td>
  	  </tr>
	  <tr>
		<td>July 21, 2021</td>
		<td>In Specification, added the need to also rewrite HTTP header parameter values containing the destination's base service address. Removed errant reference to a future FAST Scaling IG in the exception handling page. Added CapabilityStatement for an intermediary. Removed placeholder FHIR resources.</td>
  	  </tr>
	  <tr>
		<td>July 26, 2021</td>
		<td>In Specification, corrected sequence diagrams that referred to an X-Destination header that was previously removed from the IG's approaches.</td>
  	  </tr>
   </tbody>
  </table>

<br />















