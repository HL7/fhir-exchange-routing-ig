### FHIR Resource Content

When participating in exchanges described in the Hybrid / Intermediary FHIR IG, the intermediary server **SHALL** pass FHIR resources returned by the destination server to the originator unchanged.

<p></p>

### FHIR RESTful Capabilities

- Intermediary server **SHALL** support the JSON source format. 
- Intermediary serer  **SHOULD** support the XML source format. 

<p></p>

<h3>Security</h3>

- Communication security implemented by the intermediary server **SHALL** conform with the guidelines stated in [FHIR Security](https://www.hl7.org/fhir/security.html) for all exchanges covered in this IG. 
- When using TLS: the inbound gateway intermediary **SHALL** hold the TLS certificate for the destination’s public FHIR service base URL the certificates exchanged by the destination system, and any delegated intermediaries **SHALL** reflect their servers’ private URLs.
- Intermediary server **SHALL** implement Transport Layer Security (TLS) for all exchanges covered in this IG.
- Security tokens generated and returned by the destination **SHALL** be forwarded by the intermediary server to the originating client. 
- Intermediary server **MAY** implement the [HL7 / UDAP Security for Scalable Registration, Authentication, and Authorization FHIR Implementation Guide](http://hl7.org/fhir/us/udap-security/2021Sep/).
- For general security considerations refer to [FHIR Security and Privacy Considerations](https://www.hl7.org/fhir/secpriv-module.html). 

<br />

