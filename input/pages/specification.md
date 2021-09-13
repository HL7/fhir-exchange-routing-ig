This section further describes details of the scenarios introduced in the [Use Cases and Roles](use-cases.html) section, and specifies approaches for "addressing" exchanges between the originating system, participating intermediaries and the ultimate destination system. 

<p></p>

### Public FHIR service address of the destination

The routing approaches described in this guide assume that the destination has a **single, public FHIR service base URL for each FHIR service it makes available.** That address may be constructed using one of two conventions (see below), but in either case:

- References to the destination's FHIR service in resources returned by the destination SHALL be consistent with the destination's single public FHIR service base address.  

For example, the Bundle.entry.fullUrl element for a resource accessible from the destination SHALL contain the same FHIR service base address as the destination would publish in a public endpoint directory.  

Those references SHALL either be: 

- produced by the destination server to match the public service base URL 
  or
- rewritten by an intermediary while returning the destination's response to match the public service base URL. See [Response Content URL Rewriting](#response-content-url-rewriting) below.

<p></p>

### Constructing the destination's public FHIR service address

Two DNS-based approaches may be used to construct and use the destination's public FHIR service address, which all result in a conventional FHIR service base URL.

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

***Review note: Stakeholders are currently evaluating whether to include options 2 (subdomain) and 3 (app path) in the final IG***

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

<p></p>

**1. Base URL that reflects the destination's identity,** such as <br/>`fhir.example-destination.com`

In this method, the destination uses a public FHIR service base URL that reflects its own identity and, in turn, hides participation of the Inbound Gateway Intermediary it employs.

The intermediary bases routing on the network IP address at which it receives the request (the IP to which the public URL's DNS record is mapped).

<p></p>

**2. Subdomain of intermediary's URL that identifies the destination,** such as <br/>`example-destination.example-intermediary.com`

In this method, the destination is represented using a subdomain of the Inbound Gateway Intermediary's  URL.

The intermediary bases routing on the network IP address to which the subdomain's DNS record is mapped.

<p></p>

**3. "App path" option: Intermediary's base URL followed by a path indicating the destination**, such as <br/>
`fhir.example-intermediary.com/example-destination`

In this method, the destination's public FHIR service URL consists of:

- a base URL reflecting the Inbound Gateway Intermediary's identity
- a path segment following the base that reflects the destination's identity

The intermediary bases routing on the path segment that indicates the destination.

<p></p>

### Options for routing between intermediaries

Three models may be used to construct and use the destination's public FHIR service address, which all result in a conventional FHIR service base URL.

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

***Review note: Stakeholders are currently evaluating which options below to include in the final IG***

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

<p></p>

**1. HTTP header option: Intermediary's base URL accompanied by routing metadata passed in HTTP header parameters**, such as <br/>URL: `fhir.example-intermediary.com` 
HTTP header parameter: `X-Destination: example-destination`
HTTP header parameter: `X-Originator: example-originator`

In this method, the destination's public FHIR service address consists of: 

- the Inbound Gateway Intermediary's base URL 
- paired with metadata passed in the HTTP header's `X-Destination` parameter.

The intermediary bases routing on the value passed header parameters.

*Notes:*

- *In order to fulfill this guide's requirement that references to the destination's base service address match its public FHIR service base address, the HTTP routing metadata header parameters SHALL NOT be returned in the response ultimately received by the originator.*
- ***To Do: Use of the X-Originator parameter has not yet been fully defined***

<p></p>

**2. Intermediary's base URL accompanied by routing metadata passed in a query string parameter**, such as <br/>`fhir.example-intermediary.com/?_x-destination=12345`

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

***Note: Discussion in the May 2021 HL7 FHIR Connectathon determined that this approach would create URL strings that do not conform with FHIR requirements.***

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

In this method, the destination's public FHIR service address consists of: 

- the Inbound Gateway Intermediary's base URL 
- paired with metadata passed in a query string x-destination parameter appended to the FHIR request URL string. 

The parameter is:

- added to the end of the request URL if it does not otherwise contain a query string. For example, in a GET that requests a resource by its identifier, the x-destination is appended to the end of the request: `GET http://fhir.intermediary.com/Patient/P1?_x-destination=12345`
- added as an additional parameter if the request already contains a query string. For example, in a search: 
  `GET http://fhir.intermediary.com/Patient?family:exact=Cook&given=jennifer`
  `&_x-destination=12345`

The intermediary bases routing on the value passed in the `x-destination` parameter, and then strips it from the URL string before forwarding the request to the destination system.

*Response content URL rewriting.* In order to fulfill this guide's requirement that references to the destination's base service address match its public FHIR service base address, the intermediary rewrites full FHIR service URLs contained in FHIR resources returned by the destination (e.g., in an `.endpoint` or `Bundle.entry.fullURL` element) to match the URL + x-destination query string to which the originator submitted the request.

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

***FHIR service address option 1 - Base URL reflects the destination's identity***

<div><p>
  <img src="uc-search-single-intermediary-dest-url.png" style="float:none; width:1000px">  
    </p>
</div>
<p></p>

***FHIR service address option 2 - Subdomain of intermediary's URL identifies the destination***

<div><p>
  <img src="uc-search-single-intermediary-subdomain.png" style="float:none; width:1000px">  
    </p>
</div>
<p></p>

***FHIR service address option 3 - Intermediary's Base URL followed by a path indicating the destination***

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

**Exchange Flows**

***Intermediary routing option 1 - Intermediary's Base URL with routing metadata passed in HTTP header parameters***

<div><p>
  <img src="uc-search-two-intermediaries-int-url-header.png" style="float:none; width:1000px">  
    </p>
</div>

***Intermediary routing option 2 - Intermediary's Base URL with routing metadata passed in a query string parameter***

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

***Note:** Discussion in the May 2021 HL7 FHIR Connectathon determined that this approach might create URL strings that do not conform with FHIR requirements.*

\* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* \* 

<div><p>
  <img src="uc-search-two-intermediaries-query-string.png" style="float:none; width:1000px">  
    </p>
</div>
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
  <img src="uc-async-single-intermediary-dest-url.png" style="float:none; width:1000px">  
    </p>
</div>



 <p></p>

***FHIR service address option 2 - Subdomain of intermediary's URL identifies the destination***

<div><p>
  <img src="uc-async-single-intermediary-subdomain.png" style="float:none; width:1000px">  
    </p>
</div>


<p></p>

***FHIR service address option 3 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-async-single-intermediary-int-url-plus-segment.png" style="float:none; width:1000px">  
    </p>
</div>


<br>