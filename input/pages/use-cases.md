This guide supports scenarios where intermediaries take part in RESTful FHIR exchanges between an initiating system (the "originator") and the desired endpoint system (the "destination").  It focuses on use cases where:

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory
- but the destination has an arrangement where an intermediary accepts the request on the destination's behalf and then routes it--directly or through another intermediary--to the destination's system.

This page defines the roles that intermediaries play in the scenarios addressed in this guide and then describes those scenarios more fully.

#### Note about excluded use cases 

This guide does not provide guidance for: 

- situations where the originator arranges to address some or all of its requests to an intermediary--who, in turn, uses logic, provided metadata or other means to determine and route to an appropriate destination

- use of FHIR Messaging, which also includes routing features that can be used to exchange message content through intermediaries.

<p></p>

### Intermediary Roles

<table class="grid">
  <thead>
    <tr>
      <th>Intermediary  Role</th>
      <th><em>In this role, the intermediary  works for…</em></th>
      <th>Description</th>
      <th>Benefit provided</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="5"><strong><em>Supported in this guide:</em></strong></td>
    </tr>
    <tr>
      <td>Inbound  Gateway</td>
      <td>Destination</td>
      <td>The intermediary receives all requests to the destination and  forwards them to the destination system</td>
      <td>The destination  delegates the responsibility of securing a public endpoint and potentially  other work (registering clients, etc.) to the intermediary</td>
      <td>Common intermediary role for financial and administrative data exchange. DNS entries can be  used to “point” the destination’s public URL to the intermediary that’s serving as a proxy for the destination</td>
    </tr>
    <tr>
      <td>Delegated  Function Intermediary</td>
      <td>An Inbound Gateway intermediary</td>
      <td>One  intermediary transports traffic from a different intermediary to a receiving  system</td>
      <td>The <em>delegating</em>  intermediary gives responsibility for maintaining a connection to the  receiving destination system (including security requirements, etc.) to the Delegated  Function Intermediary</td>
      <td>E.g., A payer  that delegates utilization management for a type of service to a different  organization and/or system. The inter deliver to a particular recipient.      The partner  intermediaries may share non-public routing information to support delivery  to the ultimate receiver</td>
    </tr>
    <tr>
      <td colspan="5"><strong><em>Not currently supported in this guide.</em></strong> These are described here to explicitly identify certain intermediary roles that this guide does not address at this time.
</td>     
    </tr>
    <tr>
      <td>Outbound  Gateway</td>
      <td>Originator</td>
      <td>The  originator directs some or all of its requests to the intermediary, who  forwards them to the desired responders</td>
      <td>Originator  delegates the work of maintaining connections for multiple endpoints (registration,  security credentials, etc.) to the intermediary.  Also enables  the intermediary to provide searching and business logic to identify a  responder that fits the originator’s criteria.  Often  connected to originator via an VPN.</td>
      <td>Used in  e-prescribing when the sender doesn’t know the specific receiving system  (prescription renewals, certain benefit requests).     Not common in  other environments</td>
    </tr>
    <tr>
      <td>Exchange  Network</td>
      <td>Both the Originator and the Destination</td>
      <td>The  intermediary provides a closed “hub and spoke” environment in which  participants exchange with each other through a single, common intermediary  connection</td>
      <td><em>Combination  of Outbound Gateway and Inbound Gateway, above.</em> Enables originators to maintain a  single outbound connection instead  of multiple.  Network ensures secure inbound connection</td>
      <td>Example: a Health Information Exchange</td>
    </tr>
  </tbody>
</table>

<p></p>

### Use Case Overview

While there are many potential exchange scenarios in which an intermediary could play a part, this guide focuses on a specific set where:

- the exchange is a RESTful FHIR interaction
- the originator directs its exchange to the intended destination's public FHIR service address
- the originator is unaware that an intermediary will play a role in routing the request, and
- the intermediary plays either the **Inbound Gateway** or **Delegated Function Intermediary** roles described above.

Use cases are separated into two groups below:

