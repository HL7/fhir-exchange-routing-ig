### Underlying Technologies

This solution uses custom HTTP headers as described in the [FHIR Exchange Module](http://build.fhir.org/http.html#custom) with proposed new X-Originator and X-Destination headers.

<p></p>

###  Use Cases

In this model, the interaction originator will know the final destination but is agnostic to intermediaries involved in the message routing. For example, if provider A needs to request information from payer B and payer B uses a payer agnostic intermediary, provider A will initiate the interaction with a known endpoint representing payer B, which in this case is an intermediary, and the intermediary will handle routing of the transaction and provide any value-add services. The intermediary, or intermediaries, will need to have origination and routing information available during the life cycle of the transaction to ensure appropriate delivery.

 A variation of the use case is one in which the requester, provider A, simply provides the request to their intermediary who then provides routing information so that the transaction can move across additional intermediaries before getting to payer B.

 Of course, there are cases in which there are no intermediaries involved and the routing information is not explicitly needed, but there is no harm with it being available.

 Our goal is to provide a model that supports a hybrid model of point-to-point interaction as well as intermediary brokered interaction without the actors in either side needing detailed knowledge of how intermediary routing works.

A reliable routing solution needs to support:
 • Consistent definition and representation of routing information
 • Synchronous and asynchronous models – support for push and pull models in synchronous and asynchronous patterns
 • A hybrid environment - transactions over both dynamic point-to-point and intermediary brokered models

<p></p>

#### Actor to actor using one or more intermediaries

xxx

<p></p>

#### Actor to actor without intermediary involvement

xxx

<br>

