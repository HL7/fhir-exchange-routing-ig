### FHIR Resource Content

When participating in exchanges described in the Hybrid / Intermediary FHIR IG, the intermediary server **SHALL** pass FHIR resources returned by the destination server to the originator unchanged.

<p></p>

### FHIR RESTful Capabilities

- Intermediary server **SHALL** support the JSON source format. 
- Intermediary serer  **SHOULD** support the XML source format. 

<p></p>

<h3>Security</h3>

- Intermediary server **SHALL** implement Transport Layer Security (TLS) for all exchanges covered in this IG.
- Security tokens generated and returned by the destination **SHALL** be forwarded by the intermediary server to the originating client. 
- Intermediary server **MAY** implement the [HL7 / UDAP Security for Scalable Registration, Authentication, and Authorization FHIR Implementation Guide](http://hl7.org/fhir/us/udap-security/2021Sep/).
- For general security considerations refer to [FHIR Security and Privacy Considerations](https://www.hl7.org/fhir/secpriv-module.html). 
- For additional security guidance, refer to the [core FHIR Security guidance page](https://www.hl7.org/fhir/security.html). 

<br />

