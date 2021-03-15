###  Use Cases

This guide supports multiple use cases in which intermediaries take part in the exchange between an initiating system (the "originator") and the desired endpoint system (the "destination").  Circumstances where an originator may wish to use an intermediary to facilitate communication with a destination endpoint include situations where...

- The originator may have the ability to identify the specific endpoint system URL with which it wants to interact, but it does not possess the needed connection specifics or authority to interact with it directly. For example, the originator may wish to delegate the work of registration, maintaining credentials, etc. to an intermediary.

- An originator may be able to uniquely identify the desired endpoint, but not know the URL or connection details. For example, the originator might belong to an intermediary network that assigns a proprietary ID to each of the network’s participants. The network might also encapsulate additional endpoint characteristics into each ID—a payer, for instance, might have one ID for its clinical interactions and a separate ID for claims and related financial interactions. These proprietary identifiers, in combination with a a single intermediary connection, simplify the exchange process for the originator.

- An originator may be in a position to supply identifying information or other criteria identifying a desired destination, but it needs the assistance of an intermediary to locate the appropriate endpoint system—by applying mapping or other logic. For example, an intermediary might offer a service that can locate and route an exchange to an endpoint that fits a set of supplied characteristics. 
  - For example, a lab ordering network might accept a ServiceRequest accompanied by a ZIP code and urgency indicator, and complete the exchange with the closest lab with a current turnaround time matching the urgency.

In each of these situations, the originator relies on an intermediary to do one or both of the following on its behalf:

- determine the endpoint URL of the desired destination

- connect to and complete the exchange with the desired destination’s endpoint system.

Further, while the originator will direct the final destination of its exchange, it is agnostic to intermediaries involved in the message routing. 

- For example, if Provider A needs to request information from Payer B and Payer B uses a payer-agnostic intermediary, Provider A may initiate the interaction with an intermediary and specify a well-known endpoint URL representing payer B, and the intermediary will handle routing of the exchange and provide any value-add services. The intermediary, or intermediaries, will need to have origination and routing information available during the life cycle of the exchange to ensure appropriate delivery.
- A variation of the use case is one in which the requester, Provider A, simply provides the request to their intermediary with an identifier representing the desired destination, Payer B, and not a specific endpoint URL. The intermediary in this scenarios translates the identifier supplied by the originator to routing information so that the transaction can be forwarded--potentially across additional intermediaries--before reaching Payer B's system endpoint.

Of course, there are cases in which there are no intermediaries involved and the routing information is not explicitly needed, but there is no harm with it being available. Our goal is to provide a model that supports a hybrid model of point-to-point interaction as well as intermediary-brokered interaction without the actors in either side needing detailed knowledge of how intermediary routing works.

A reliable routing solution needs to support:
 • Consistent definition and representation of routing information
 • Synchronous and asynchronous models – support for push and pull models in synchronous and asynchronous patterns
 • A hybrid environment - transactions over both dynamic point-to-point and intermediary brokered models

<p></p>

### Example Exchange Flows

#### High-level Example Flow – Originator Specifies a Destination Endpoint URL

Before the exchange, the initiating system consults an endpoint directory and locates the specific endpoint system with which it intends to interact (the “destination”). Then the system initiates an exchange (GET, POST, etc.) with an intermediary while identifying the desired destination endpoint system URL. The intermediary forwards the exchange to the specified endpoint and returns its response to the intiator. 

#### High-level Example Flow – Originator Supplies a Unique Destination ID

The originator system consults its local listing of routing IDs for participants in a partner intermediary network. The system selects the identifier representing an intended destination and initiates an exchange with the intermediary, supplying the proprietary ID at the same time. The intermediary translates the proprietary identifier to the associated endpoint system and completes the exchange, returning the response to the originator.

#### High-level Example Flow – Originator Supplies Criteria to be Used to Determine the Destination Endpoint

The originator system locates the information elements that, together, can be used to determine the intended destination for the exchange. The system initiates an exchange with the intermediary, supplying the identifying information. The intermediary performs the necessary mapping and/or logic to determine a specific destination endpoint system that satisfies the received criteria, completes the exchange with tht endpoint and returns the response to the originator.





 

#### Actor to actor using one or more intermediaries

xxx

<p></p>

#### Actor to actor without intermediary involvement

xxx

<br>

