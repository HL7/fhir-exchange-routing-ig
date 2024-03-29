This guide supports scenarios where intermediaries take part in RESTful FHIR exchanges between an initiating system (the "originator") and the desired endpoint system (the "destination").  

Potential applications of this initial IG include use by implementers of payer/provider use cases such as Da Vinci value-based care use case in which intermediaries may bridge connectivity between actors. Other HL7 accelerators (CARIN, Gravity, etc.) are developing disparate actor use cases in which intermediaries may be involved.

Specifically, it focuses on scenarios where:

- the originator directs the exchange to the destination's public FHIR service base URL, for example as retrieved from an endpoint directory

- but the destination has an arrangement where an intermediary accepts the request on the destination's behalf and then routes it to the destination's system -- either directly or through another intermediary.

This page defines the roles that intermediaries play in the scenarios addressed in this guide and then describes those scenarios more fully.

<p></p>

### Intermediary Capability Roles

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
      <td>The inbound gateway assumes the responsibility of establishing transport security with submitters to the public URL and potentially performing other work on behalf of the destination such as cloud-scale infrastructure, technical support, business/operational onboarding and support, actor agnosticism, and attack protection such as denial of service.</td>
      <td>Public DNS entries are used to “point” the destination’s public URL to this intermediary
        </td>
    </tr>
    <tr>
      <td>Delegated  Intermediary</td>
      <td>This intermediary role assists in routing from a different intermediary to the destination system</td>
      <td>This intermediary may serve as a public network to which the destination system is connected</td>
      <td>Intermediaries share non-public routing information to pre-arrange for delivery to the ultimate destination</td>
    </tr>
  </tbody>
</table>





<p></p>

### Scenario Overview

While there are many potential exchange scenarios in which an intermediary could play a part, this guide focuses on a specific set where:

