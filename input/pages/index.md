<blockquote class="note-to-balloters">
<p>
This specification is a proposed Standard for Trial Use. It is expected to continue to evolve and improve through connectathon testing, feedback from early adopters, and continued industry development and alignment. 
</p>
<p>
Until balloting begins, feedback is welcome and may be submitted through the <a href="https://chat.fhir.org/#narrow/stream/264775-FHIR-at.20Scale.20.28FAST.29.3A.20Exchange.20with.2Fwithout.20intermediaries">FHIR at Scale (FAST): Exchange with/without intermediaries Zulip stream</a>.
</p>
</blockquote>





<p></p>

### Overview

This implementation guide provides guidance for enabling FHIR REST interactions across one or more intermediaries using a passive approach. A passive approach is one in which the intermediary is ‘passing through’ the interaction and the requesting actor is not necessarily aware of the presence of the intermediary. Intermediaries in this context are organizations such as clearinghouses, health information exchanges, and similar entities.  We recognize that there are ‘active’ intermediary use cases (TEF QHIN, aggregation services, record locator, etc.) which will be handled in future IGs.

It supports exchanges where the client and destination FHIR server interact with the same steps, content and responsibilities as in a direct connection--while enabling the destination to "sit behind" an intermediary that can provide value-add services such as cloud-scale technical infrastructure, support services, denial of service protection, and business/operational onboarding.

- Example actors for the initial IG include organizations such clinics, providers, hospitals, payers, etc.
- Example intermediaries for the initial IG include organizations such as clearinghouses (i.e., Change, Availity) and HIEs ( i.e., eHealth Exchange).

<p></p>

Potential applications of this initial IG include use by implementers of payer/provider use cases such as Da Vinci value-based care use case in which intermediaries may bridge connectivity between actors. . Other HL7 accelerators (CARIN, Gravity, etc.) are developing disparate actor use cases in which intermediaries may be involved.

The community recognizes that direct point to point RESTful interaction is a primary interaction pattern. However, we also recognize that intermediaries play important roles for some healthcare actors and having a set of best practices so that we don’t put additional burdens on the client actors is key to running FHIR at scale. This is called the ‘hybrid’ model approach and this IG documents a set of best practices to enable connectivity both in point to point and intermediary facilitated exchange without the client actor needing to have knowledge of what model is executing.

<p></p>

### Background

As the range of healthcare actors using FHIR has grown, so has the need to route exchanges across intermediaries such as clearinghouses, HIEs, national networks, and others. An example of this scenario is the situation in which a payer uses a clearinghouse intermediary as their 'gateway' for receiving FHIR requests. 

Stakeholders use intermediaries for technical, operational and business reasons. The intermediary model was adopted by many payer and provider systems with the original X12 transaction set and is expected to continue as FHIR REST API integration evolves. Other networks, including HIEs and national networks, have emerged as brokering intermediaries for document access/exchange, e-prescribing and other purposes, and may also engage in FHIR-based interoperability.

This implementation guide defines conventions for certain FHIR exchanges that involve such intermediaries. It establishes a basic foundation that will be enhanced and built on over time as stakeholders encounter additional needs.

<p></p>

### Scope of This Guide

**In scope**

The implementation guide focuses on exchanges where...

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory
- an intermediary accepts the request on the destination's behalf and then routes it--directly or through another intermediary--to the destination's system
- the originator and destination have established trust, with participating intermediaries passively conveying resulting tokens or other security artifacts

The guide aims to support all RESTful FHIR interaction types (GET, POST, etc.) within this set of scenarios.

This implementation guide was initially created for US Realm but is certainly open to use anywhere it provides value.

<p></p>

**Out of scope of this version of the guide**

The guide does not address all intermediary exchange scenarios that exist today, and does not aim to support emerging network needs such as those posed by the Trusted Exchange Framework and Common Agreement (TEFCA). Environments and use cases supported by active intermediaries are recognized as valid, but are not the focus of the initial version of this guide.

