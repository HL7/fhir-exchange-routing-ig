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
		<td>April 12-23, 2021</td>
		<td>Expanded Use Cases and Roles and Specification pages to reflect current group discussion. Added draft Exception Handling page</td>
  	  </tr>
   </tbody>
  </table>



<br />
