- the exchange is a RESTful FHIR interaction or complementary approach such as CDS hooks
- an intermediary plays the **inbound gateway** role described above, accepting requests from the originator
- one or more additional intermediaries optionally play the **delegated function Intermediary** role described above, assisting in routing to the destination
- the originator directs its request to the destination’s public FHIR service URL, and the destination likewise uses that public FHIR service URL when referring to itself in the response (the destination populates elements referencing the destination server in returned FHIR resources, e.g., fullUrl, using the pubic FHIR service base URL)
- trust has been established or is negotiated between the originating client and the destination; any participating intermediaries play only a pass-through role in authentication and authorization (see [Pass-Through Security](security.html#pass-through-security))
- the originator may be unaware that an intermediary will play a role in routing the request, and has no additional responsibilities beyond those in a typical point-to-point FHIR exchange.

<p></p>
<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 1: High-level Exchange Overview</strong></figcaption>
    <img src="supported.png" style="float:none; width:1000px" title="Figure 1: High-level Exchange Overview"/> 
  </figure>
</div>
<p></p>


### Excluded scenarios

This implementation guide does not support environments or scenarios where:

- trust is not negotiated between the originating client and the destination. For example, scenarios where the client establishes trust with an intermediary instead of the destination are not supported
- the originating client addresses requests to anything but the destination’s public URL (for example, instead submitting to a different, network-assigned URL representing the destination)
- the destination does not respond using its pubic FHIR service base URL in elements that reference the destination's server within returned FHIR resources (for example, populating elements such as fullUrl with a non-public system URL)
- one or more intermediaries rewrite URLs representing the destination server in returned FHIR resources.

In addition, this guide does not provide guidance for the use of [FHIR Messaging](https://www.hl7.org/fhir/messaging.html), which also includes routing features that can be used to exchange message content through intermediaries.

<p></p>



### Assumptions: Participant Agreements and Setup Steps

This guide assumes that the destination and intermediary participants have established business arrangements and performed the following configuration steps prior to processing requests:

- The destination arranges for an inbound gateway intermediary to accept FHIR requests on its behalf
- The destination and inbound gateway intermediary establish the destination's public FHIR service base URL. The parties determine the structure of the URL to meet their preferences and technical requirements. For example, the URL's hostname may refer to either the destination or the intermediary and path segments may be included to identify the destination, specify the supported FHIR version, etc.
  - This guide does not prescribe a particular URL structure to be used; it can be any valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) that originators can use without knowledge of its individual components--as they do any other FHIR service URL
- The intermediary or destination configures its public FHIR service base URL so that requests resolve to the inbound gateway intermediary
- Participants share private server addresses as needed and pre-configure internal routing mechanisms to support forwarding through any delegated intermediaries and ultimately delivery to the destination system
  - This guide does not prescribe particular routing logic or technical approaches to be used by the intermediary
- (Optional) One or more public endpoint directories are configured to support searching for the destination and return of the destination's public FHIR service URL 

<p></p>

Scenarios are separated into two groups below:

- **synchronous** exchanges, in which participating intermediaries stay connected until the destination's response is forwarded to the originator
  - Scenario: [Request routed through a single inbound gateway intermediary](#request-routed-through-a-single-inbound-gateway-intermediary)
  - Scenario: [Destination uses an inbound gateway intermediary that, in turn routes to another intermediary to deliver the exchange](#destination-uses-an-inbound-gateway-intermediary-that-in-turn-routes-to-another-intermediary-to-deliver-the-exchange)
- **asynchronous** exchanges that follow the [FHIR Asynchronous Request pattern](http://hl7.org/fhir/async.html), as used in the [Bulk Data Access IG](https://hl7.org/fhir/uv/bulkdata/index.html)
  - Scenario: [Originator initiates an asynchronous retrieval of data from a Destination that uses an inbound gateway Intermediary](#originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)

<p></p>

### Synchronous Scenarios

<p></p>

#### Request routed through a single inbound gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination. The destination processes the request and synchronously returns its response.

- The originator obtains the destination's public FHIR service base URL, for example from a FHIR endpoint directory

- The originator initiates a RESTful interaction using the destination's public FHIR service base URL

- The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL

- The intermediary routes the exchange to the destination

- The destination processes the request and returns its response to the intermediary, populating any references to the destination's FHIR server address using its public FHIR service base URL 

- The intermediary passes through the response to the originator

- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-request-routed-through-a-single-inbound-gateway-intermediary)*

<p></p>

#### Destination uses an inbound gateway intermediary that in turn routes to another intermediary to deliver the exchange

This scenario supports a situation where one or more intermediaries participate in delivering the exchange to the destination system. 

Steps
- Originator obtains the destination's public FHIR service address
- Originator initiates a RESTful interaction using the destination's public FHIR service address
- The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL
- The inbound gateway intermediary routes the exchange to a second intermediary. Based on its arrangement with the destination owner or other intermediary(ies), the inbound gateway intermediary determines that the exchange must be directed to a different intermediary for delivery to the endpoint used by the destination organization
- The delegated intermediary routes the exchange to the destination endpoint. (Optionally, one or more intermediaries may also participate in the exchange prior to delivery to the destination)
- The destination processes the request and returns its response to the last intermediary
- The delegated intermediary passes through the response to the inbound gateway intermediary
- The inbound gateway intermediary, which is holding a synchronous connection with the originator, passes through the response
- The originator receives the response

*[Exchange flows and additional details](specification.html#scenario-destination-uses-an-inbound-gateway-intermediary-that-in-turn-routes-through-another-intermediary-to-deliver-the-exchange)*

<p></p>

### Asynchronous Scenario

#### Originator initiates an asynchronous retrieval of data from a Destination that uses an inbound gateway Intermediary

In this scenario, the originator uses the [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) to retrieve data from a destination that uses an inbound gateway intermediary as described above. 

Steps
- Originator obtains the destination's public FHIR service address
- Originator initiates a RESTful interaction using the destination's public FHIR service address. In this scenario, the originator populates the `Prefer` HTTP header with `respond-async` to notify the destination that it want to obtain the response data asynchronously
- The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL
- The intermediary routes the exchange to the destination. The `Prefer: respond-async` header is also forwarded to the destination
- The destination processes the request and returns the location to poll for status to the intermediary. The destination responds with an HTTP status code of `202 Accepted`, and an HTTP header containing a `Content-Location` parameter that specifies the URL at which status of the request will be available
- The intermediary passes through the response to the originator
- The originator receives the response
- The originator polls for status until the server responds with a "completed" HTTP status code 200 and body containing one or more URLs at which to retrieve the response data
- The originator retrieves the response data using the URL(s) returned in the completed status message from the destination

*[Exchange flows and additional details](specification.html#scenario-originator-initiates-an-asynchronous-retrieval-of-data-from-a-destination-that-uses-an-inbound-gateway-intermediary)*

<br/>