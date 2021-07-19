This guide supports scenarios where intermediaries take part in RESTful FHIR exchanges between an initiating system (the "originator") and the desired endpoint system (the "destination").  It focuses on scenarios where:

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory
- but the destination has an arrangement where an intermediary accepts the request on the destination's behalf and then routes it--directly or through another intermediary--to the destination's system.

This page defines the roles that intermediaries play in the scenarios addressed in this guide and then describes those scenarios more fully.

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
      <td>The  originator directs some or all of its requests to the intermediary, who  forwards them to the desired responders. Specifically, the  originator directs each request to the same intermediary FHIR service base URL--with informational content in the payload--that the intermediary uses to determine and route to the appropriate recipient.</td>
      <td>Originator  delegates to the intermediary the searching and business logic to identify a  responder that fits the originator’s criteria.  Often  connected to originator via an VPN.</td>
      <td>Used in  e-prescribing when the sender doesn’t know the specific receiving system  (prescription renewals, certain benefit requests).     Not common in  other environments</td>
    </tr>
  </tbody>
</table>



<p></p>

### Scenario Overview

While there are many potential exchange scenarios in which an intermediary could play a part, this guide focuses on a specific set where:

- the exchange is a RESTful FHIR interaction
- the originator directs its exchange to the intended destination's public FHIR service address
- the originator is unaware that an intermediary will play a role in routing the request, and
- the intermediary plays either the **Inbound Gateway** or **Delegated Function Intermediary** roles described above.

Scenarios are separated into two groups below:

- **synchronous** exchanges, in which participating intermediaries stay connected until the destination's response is forwarded to the originator
  - Scenario: [Request routed through a single Inbound Gateway intermediary](#request-routed-through-a-single-inbound-gateway-intermediary)
  - Scenario: [Destination uses an Inbound Gateway intermediary that, in turn has a relationship with a Delegated Function intermediary to deliver certain exchanges](#destination-uses-an-inbound-gateway-intermediary-that-in-turn-uses-a-delegated-function-intermediary-to-deliver-certain-exchanges)
- **asynchronous** exchanges that follow the [FHIR Asynchronous Request pattern](http://hl7.org/fhir/async.html), as used in the [Bulk Data Access IG](https://hl7.org/fhir/uv/bulkdata/index.html)
  - Scenario: [Originator initiates an asynchronous retrieval of data from a Destination that uses an Inbound Gateway Intermediary](#originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)

<p></p>

### Excluded scenarios 

This guide does not provide guidance for: 

- situations where the originator arranges to address some or all of its requests to an intermediary--who, in turn, uses logic, provided metadata or other means to determine and route to an appropriate destination

- use of FHIR Messaging, which also includes routing features that can be used to exchange message content through intermediaries.

<p></p>

### Synchronous Scenarios

<p></p>

#### Request routed through a single Inbound Gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination. The destination processes the request and synchronously returns its response.

Steps

- Originator obtains the destination's public FHIR service address

- Originator initiates a RESTful interaction using the destination's public FHIR service address

- The exchange is received by the Inbound Gateway intermediary

- The intermediary routes the exchange to the destination

- The destination processes the request and returns its response to the intermediary. References to the destination's FHIR service in returned resources match the public FHIR service address used by the originator

- The intermediary passes through the response to the originator. It does not modify the response

- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-request-routed-through-a-single-inbound-gateway-intermediary)*

<p></p>

#### Destination uses an Inbound Gateway Intermediary that in turn uses a Delegated Function Intermediary to deliver certain exchanges

This scenario supports a situation where a second intermediary is used for delivery of a subset of requests addressed to the destination's public FHIR service address. 

Scenarios where this model can be used are those where the destination organization employs different systems or partners to  process different types of requests. For example, lab orders might need to be directed to Endpoint A, and medication orders to Endpoint B.

Steps

- Originator obtains the destination's public FHIR service address

- Originator initiates a RESTful interaction using the destination's public FHIR service address

- The exchange is received by the Inbound Gateway intermediary

- The Inbound Gateway intermediary routes the exchange to a second intermediary. Based on its arrangement with the destination owner, the intermediary determines that the exchange must be directed to a second intermediary (the Delegated Function Intermediary) for delivery to an *alternative destination endpoint* used by the destination organization

- The Delegated Function intermediary routes the exchange to the alternative destination endpoint

- The destination processes the request and returns its response to the intermediary. References to the destination's FHIR service in returned resources match the public FHIR service address used by the originator

- The Delegated Function intermediary passes through the response to the Inbound Gateway intermediary. It does not modify the response in any way

- The Inbound Gateway intermediary passes through the response to the originator. It does not modify the response

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response

- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-destination-uses-an-inbound-gateway-intermediary-that-in-turn-uses-a-delegated-function-intermediary-to-deliver-certain-exchanges)*

<p></p>

### Asynchronous Scenario

#### Originator initiates an asynchronous retrieval of data from a Destination that uses an Inbound Gateway Intermediary

In this scenario, the originator uses the [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) to retrieve data from a destination that uses an Inbound Gateway intermediary as described above. 

Steps

- Originator obtains the destination's public FHIR service address

- Originator initiates a RESTful interaction using the destination's public FHIR service address. In this scenario, the originator populates the `Prefer` HTTP header with `respond-async` to notify the destination that it want to obtain the response data asynchronously

- The exchange is received by the Inbound Gateway intermediary

- The intermediary routes the exchange to the destination. The `Prefer: respond-async` header is also forwarded to the destination

- The destination processes the request and returns its response to the intermediary. The destination responds with an HTTP status code of `202 Accepted`, and an HTTP header containing a `Content-Location` parameter that specifies the URL at which the response data will be available

- The intermediary passes through the response to the originator. It does not modify the response

- The originator receives the response

- The originator later retrieves the response data using the address previously returned in the `Content-Location` parameter of the initial response from the destination

*[Exchange flows and additional details](specification.html#scenario-originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)*

<br/>