### Overview
As the need for integration between different actors in healthcare has grown, the aspect of transactions routings across one or more intermediaries such as clearinghouses, HIEs, national networks, and other exchanges is recognized. An example of this scenario is the situation in which a payer uses a clearinghouse intermediary as their 'gateway' for transactions. 

There are both technical and business operational value adds in this intermediary model. This model was born in the world of the original X12 transaction set and is expected to continue in the evolving RESTful FHIR API integration model. Other networks including HIEs and national networks have emerged as brokering intermediaries (document access/exchange, e-prescribing, etc.), that may also engage in FHIR based interoperability.

This implementation guide defines conventions for FHIR exchanges involving such intermediaries.

<p></p>

### Solution Features

- Common pattern, used for many years in healthcare and other industries
- Lightweight
- Works even when doing GET or POST (i.e., searches or matches), so even if there is no FHIR resource being exchanged then routing information is still available
- Universally usable, regardless of FHIR transaction – it’s resource agnostic

<p></p>

### Scope of This Guide

This implementation guide is intended be used in the United States.

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
   </tbody>
  </table>




<br />















