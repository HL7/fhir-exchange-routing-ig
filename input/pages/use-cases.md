This guide supports scenarios where intermediaries take part in RESTful FHIR exchanges between an initiating system (the "originator") and the desired endpoint system (the "destination").  It focuses on scenarios where:

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory
- but the destination has an arrangement where an intermediary accepts the request on the destination's behalf and then routes it to the destination's system -- either directly or through another intermediary.

This page defines the roles that intermediaries play in the scenarios addressed in this guide and then describes those scenarios more fully.

### Intermediary Roles

<table class="grid">
  <thead>
    <tr>
      <th>Intermediary  Role</th>
      <th>Description</th>
      <th>Benefit provided</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="4"><strong><em>Supported in this guide:</em></strong></td>
    </tr>
    <tr>
      <td>Inbound  Gateway</td>
      <td>This intermediary role receives requests intended for the destination and  forwards them to the destination system or another intermediary</td>
      <td>The Inbound Gateway assumes the responsibility of securing a public endpoint and potentially performing other work (registering clients, etc.) on behalf of the destination</td>
      <td>Public DNS entries are used to “point” the destination’s public URL to this intermediary. Common intermediary role for financial and administrative data exchange</td>
    </tr>
    <tr>
      <td>Delegated  Intermediary</td>
      <td>This intermediary role assists in routing from a different intermediary to the destination system</td>
      <td>This intermediary may serve as a public network or hub to which the destination system is connected</td>
      <td>Intermediaries may share non-public routing information to support delivery to the ultimate destination</td>
    </tr>
  </tbody>
</table>





<p></p>

### Scenario Overview

While there are many potential exchange scenarios in which an intermediary could play a part, this guide focuses on a specific set where:

- the exchange is a RESTful FHIR interaction
- the originator directs its exchange to the intended destination's public FHIR service address
- the originator may be unaware that an intermediary will play a role in routing the request, and has no additional responsibilities beyond those in a typical point-to-point FHIR exchange, and
- an intermediary plays the **Inbound Gateway** role described above, accepting requests from the originator
- one or more additional intermediaries optionally play the **Delegated Function Intermediary** role described above, assisting in routing to the destination.

Scenarios are separated into two groups below:

- **synchronous** exchanges, in which participating intermediaries stay connected until the destination's response is forwarded to the originator
  - Scenario: [Request routed through a single Inbound Gateway intermediary](#request-routed-through-a-single-inbound-gateway-intermediary)
  - Scenario: [Destination uses an Inbound Gateway intermediary that, in turn routes to another intermediary to deliver the exchange](#destination-uses-an-inbound-gateway-intermediary-that-in-turn-routes-to-another-intermediary-to-deliver-the-exchange)
- **asynchronous** exchanges that follow the [FHIR Asynchronous Request pattern](http://hl7.org/fhir/async.html), as used in the [Bulk Data Access IG](https://hl7.org/fhir/uv/bulkdata/index.html)
  - Scenario: [Originator initiates an asynchronous retrieval of data from a Destination that uses an Inbound Gateway Intermediary](#originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)

<p></p>

### Excluded scenarios 

This guide does not provide guidance for the use of [FHIR Messaging](https://www.hl7.org/fhir/messaging.html), which also includes routing features that can be used to exchange message content through intermediaries.

<p></p>

### Assumptions and Setup Steps

This guide assumes that the destination and intermediary participants have established business arrangements and performed the following configuration steps prior to processing requests:

- The destination arranges for an Inbound Gateway intermediary to accept FHIR requests on its behalf
- The destination and Inbound Gateway intermediary establish the destination's public FHIR service base URL. The parties determine the structure of the URL to meet their preferences and technical requirements. For example, the URL's hostname may refer to either the destination or the intermediary and path segments may be included to identify the destination, specify the supported FHIR version, etc.
  - This guide does not prescribe a particular URL structure to be used; it can be any valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) that originators can use without knowledge of its individual components--as they do any other FHIR service URL
- The destination configures DNS entries for its public FHIR service base URL so that requests resolve to the Inbound Gateway intermediary
- The destination shares its private server address with the Inbound Gateway intermediary, and the intermediary configures its internal routing mechanisms to enable it to forward requests received at the destination's public FHIR service base URL to either the destination system or another participating intermediary
  - This guide does not prescribe particular routing logic or technical approaches to be used by the intermediary
- (Optional) One or more public endpoint directories are configured to support searching for the destination and return of the destination's public FHIR service URL 

<p></p>

### Synchronous Scenarios

<p></p>

#### Request routed through a single Inbound Gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination. The destination processes the request and synchronously returns its response.

- The originator obtains the destination's public FHIR service base URL, for example from a FHIR endpoint directory
- The originator initiates a RESTful interaction using the destination's public FHIR service base URL
- The exchange is received by the Inbound Gateway intermediary
- The intermediary routes the exchange to the destination
- The destination processes the request and returns its response to the intermediary, first [rewriting certain resource URLs](specification.html#response-content-url-rewriting) if needed 
- The intermediary passes through the response to the originator
- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-request-routed-through-a-single-inbound-gateway-intermediary)*

<p></p>

#### Destination uses an Inbound Gateway Intermediary that in turn routes to another intermediary to deliver the exchange

This scenario supports a situation where one or more intermediaries participate in delivering the exchange to the destination system. 

Steps

- Originator obtains the destination's public FHIR service address

- Originator initiates a RESTful interaction using the destination's public FHIR service address

- The exchange is received by the Inbound Gateway intermediary

- The Inbound Gateway intermediary routes the exchange to a second intermediary. Based on its arrangement with the destination owner or other intermediary(ies), the Inbound Gateway intermediary determines that the exchange must be directed to a different intermediary for delivery to the endpoint used by the destination organization

- The delegated intermediary routes the exchange to the destination endpoint. (Optionally, one or more intermediaries may also participate in the exchange prior to delivery to the destination)

- The destination processes the request and returns its response to the last intermediary

- The delegated intermediary passes through the response to the Inbound Gateway intermediary, first [rewriting certain resource URLs](specification.html#response-content-url-rewriting) if needed

- The Inbound Gateway intermediary, which is holding a synchronous connection with the originator, passes through the response, first [rewriting certain resource URLs](specification.html#response-content-url-rewriting) if needed

- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-destination-uses-an-inbound-gateway-intermediary-that-in-turn-routes-through-another-intermediary-to-deliver-the-exchange)*

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

- The intermediary passes through the response to the originator

- The originator receives the response

- The originator later retrieves the response data using the address previously returned in the `Content-Location` parameter of the initial response from the destination

*[Exchange flows and additional details](specification.html#scenario-originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)*

<br/>