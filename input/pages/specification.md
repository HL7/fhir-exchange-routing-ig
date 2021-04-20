This section further describes details of the scenarios introduced in the [Use Cases and Roles](use-cases.html) section, and specifies approaches for "addressing" exchanges between the originating system, participating intermediaries and the ultimate destination system. 

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

### Synchronous Scenarios

<p></p>

#### Request routed through a single Inbound Gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination.

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination's public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory. 

This address is conventionally a URL (the destination's FHIR service base URL). However, in one of the variations described below, the address consists of a URL paired with a value to be conveyed in the FHIR request's HTTP header (also see *Options for constructing the public FHIR service address, above*)

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

#### Destination uses an Inbound Gateway Intermediary that in turn uses a Delegated Function Intermediary to deliver certain exchanges

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

Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--will match the public FHIR service address used by the originator.

**Step 7. The Delegated Function intermediary passes through the response to the Inbound Gateway intermediary**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

**Step 8. The Inbound Gateway intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

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

***FHIR service address option 2 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-search-two-intermediaries-int-url-plus-segment.png" style="float:none">  
    </p>
</div>



<p></p>

***FHIR service address option 2 - Intermediary's Base URL with routing metadata passed in an HTTP header parameter***

<div><p>
  <img src="uc-search-two-intermediaries-int-url-header.png" style="float:none">  
    </p>
</div>


<p></p>

### Asynchronous Scenarios

#### Originator initiates an asynchronous retrieval of data from a Destination that uses an Inbound Gateway Intermediary

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

It is expected that the `Content-Location` base will match the public FHIR service address used by the originator.

**Step 6. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. It does not modify the response in any way.

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

***FHIR service address option 2 - Intermediary's Base URL followed by a path indicating the destination***

<div><p>
  <img src="uc-async-single-intermediary-int-url-plus-segment.png" style="float:none">  
    </p>
</div>



<p></p>

***FHIR service address option 2 - Intermediary's Base URL with routing metadata passed in an HTTP header parameter***

<div><p>
  <img src="uc-async-single-intermediary-int-url-header.png" style="float:none">  
    </p>
</div>


<p></p>

#### Originator pushes data to a destination that uses an Inbound Gateway intermediary and obtains processing results asynchronously

***[Need to investigate how/whether this scenario is supported by the [FHIR asynchronous request pattern](https://www.hl7.org/fhir/async.html), which only describes an async GET interaction ]***

In this scenario, the originator uses the [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) to push data to the destination and then later retrieve processing results asynchronously. The destination uses an Inbound Gateway intermediary as described above. 

**Steps**

This scenario is a variation on the previous "asynchronous data retrieval" scenario--differing only in that the initial exchange POSTs data to the destination for processing, rather than the previous scenarios use of a GET data request.

**Exchange Flows**

See flows for previous asynchronous scenario. Only difference is that the initial Originator exchange to the Destination is a POST of data.



<br>