- **synchronous** exchanges, in which participating intermediaries stay connected until the destination's response is forwarded to the originator
  - Use Case: [Request routed through a single Inbound Gateway intermediary](#request-routed-through-a-single-inbound-gateway-intermediary)
  - Use Case: [Destination uses an Inbound Gateway Intermediary that, in turn has a relationship with a Delegated Function Intermediary to deliver certain exchanges](destination-uses-an-inbound-gateway-intermediary-that-in-turn-has-a-relationship-with-a-delegated-function-intermediary-to-deliver-certain-exchanges)
- **asynchronous** exchanges that follow the [FHIR Asynchronous Request pattern](http://hl7.org/fhir/async.html), as used in the [Bulk Data Access IG](https://hl7.org/fhir/uv/bulkdata/index.html)
  - Use Case 3: tbd

<p></p>

### Options for constructing the public FHIR service address

For each of the above use below, three models may be used to construct and use the destination's public FHIR service address. The first two approaches result in a URL, and the third results in a URL that is accompanied by additional metadata to be conveyed in the HTTP header (in an X-Destination parameter).

**Base URL that reflects the destination's identity,** such as <br/>`fhir.example-destination.com`

In this method, the destination uses a public FHIR service base URL that reflects its own identity and, in turn, hides participation of the Inbound Gateway Intermediary it employs.

The intermediary bases routing on the network IP address at which it receives the request (the IP to which the public URL's DNS record is mapped).

**Intermediary's base URL followed by a path indicating the destination**, such as <br/>
`fhir.example-intermediary.com/example-destination`

In this method, the destination's public FHIR service URL consists of:

- a base URL reflecting the Inbound Gateway Intermediary's identity
- a path segment following the base that reflects the destination's identity

The intermediary bases routing on the path segment that indicates the destination.

**Intermediary's base URL accompanied by routing metadata passed in an HTTP header parameter**, such as <br/>URL: `fhir.example-intermediary.com` 
HTTP header parameter: `X-Destination: example-destination`

In this method, the destination's public FHIR service address consists of: 

- the Inbound Gateway Intermediary's base URL 
- paired with metadata passed in the HTTP header's `X-Destination` parameter.

The intermediary bases routing on the value passed in the `X-Destination` parameter.

<p></p>

### Synchronous Use Cases

<p></p>

#### Request routed through a single Inbound Gateway intermediary 

This use case supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination.

Below is the main flow of this use case.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination's public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory. 

This address is conventionally a URL (the destination's FHIR service base URL). However, in a variation described below, the address consists of a URL paired with a value to be conveyed in the FHIR request's HTTP header.

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

The originator system then initiates a RESTful exchange (for example, a GET method to retrieve a FHIR resource) that is transmitted to the destination's public FHIR service address. 

**Step 3. The exchange is received by the Inbound Gateway intermediary**

The intermediary receives the request on behalf of the destination. The sections below describe multiple approaches for constructing the destination's public FHIR service address, but in all cases its URL resolves to a destination-specific location at the intermediary.

**Step 4. The intermediary routes the exchange to the destination**

The intermediary determines where to route the exchange based on its arrangement with the destination owner. 

Because the destination's public URL resolves to a destination-specific location at the intermediary--the intermediary can recognize the intended receiver and determine the correct routing to that destination. 

**Step 5. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--will match the public FHIR service address used by the originator.

**Step 6. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

**Step 7. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

***FHIR service address option 1 - Base URL reflects the destination's identity***

<div><p>
  <img src="uc-search-single-intermediary-dest-url.png" style="float:none">  
    </p>
</div>

 <p></p>

***FHIR service address option 2 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-single-intermediary-int-url-plus-segment.png" style="float:none">  
    </p>
</div>

<p></p>

***FHIR service address option 2 - Intermediary's Base URL with routing metadata passed in an HTTP header parameter***

<div><p>
  <img src="uc-search-single-intermediary-int-url-header.png" style="float:none">  
    </p>
</div>

<p></p>

#### Destination uses an Inbound Gateway Intermediary that, in turn has a relationship with a Delegated Function Intermediary to deliver certain exchanges

This use case supports a situation where a second intermediary is used for delivery of a subset of requests addressed to the destination's public FHIR service address. 

Scenarios where this model can be used are those where the destination organization employs different systems or partners to  process different types of requests. For example, lab orders might need to be directed to Endpoint A, and medication orders to Endpoint B.

Below is the main flow of this use case.

**Step 1. Originator obtains the destination's public FHIR service address**

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

**Step 3. The exchange is received by the Inbound Gateway intermediary**

The initial steps in this use case are the same as in the one-intermediary use case above--the originator its request to the public FHIR service address for the destination, which is received by the Inbound Gateway intermediary to be routed onward.

**Step 4. The Inbound Gateway intermediary routes the exchange to a second intermediary**

Based on its arrangement with the destination owner, the intermediary determines that the exchange must be directed to second intermediary (the Delegated Function Intermediary) for delivery to an *alternative destination endpoint* used by the destination organization. 

The  Inbound Gateway intermediary forwards the exchange to a location at the Delegated Function Intermediary that is specific to the *alternative destination endpoint*.

**Step 5. The Delegated Function intermediary routes the exchange to the alternative destination endpoint**

The intermediary determines where to route the exchange based on its arrangement with the Inbound Gateway intermediary and/or the destination owner. 

Because the exchange was received at an endpoint specific to the *alternative destination*, the Delegated Function intermediary can recognize the intended receiver and determine the correct routing to that destination. 

**Step 6. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--will match the public FHIR service address used by the originator.

**Step 7. The Delegated Function intermediary passes through the response to the Inbound Gateway intermediary**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

**Step 8. The Inbound Gateway intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

**Step 9. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

### Asynchronous Use Cases

to be added

<p></p>

#### 