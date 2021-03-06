This section further describes details of the scenarios introduced in the [Use Cases and Roles](use-cases.html) section, and specifies approaches for "addressing" exchanges between the originating system, participating intermediaries and the ultimate destination system. 

<p></p>

### Public FHIR service address of the destination

The routing approaches described in this guide assume that the destination has a **single, public FHIR service base "address" for each FHIR service it makes available.** That address may be constructed using different conventions (see below), but in all cases:

- References to the destination's FHIR service in resources returned by the destination SHALL be consistent with the destination's single public FHIR service base address. 

For example, the Bundle.entry.fullUrl element for a resource accessible from the destination SHALL contain the same FHIR service base address as the destination would publish in a public endpoint directory.  

<p></p>

### Options for constructing the destination's public FHIR service address

Three models may be used to construct and use the destination's public FHIR service address, which all result in a conventional FHIR service base URL.

**1. Base URL that reflects the destination's identity,** such as <br/>`fhir.example-destination.com`

In this method, the destination uses a public FHIR service base URL that reflects its own identity and, in turn, hides participation of the Inbound Gateway Intermediary it employs.

The intermediary bases routing on the network IP address at which it receives the request (the IP to which the public URL's DNS record is mapped).

**2. Subdomain of intermediary's URL that identifies the destination,** such as <br/>`example-destination.example-intermediary.com`

In this method, the destination is represented using a subdomain of the Inbound Gateway Intermediary's  URL.

The intermediary bases routing on the network IP address to which the subdomain's DNS record is mapped.

**3. Intermediary's base URL followed by a path indicating the destination**, such as <br/>
`fhir.example-intermediary.com/example-destination`

In this method, the destination's public FHIR service URL consists of:

- a base URL reflecting the Inbound Gateway Intermediary's identity
- a path segment following the base that reflects the destination's identity

The intermediary bases routing on the path segment that indicates the destination.

<p></p>

### Response Content URL Rewriting

In order to fulfill this guide's requirement that references to the destination's base service address match its public FHIR service base address, the intermediary may need to rewrite full FHIR service URLs contained in FHIR resources returned by the destination (e.g., in an `.endpoint` or `Bundle.entry.fullURL` element) to match the URL to which the originator submitted the request.

For example, in a search submitted to `http//dest12345.intermediary.com`  (using option 2 above, *Subdomain of intermediary's URL that reflects the destination's identity*):

- if the response returned from the destination to the intermediary contains this entry.fullUrl value, which reflects a private destination server address:
  `http://fhir.destination.com/Patient/1`
- the intermediary rewrites the fullUrl value to
  `http://dest12345.intermediary.com/Patient/P1`

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

***FHIR service address option 1 - Base URL reflects the destination's identity***

<div><p>
  <img src="uc-search-single-intermediary-dest-url.png" style="float:none">  
    </p>
</div>
<p></p>

***FHIR service address option 2 - Subdomain of intermediary's URL identifies the destination***

<div><p>
  <img src="uc-search-single-intermediary-subdomain.png" style="float:none">  
    </p>
</div>
<p></p>

***FHIR service address option 3 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-single-intermediary-int-url-plus-segment.png" style="float:none">  
    </p>
</div>
<p></p>

<p></p>

<p></p><hr><p></p>

#### Scenario: Destination uses an Inbound Gateway Intermediary that in turn uses a Delegated Function Intermediary to deliver certain exchanges

This scenario supports a situation where a second intermediary is used for delivery of a subset of requests addressed to the destination's public FHIR service address. 

Scenarios where this model can be used are those where the destination organization employs different systems or partners to  process different types of requests. For example, lab orders might need to be directed to Endpoint A, and medication orders to Endpoint B.

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

**Step 3. The exchange is received by the Inbound Gateway intermediary**

The initial steps in this scenario are the same as in the one-intermediary scenario above--the originator its request to the public FHIR service address for the destination, which is received by the Inbound Gateway intermediary to be routed onward.

**Step 4. The Inbound Gateway intermediary routes the exchange to a second intermediary**

Based on its arrangement with the destination owner, the intermediary determines that the exchange must be directed to a second intermediary (the Delegated Function Intermediary) for delivery to an *alternative destination endpoint* used by the destination organization. 

The Inbound Gateway intermediary forwards the exchange to a location at the Delegated Function Intermediary that is specific to the *alternative destination endpoint*.

**Step 5. The Delegated Function intermediary routes the exchange to the alternative destination endpoint**

The intermediary determines where to route the exchange based on its arrangement with the Inbound Gateway intermediary and/or the destination owner. 

Because the exchange was received at an endpoint specific to the *alternative destination*, the Delegated Function intermediary can recognize the intended receiver and determine the correct routing to that destination. 

**Step 6. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--SHALL either:

- be populated by the destination server to match the public FHIR service address used by the originator

or

- be adjusted by the intermediary in the following step to match the public FHIR service address used by the originator. See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 7. The Delegated Function intermediary passes through the response to the Inbound Gateway intermediary**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. Based on previous arrangement with the destination, it may rewrite references to the destination's FHIR service in resources returned by the destination.  See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 8. The Inbound Gateway intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It may rewrite references to the destination's FHIR service in resources returned by the destination.  See [Response Content URL Rewriting](#response-content-url-rewriting) above.

**Step 9. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

**Exchange Flows**

***FHIR service address option 1 - Base URL reflects the destination's identity***

<div><p>
  <img src="uc-search-two-intermediaries-dest-url.png" style="float:none">  
    </p>
</div>
<p></p>

***FHIR service address option 2 - Subdomain of intermediary's URL identifies the destination***

<div><p>
  <img src="uc-search-two-intermediaries-int-subdomain.png" style="float:none">  
    </p>
</div>
<p></p>

***FHIR service address option 3 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-two-intermediaries-int-url-plus-segment.png" style="float:none">  
    </p>
</div>
<p></p>

<p></p>

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

**Exchange Flows**

***FHIR service address option 1 - Base URL reflects the destination's identity***

<div><p>
  <img src="uc-async-single-intermediary-dest-url.png" style="float:none">  
    </p>
</div>



 <p></p>

***FHIR service address option 2 - Subdomain of intermediary's URL identifies the destination***

<div><p>
  <img src="uc-async-single-intermediary-subdomain.png" style="float:none">  
    </p>
</div>


<p></p>

***FHIR service address option 3 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-async-single-intermediary-int-url-plus-segment.png" style="float:none">  
    </p>
</div>


<br>