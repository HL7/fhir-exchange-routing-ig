This section further describes details of the scenarios introduced in the [Use Cases and Roles](use-cases.html) section, and specifies approaches for "addressing" exchanges between the originating system, participating intermediaries and the ultimate destination system. 

<p></p>

### Assumptions: Partnership and setup between the originator and intermediaries

This guide assumes that the destination and intermediary participants have established business arrangements and performed configuration activities prior to processing requests. These SHALL include the following, but MAY include the setup of additional value-add services performed by the intermediary participants.

- The Inbound Gateway intermediary SHALL agree to accept FHIR requests on the destination's behalf
- One or more additional delegated intermediaries MAY optionally agree to participate in the routing of requests between the Inbound Gateway intermediary and the destination
- The destination and Inbound Gateway intermediary establish the destination's [public FHIR service base URL](#destinations-public-fhir-service-base-url) according to their needs
- If the hostname in the destination's public FHIR service base URL is maintained by the destination organization, the destination SHALL configure DNS entries for its public FHIR service base URL so that requests resolve to the Inbound Gateway intermediary
- Each participating intermediary SHALL configure its internal routing mechanisms to enable forwarding of requests to either the destination system or another participating intermediary, as prearranged among the parties. Note: This guide does not prescribe particular routing logic or technical approaches to be used by an intermediary
- One or more public endpoint directories MAY be configured to support searching for the destination and return of the destination's public FHIR service URL 

<p></p>

### FHIR service base URLs 

#### Destination's public FHIR service base URL

To support the routing approaches described in this guide, the destination SHALL have a single, public FHIR service base URL for each FHIR service it makes available. 

The partnering destination and Inbound Gateway intermediary are free to determine the appropriate structure of the public FHIR service base URL to meet their preferences and technical requirements. For example, the URL's hostname may refer to either the destination or the intermediary and path segments may be included to identify the destination, specify the supported FHIR version, etc. 

<blockquote class="note-to-balloters">
<p>
Feedback requested: Is it helpful to include the following example approaches? Or would it be better to omit them--given that the guide does not prescribe a particular URL structure?</p>
</blockquote>

Example approaches:

- *Intermediary's base URL followed by a path indicating the destination,* such as `fhir.example-intermediary.com/example-destination`. In this method, the destination's public FHIR service URL consists of a base URL reflecting the Inbound Gateway Intermediary's identity followed by a path segment following the base that reflects the destination's identity. The intermediary may then base routing on the path segment that indicates the destination.
- *Base URL that reflects the destination's identity,* such as`fhir.example-destination.com`. In this method, the destination uses a public FHIR service base URL that reflects its own identity and, in turn, hides participation of the Inbound Gateway Intermediary it employs. The intermediary bases routing on the network IP address at which it receives the request (the IP to which the public URL's DNS record is mapped).
- *Subdomain of intermediary's URL that identifies the destination,* such as `example-destination.example-intermediary.com`. In this method, the destination is represented using a subdomain of the Inbound Gateway Intermediary's URL. The intermediary bases routing on the network IP address to which the subdomain's DNS record is mapped.

This guide does not prescribe a particular URL structure to be used; however, it SHALL be a valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) that originators can use without knowledge of its individual components--as they do any other FHIR service URL

#### Private FHIR service base URLs used in routing between intermediaries and to the actual destination server

The destination and each delegated intermediary (participating in exchange hops following the Inbound Gateway intermediary) SHALL define its own URL at which it accepts requests intended for the destination.  Each party is free to determine the appropriate structure of the URL to meet its preferences and technical requirements, as described in the preceding section.

This guide does not prescribe a particular URL structure to be used; however, it SHALL: 

- be a valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) 
- be a unique URL associated only with the destination.

#### Sharing of private URLs among the destination and partner intermediaries

Private URLs are shared between participating parties as needed to facilitate routing during the exchange. This guide does not assume that a participant will be aware of the URLs used by all "hops" in the exchange, but only its own URL and that of the next participant to which it forwards a request.

