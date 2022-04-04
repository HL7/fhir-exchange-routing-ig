This section further describes details of the scenarios introduced in the [Use Cases and Roles](use-cases.html) section, and specifies approaches for "addressing" exchanges between the originating system, participating intermediaries and the ultimate destination system. 

<p></p>

### Assumptions: Partnership and setup between the destination and intermediaries

This guide assumes that the destination and intermediary participants have established business arrangements, if required, and performed configuration activities, if required, prior to processing requests. These SHALL include the following, but MAY include the setup of additional value-add services performed by the intermediary participants.

- An intermediary, functioning as an inbound gateway, SHALL agree to accept FHIR requests on the destination's behalf
- The destination and inbound gateway intermediary SHALL establish the destination's public FHIR service base URL [(details below)](#destinations-public-fhir-service-base-url) according to their preferences
- One or more additional delegated intermediaries MAY optionally agree to participate in the routing of requests between the inbound gateway intermediary and the destination
- Each participating intermediary SHALL configure its internal routing mechanisms to enable forwarding of requests to either the destination system or another participating intermediary, as prearranged among the parties. 
  - Note: This guide does not prescribe particular routing logic or technical approaches to be used by an intermediary to other intermediaries or the destination
- One or more public endpoint directories MAY be configured to support searching for the destination and return of the destination's public FHIR service URL 

<p></p>

### FHIR service base URLs 

#### Destination's public FHIR service base URL

To support the routing approaches described in this guide, the destination SHALL have a single, public FHIR service base URL for each FHIR service it makes available. 

The partnering destination and inbound gateway intermediary are free to determine the appropriate structure of the public FHIR service base URL to meet their preferences and technical requirements. For example, the URL's hostname may refer to either the destination or the intermediary and path segments may be included to identify the destination, specify the supported FHIR version, etc. In addition, the destination SHALL  [reference its public URL in returned FHIR resources](#references-to-the-service-base-url-in-returned-fhir-resources).

<p></p>


Example approaches:

- *Intermediary's base URL followed by a path indicating the destination,* such as `fhir.example-intermediary.com/example-destination`. In this method, the destination's public FHIR service URL consists of a base URL reflecting the inbound gateway Intermediary's identity followed by a path segment following the base that reflects the destination's identity. The intermediary may then base routing on the path segment that indicates the destination.
- *Base URL that reflects the destination's identity,* such as`fhir.example-destination.com`. In this method, the destination uses a public FHIR service base URL that reflects its own identity and, in turn, hides participation of the inbound gateway Intermediary it employs. The intermediary bases routing on the network IP address at which it receives the request (the IP to which the public URL's DNS record is mapped).
- *Subdomain of intermediary's URL that identifies the destination,* such as `example-destination.example-intermediary.com`. In this method, the destination is represented using a subdomain of the inbound gateway Intermediary's URL. The intermediary bases routing on the network IP address to which the subdomain's DNS record is mapped.

Regardless of the URL structure used as the public URL, it SHALL be a valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) that originators can use without knowledge of its individual components--as they do any other FHIR service URL

<p></p>

**Considerations**
<br><br>
While this guide does not prescribe a particular structure for the public FHIR service base URL used in these exchanges, implementers might consider the factors below when deciding their URL approach:

Use of a public URL containing the intermediary's hostname, for example `fhir.intermediary1.com/myorg`, provides human-recognizable context about the intermediary relationship, independent of the technical resolution of the URL. While this does not necessarily reflect a technical constraint to changing intermediaries, use of a public URL containing the intermediary's hostname may make it more difficult for the destination to change the intermediary as it may result in a change to its public URL. 

More critically, any change to the destination's public URL–whether due to a change in intermediary or other reason--would impact originators using the original URL, creating a responsibility for the destination, intermediary or other party to make originators aware that the URL has changed so that they can start using the new one.

Preferably, URL naming will accommodate changes to actors involved in the exchange or relationships between them, to avoid impacting originators.

</div>


<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 2: FHIR Service URLs</strong></figcaption>
    <img src="one-int-routing.png" style="float:none; width:1000px" title="Figure 2: FHIR Service URLs"/>  
    </figure>
</div>
<p></p>

<blockquote class="note-to-balloters">
<p>
This implementation guide does not support URL rewriting, based on stakeholder input regarding the difficulty of reliably locating and adjusting applicable URLs, and the complexity it introduces to security aspects such as signing payload content. It may be considered in future enhancements to the IG.</p>
</blockquote>
<p></P>


#### FHIR service base URLs used in routing between intermediaries and to the actual destination server

The destination and each delegated intermediary (participating in exchange hops following the inbound gateway intermediary) defines the URL at which it accepts requests intended for the destination.  Each party is free to determine the appropriate structure of the URL to meet its preferences and technical requirements, as described in the preceding section.

This guide does not prescribe a particular URL structure to be used; however, it SHALL: 

