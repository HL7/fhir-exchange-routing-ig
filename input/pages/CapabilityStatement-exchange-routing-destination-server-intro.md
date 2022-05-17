### FHIR Resource Content

When participating in exchanges described in the Hybrid / Intermediary FHIR IG, the destination server **SHALL** populate references to itself within FHIR resources it returns (e.g., in the Bundle.entry.fullUrl element) and in HTTP header parameters (e.g., Content-Location within the FHIR Asynchronous Pattern) with its public FHIR service base URL. 

<p></p>

### FHIR RESTful Capabilities

- Destination server **SHALL** support the JSON source format. 
- Destination server **SHOULD** support the XML source format. 

<p></p>

<h3>Security</h3>

- Communication security implemented by the server SHALL conform with the guidelines stated in [FHIR Security](https://www.hl7.org/fhir/security.html) for all exchanges covered in this IG.
- Destination server **MAY** implement the [HL7 / UDAP Security for Scalable Registration, Authentication, and Authorization FHIR Implementation Guide](http://hl7.org/fhir/us/udap-security/2021Sep/).
- For general security considerations refer to [FHIR Security and Privacy Considerations](https://www.hl7.org/fhir/secpriv-module.html). 

<br />