<p></p>

### References to the service base URL in returned FHIR resources

References to the destination's FHIR service in resources returned by the destination SHALL be consistent with the destination's single public FHIR service base address.  

For example, the Bundle.entry.fullUrl element for a resource accessible from the destination SHALL contain the same FHIR service base address as the destination would publish in a public endpoint directory.  

Those references SHALL either be: 

- produced by the destination server to match the public service base URL 
  or
- rewritten by an intermediary while returning the destination's response to match the public service base URL. See [Response Content URL Rewriting](#response-content-url-rewriting) below.

<p></p>

<p></p>

### Response Content URL Rewriting

In order to fulfill this guide's requirement that references to the destination's base service address match its public FHIR service base address, the intermediary may need to rewrite full FHIR service URLs contained in FHIR resources returned by the destination (e.g., in an `.endpoint` or `Bundle.entry.fullURL` element) and in HTTP header parameters (e.g., `Content-Location`) to match the URL to which the originator submitted the request.

For example, in a search submitted to `http//dest12345.intermediary.com`  (using option 2 above, *Subdomain of intermediary's URL that reflects the destination's identity*):

- if the response returned from the destination to the intermediary contains this entry.fullUrl value, which reflects a private destination server address:
  `http://fhir.destination.com/Patient/1`
- the intermediary rewrites the fullUrl value to
  `http://dest12345.intermediary.com/Patient/P1`

The destination and intermediary are expected to establish in advance the situations in which  base service addresses are either: 

- populated by the destination to match the public FHIR service address used by the originator

  or

- adjusted in the following step by the intermediary to match the public FHIR service address used by the originator.

But in either case, references to the destination's base service address that are returned to the originator SHALL always match the public FHIR service base address submitted on the request.

<p></p>

### Synchronous Scenarios

<p></p>

#### Scenario: Request routed through a single Inbound Gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination.

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination's public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory. 

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

The originator system then initiates a RESTful exchange (for example, a GET method to retrieve a FHIR resource) that is transmitted to the destination's public FHIR service address. 

**Step 3. The exchange is received by the Inbound Gateway intermediary**

The intermediary receives the request on behalf of the destination. The sections below describe multiple approaches for constructing the destination's public FHIR service address, but in all cases its URL resolves to a destination-specific location at the intermediary.

**Step 4. The intermediary routes the exchange to the destination**

The intermediary determines where to route the exchange based on its arrangement with the destination owner. 

Because the destination's public URL resolves to a destination-specific location at the intermediary--the intermediary can recognize the intended receiver and determine the correct routing to that destination. 

**Step 5. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--SHALL either:

- be populated by the destination server to match the public FHIR service address used by the originator

or

- be adjusted by the intermediary in the following step to match the public FHIR service address used by the originator. See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 6. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. Based on previous arrangement with the destination, it may rewrite references to the destination's FHIR service in resources returned by the destination.  See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 7. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

**Exchange flow**

<blockquote class="note-to-balloters">
<p>
    Feedback requested: 
</p>
<p>
DRAFT IG NOTE: Would it be helpful to include multiple examples using different URL approaches? Or is it better to include just one--given that the guide does not prescribe a particular URL structure?
</p>
<p>
DRAFT IG NOTE: Do we want to show URL re-writing here? Or indicate that the default expectation is that the destination server uses the public FHIR service URL in returned resources?
</p>
</blockquote>


***Example - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-single-intermediary-int-url-plus-segment.png" style="float:none; width:1000px">  
    </p>
</div>
<p></p>

#### Scenario: Destination uses an Inbound Gateway Intermediary that in turn routes through another intermediary to deliver the exchange

This scenario supports a situation where one or more additional "delegated" intermediaries--beyond the Inbound Gateway with with the originator directly interacts--is used to deliver a request to its ultimate destination. 

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