- be a valid [FHIR service base URL](https://www.hl7.org/fhir/http.html#root) 
- be a unique URL associated only with the destination.

**Note: Handling when an HIE plays the role of an aggregator.** In the case where the HIE is the entity serving the FHIR resources (acting as an aggregator that stores/serves the resources to the originator, and there is no visibility to the source systems):

- The HIE is considered the destination, the public URL is that of the HIE, and the source entities would not be visible in the exchange

#### Sharing of URLs among the destination and partner intermediaries

"Private" FHIR service base URLs are shared between participating parties as needed to facilitate routing during the exchange. This guide does not assume that a participant will be aware of the URLs used by all "hops" in the exchange, but only its own URL and that of the next participant to which it forwards a request.

#### References to the service base URL in returned FHIR resources

References to the destination's server in FHIR resources that it produces SHALL equal the destination's single public FHIR service base address.  

For example, the Bundle.entry.fullUrl element for a resource on the destination's server SHALL contain the same FHIR service base address as the destination would publish in a public endpoint directory.  

<p></p>

### Trust Between Participants 

#### The originating client negotiates trust with the destination

In this exchange model, trust has been established or is negotiated solely between the originator and destination. It is the destination which makes the decision as to whether it trusts the originator or not; any intermediaries involved in the exchange play a passive, "pass through" role in the process. 

Required behavior:

- all exchanges in the exchange SHALL use Transport Layer Security (TLS)
- security tokens generated by the destination for use by the originator SHALL be forwarded by any intermediaries to the originating client.

See [the Security section](security.html) for additional details.

<p></p>

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 3: Trust Overview</strong></figcaption>
    <img src="one-int-trust.png" style="float:none; width:1000px" title="Figure 3: Trust Overview"/>  
  </figure>
</div>
<p></p>

<p></p>

#### Trust and routing overview - multiple intermediaries

The required behavior described above for single-intermediary scenarios apply equally when one or more additional intermediaries participate in the exchange, as illustrated below.

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 4: Mutliple Intermediary Overview</strong></figcaption>
    <img src="multi-int-overview.png" style="float:none; width:1000px" title="Figure 4: Multiple Intermediary Overview">  
   </figure>
</div>
<p></p>

<p></p>


### Unsupported Scenarios
<p></p>
<blockquote class="note-to-balloters">
<p>
This initial release of the Hybrid/Intermediary Exchange IG focuses on a range of typical intermediary routing scenarios in which exchange happens over the public internet using RESTful interactions, originators address requests directly to destinations' public FHIR service URLs, and authentication and authorization are negotiated between the originator and ultimate destination. Additional scenarios and environments may be addressed in future enhancements to the IG.
</p>
</blockquote>
<p></p>
Based on the rules and constraints described above, the following underscores scenarios that are not in scope of this implementation guide.

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 5: Unsupported Scenario Overview</strong></figcaption>
    <img src="not-supported.png" style="float:none; width:1000px" title="Figure 5: Unsupported Scenario Overview"/>  
   </figure>
</div>
<p></p>

<p></p>

### Synchronous Scenarios

<p></p>

#### Scenario: Request routed through a single inbound gateway intermediary 

This scenario supports a situation where the destination delegates the responsibility of securing a public endpoint--and potentially performing other functions such as registering clients--to an intermediary. A single intermediary participates in this exchange, forwarding requests directly from the originator to the destination.

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination's public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory. 

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

The originator system then initiates a RESTful exchange (for example, a GET method to retrieve a FHIR resource) that is transmitted to the destination's public FHIR service address. 

**Step 3. The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL**

The intermediary receives the request on behalf of the destination. The URL resolves to a destination-specific location at the intermediary.

**Step 4. The intermediary routes the exchange to the destination**

The intermediary determines where to route the exchange based on its arrangement with the destination owner. 

Because the destination's public URL resolves to a destination-specific location at the intermediary--the intermediary can recognize the intended receiver and determine the correct routing to that destination. 

**Step 5. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

_**Note - conformance requirement:**_ Any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--**SHALL** be populated by the destination server to match the public FHIR service address used by the originator.

**Step 6. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. 

**Step 7. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 6: Example - Intermediary's Base URL followed by a path indicating the destination</strong></figcaption>
    <img src="uc-search-single-intermediary-int-url-plus-segment.png" style="float:none; width:1100px" title="Example - Intermediary's Base URL followed by a path indicating the destination"/>  
  </figure>
</div>
<p></p>

#### Scenario: Destination uses an inbound gateway Intermediary that in turn routes through another intermediary to deliver the exchange

This scenario supports a situation where one or more additional "delegated" intermediaries--beyond the inbound gateway with with the originator directly interacts--is used to deliver a request to its ultimate destination. 

Below is the main flow of this scenario. The initial steps in this scenario are the same as in the one-intermediary scenario above.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination’s public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory.

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

The originator system then initiates a RESTful exchange (for example, a GET method to retrieve a FHIR resource) that is transmitted to the destination’s public FHIR service address.

**Step 3. The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL**

The originator submits its request to the public FHIR service address for the destination, and the public FHIR service base URL resolves to the inbound gateway intermediary to be routed onward.

**Step 4. The inbound gateway intermediary routes the exchange to a second intermediary**

The intermediary forwards the exchange to a second "delegated" intermediary who will forward it on toward the destination organization. 

This determination is based on arrangements previously established between this intermediary, the destination owner and/or participating intermediaries.

*Note: This implementation guide does not prescribe the nature of such arrangements nor the manner by which intermediaries discover or represent them within their systems.*

**Step 5. The second intermediary routes the exchange to the destination endpoint**

Based on routing metadata conveyed to it in the URL path or via HTTP headers, the second intermediary determines where to route the exchange based on its arrangement with the inbound gateway intermediary and/or the destination owner. 

**Step 6. The destination processes the request and returns its response to the intermediary**

The destination processes the request and synchronously returns its response. 

_**Note - conformance requirement:**_ Note that any references to the destination's FHIR service in resources that are returned by the destination--for example, in a search result Bundle's fullUrl element--**SHALL** be populated by the destination server to match the public FHIR service address used by the originator.

**Step 7. The second intermediary passes through the response to the inbound gateway intermediary**

The intermediary, which is holding a synchronous connection with the originator, passes through the response

**Step 8. The inbound gateway intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. 

**Step 9. The originator receives the response**

The originator accepts the response. If it wishes to submit a follow-on request using a reference in a received resource, it interprets the reference as typically does, without any adjustments caused by the initial request having been routed through an intermediary. This is possible because the destination uses its public FHIR address as the FHIR service base value in elements such as Bundle.fullUrl.  

<p></p>

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 7: Example - Intermediary's Base URL followed by a path indicating the destination</strong></figcaption>
    <img src="uc-search-two-intermediaries-int-url-plus-segment.png" style="float:none; width:1100px" title="Figure 7: Example - Intermediary's Base URL followed by a path indicating the destination"/>  
  </figure>  
</div>
<p></p>

<p></p><hr><p></p>

### Asynchronous Scenario

<p></p>
Note: While the section below describes a GET request of information, all methods described in [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) including Bulk Data Delete Request are supported by this IG.

#### Scenario: Originator initiates an asynchronous retrieval of data from a Destination that uses an inbound gateway Intermediary

In this scenario, the originator uses the [FHIR Asynchronous Pattern](https://www.hl7.org/fhir/async.html) to retrieve data from a destination that uses an inbound gateway intermediary as described above. 

Below is the main flow of this scenario.

**Step 1. Originator obtains the destination's public FHIR service address**

Before the exchange, the originator system obtains the destination’s public FHIR service address, for example through a reference in a previously-received FHIR resource or by consulting an endpoint directory.

**Step 2. Originator initiates a RESTful interaction using the destination's public FHIR service address**

In this scenario, the originator populates the `Prefer` HTTP header with `respond-async` to notify the destination that it want to obtain the response data asynchronously.

**Step 3. The exchange is received by the inbound gateway intermediary through resolution of the public FHIR service base URL**

The originator submits its request to the public FHIR service address for the destination, and the public FHIR service base URL resolves to the inbound gateway intermediary to be routed onward.

**Step 4. The intermediary routes the exchange to the destination**

The `Prefer: respond-async` header is also forwarded to the destination.

**Step 5. The destination processes the request and returns its response to the intermediary**

The destination processes the request and returns the location to poll for status to the intermediary. The destination responds with an HTTP status code of `202 Accepted`, and an HTTP header containing a `Content-Location` parameter that specifies the URL at which status of the request will be available

**Step 7. The intermediary passes through the response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response. 

**Step 7. The originator polls for status**

The originator polls for status until the server responds with a "completed" HTTP status code `200 OK` and body containing one or more URLs at which to retrieve the response data. 
Note: The returned content location URL(s) may contain the public FHIR service base URL used to initiate the asynchronous exchange process--so that retrieval of the content also flows through the intermediary--but that is not required.

**Step 8. The intermediary routes the status request to the destination**
The intermediary forwards the request to the destination.

**Step 9. The intermediary passes through the status response to the originator**

The intermediary, which is holding a synchronous connection with the originator, passes through the response.

**Step 10. The originator retrieves the response data**

The originator retrieves the requested data using the URL(s) returned in the completed status response from the destination. This request and response are routed as described in Steps 1-7, except that the `Prefer: respond-async` parameter is not used.

<p></p>

<div>
   <figure>
    <figcaption class="figure-caption"><strong>Figure 8: Example - Intermediary's Base URL followed by a path indicating the destination</strong></figcaption>
    <img src="uc-async-single-intermediary-int-url-plus-segment.png" style="float:none; width:1100px" title="Figure 8: Example - Intermediary's Base URL followed by a path indicating the destination"/>  
  </figure>
</div>
<br>