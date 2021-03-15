### Implementation Guide Overview
FHIR integration is typically point-to-point, where the initiating party communicates directly with the responding endpoint system using REST interactions. But there are circumstances where an originating party may wish to use an intermediary to facilitate communication with a destination endpoint.

For example, an originator may wish to delegate work associated maintaining connectivity with certain endpoints to an intermediary. Or it may participate in a value-added exchange network that provides business or technical services.

In these circumstances, an approach is needed for conveying routing metadata to participating intermediaries--in addition to the content of the exchange itself.

This implementation guide defines a specification for transmitting that routing metadata, and describes exchange conventions for handling a range of scenarios that may arise when implementing the approach. 

<p></p>

### Background

As the need for integration between different actors in healthcare has grown, so have opportunities for participation of intermediaries such as clearinghouses, HIEs, national networks, and other exchanges. An example of this scenario is the situation in which a payer uses a clearinghouse intermediary as their 'gateway' for transactions and other exchanges.

An intermediary model can provide both technical and business operational value-adds in various situations. The model was born in the world of the original X12 transaction set and is expected to continue in the evolving RESTful FHIR API integration model. Other networks including HIEs and national networks have emerged as brokering intermediaries (document access/exchange, e-prescribing, etc.), that may also engage in FHIR based interoperability.

<p></p>

### Solution Goals

- Common pattern, used for many years in healthcare and other industries
- Lightweight
- Works for all RESTful operations including those that do not carry a payload, such as GET searches, so that routing information can be provided to an intermediary even if no FHIR resource is being exchanged
- Universally usable, regardless of FHIR transaction – resource-agnostic.

<p></p>

### Scope of This Guide

This implementation guide is intended be used in the United States.

<p></p>

### Content and Organization

The guide is organized into the following sections:

- [Background and Use Cases](use-cases.html) gives an overview of the guide's goals and participants.
- [Specification](specification.html) describes the solution in detail.
- ...

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
		<td>Mar 15, 2021</td>
		<td>Additional draft content for review. Home, Use Cases and Specification pages.</td>
  	  </tr>
   </tbody>
  </table>




<br />
