Excluded from scope are environments or scenarios where:

- trust is not negotiated between the originating client and the destination. For example, scenarios where the client establishes trust with an intermediary instead of the destination are not supported
- the originating client addresses requests to anything but the destination’s public URL (for example, instead submitting to a different, network-assigned URL representing the destination)
- the destination does not respond using its pubic FHIR service base URL in elements that reference the destination's server within returned FHIR resources (for example, populating elements such as fullUrl with a non-public system URL which would require URL rewriting)
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
- Requires no special handling by the originator; the submitting system is unaware that an intermediary will play a role in routing the request

<p></p>

### Content and Organization

The guide is organized into the following sections:

- [Use Cases and Roles](use-cases.html) gives an overview of the guide's goals and participants.
- [Specification](specification.html) describes the solution in detail.
- [Exception Handling](exceptions.html) describes expectations for conveying destination-reported exceptions as well as those detected by an intermediary.
- [Security](security.html) identifies aspects needed to support the IG's flows and addtional guidance defined elsewhere.

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
	  <tr>
		<td>September 10, 2021</td>
		<td>Adjustments based on public discussions during the week of Sep. 6. <br><br>Specification page: Modified all diagrams and sections according to current discussion. Reintroduced the HTTP Header approach sections and diagrams previously removed from the IG. Removed app path approach from intermediary-to-intermediary routing. Clarified construction of the public URL (addressing to the Inbound Gateway) vs. addressing between intermediaries. Added discussion notes. <br><br>Use Cases page: Adjustments to intermediary roles table and links to the Specification page. <br><br>Index page: adjusted scope section.</td>
  	  </tr>
	  <tr>
		<td>October 23, 2021</td>
		<td>Adjustments based on public discussions during the week of Oct. 18. <br><br>Specification page: Added section, Assumptions: Partnership and setup between the originator and intermediaries. Added content to the section, Public FHIR service URL of the destination, indicating that the guide does not prescribe a particular URL structure. Added section, Private FHIR service base URLs used in routing between intermediaries and to the actual destination server. Added section, Sharing of private URLs among the destination and partner intermediaries. Removed HTTP header and querystring flows and replaced with URL-based options
 <br><br>Index page: added STU note. <br><br>Use Cases and Roles page: Removed a scenario from the Excluded Scenarios bullet a statement. Added an Assumptions and Setup Steps section. Added links within the scenario overviews to URL rewriting section in the Specification page</td>
  	  </tr>
	<tr>
		<td>October 27, 2021</td>
		<td>Added Security page</td>
  	  </tr>
        <tr>
		<td>November 2, 2021</td>
		<td>Added illustrations of single and multiple intermediary/network exchange to the Specification page. Other adjustments to the Specification page based on Oct 28 public call.</td>
  	  </tr>
        <tr>
		<td>November 15, 2021</td>
		<td>Updates to Specification, Use Cases and Security pages based on consideration of recent discussions about scope and security. Tightened the scope description to focus on exchanges conducted over the public internet using RESTful interactions. Specified further requirements related to routing and trust behavior to reduce potential complexity and enable application of typical security best practices including those detailed in the FAST Security IG. Requied destination's use of its public URL in returned FHIR resources and removed URL rewriting from the IG's scope. Added more security specifics. Added CapabilityStatement for the destination server</td>
  	  </tr>
        <tr>
		<td>November 17, 2021</td>
		<td>Updates to Home page</td>
  	  </tr>
        <tr>
		<td>November 17, 2021 6ET</td>
		<td>Additional adjustments to Home page and Use Case page based on ITS WG review</td>
  	  </tr>
        <tr>
		<td>November 21, 2021</td>
		<td>Updates to the narrative in the Home, Use Cases and Roles, Specification, Exception Handling and Security pages.</td>
  	  </tr>
      <tr>
		<td>November 22, 2021</td>
		<td>Added example participants.</td>
  	  </tr>
   </tbody>
  </table>



<br />