**Step 3. The exchange is received by the Inbound Gateway intermediary**

The initial steps in this scenario are the same as in the one-intermediary scenario above--the originator submits its request to the public FHIR service address for the destination, which is received by the Inbound Gateway intermediary to be routed onward.

**Step 4. The Inbound Gateway intermediary routes the exchange to a second intermediary**

The intermediary forwards the exchange to a second "delegated" intermediary who will forward it on toward the destination organization. 

This determination is based on arrangements previously established between this intermediary, the destination owner and/or participating intermediaries.

*Note: This implementation guide does not prescribe the nature of such arrangements nor the manner by which intermediaries discover or represent them within their systems.*

**Step 5. The second intermediary routes the exchange to the destination endpoint**

Based on routing metadata conveyed to it in the URL path or via HTTP headers, the second intermediary determines where to route the exchange based on its arrangement with the Inbound Gateway intermediary and/or the destination owner. 

**Step 6. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--SHALL either:

- be populated by the destination server to match the public FHIR service address used by the originator

or

- be adjusted by the intermediary in the following step to match the public FHIR service address used by the originator. See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 7. The second intermediary passes through the response to the Inbound Gateway intermediary**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. Based on previous arrangement with the destination, it may rewrite references to the destination's FHIR service in resources returned by the destination.  See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 8. The Inbound Gateway intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It may rewrite references to the destination's FHIR service in resources returned by the destination.  See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 9. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>



<blockquote class="note-to-balloters">
<p>
DRAFT IG NOTE: How/do we want to show URL re-writing here?
</p>
<p>
DRAFT IG NOTE: Would it be helpful to include multiple examples using different URL approaches? Or is it better to include just one--given that the guide does not prescribe a particular URL structure?
</p>
</blockquote>

**Exchange Flow**

***Example - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-two-intermediaries-int-url-plus-segment.png" style="float:none">  
    </p>
</div>
<p></p>







<p></p><hr><p></p>

### Asynchronous Scenario

<p></p>

#### Scenario: Originator initiates an asynchronous retrieval of data from a Destination that uses an Inbound Gateway Intermediary

In this scenario, the originator uses the [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) to retrieve data from a destination that uses an Inbound Gateway intermediary as described above. 

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

In this scenario, the originator populates the `Prefer` HTTP header with `respond-async` to notify the destination that it want to obtain the response data asynchronously.

**Step 3. The exchange is received by the Inbound Gateway intermediary**

**Step 4. The intermediary routes the exchange to the destination**

The `Prefer: respond-async` header is also forwarded to the destination.

**Step 5. The destination processes the request and returns its response to the intermediary**

The destination responds with an HTTP status code of `202 Accepted`, and an HTTP header containing a `Content-Location` parameter that specifies the URL at which the response data will be available.

The `Content-Location` base SHALL either be: 

- populated by the destination to match the public FHIR service address used by the originator

or

- adjusted in the following step by the intermediary to match the public FHIR service address used by the originator.

**Step 6. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. Based on previous arrangement with the destination, it may modify the `Content-Location` base to match the public FHIR service address used by the originator.

**Step 7. The originator receives the response**

The originator accepts the response.

**Step 8. The originator later retrieves the response data**

The originator later retrieves the response data using the address previously returned in the `Content-Location` parameter of the initial response from the destination. This request and response are routed as described in Steps 1-7, except that the `Prefer: respond-async` parameter is not used.

<p></p>

<blockquote class="note-to-balloters">
<p>
DRAFT IG NOTE: How/do we want to show URL re-writing here?
</p>
<p>
DRAFT IG NOTE: Would it be helpful to include multiple examples using different URL approaches? Or is it better to include just one--given that the guide does not prescribe a particular URL structure?
</p>
</blockquote>

**Exchange Flow**

***Example - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-async-single-intermediary-int-url-plus-segment.png" style="float:none">  
    </p>
</div>

<br>