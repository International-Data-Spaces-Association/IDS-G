# General Overview<p>
The International Data Spaces Reference Architecture Model (version 3.0) describes several components:<p>
  - International Data Spaces Connector<p>
  - International Data Spaces App Store<p>
  - International Data Spaces Meta Data Broker<p>
  - International Data Spaces Clearing House<p>
  - International Data Spaces Identity Provider<p>

The IDS Meta Data Broker is a service for publishing and searching metadata of Connectors and resources between International Data Spaces Participants.
In order to ensure the necessary interoperability and general interactions, an IDS Meta Data Broker (like the App Store) is also defined as a specialized IDS Connector.
The communication between an IDS Connector and an IDS Meta Data Broker is based on the same principles as any other Connector-Connector communication within
the International Data Spaces. Still, an IDS Meta Data Broker provides a collection of additional functionalities:<p>
  - Indexing services in order to effectively and efficiently respond to queries and present known Connectors and other resources<p>
  - Interfaces for Users or IDS-Messages to ensure access to the stored information.<p>

This document serves as a specification defining what an IDS Meta Data Broker is, which functionalities it must support and which services other IDS components can expect.
The described content is aligned with the explanations of the IDS-RAM 3.0 and complements the[“The IDS Communication Guide”](https://industrialdataspace.jiveon.com/docs/DOC-2524).<p>
A software component is allowed to describe it self as an IDS Connector only after an IDS compliant Certification Body verified that the necessary capabilities are in place and
operate in IDS conformant manners. The list of certification criteria ([Certification Criteria -Components v2.1.2](https://industrialdataspace.jiveon.com/docs/DOC-3823)) contains the respective requirements. 
The same procedure is valid for an IDS Meta Data Broker, with additional requirements for broker-functionalities as documented in this list.
After a successful certification of the relevant Criteria of an IDS Connector and as an IDS Meta Data Broker, a software component can include the following RDF statement
into its Self-Description:<p>
  
`<Connector/Broker URI> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <https://w3id.org/idsa/core/Broker> `.<p>
However, no other entity is obliged to believe this claim without further proof. The IDS way for a trustworthy information provision is the Dynamic Attribute Token ([DAT](https://github.com/International-Data-Spaces-Association/IDS-G/tree/master/glossary#dynamic-attribute-token-dat)),
provided by IDS Dynamic Attributes Provisioning Service ([DAPS](https://github.com/International-Data-Spaces-Association/IDS-G/tree/master/glossary#dynamic-attribute-provisioning-service-daps)). A certified IDS Meta Data Broker can ask for a DAT at its responsible DAPS. The necessary interactions are
specified in [The IDS Communication Guide](https://industrialdataspace.jiveon.com/docs/DOC-2524) and The [IDS Handshake](https://industrialdataspace.jiveon.com/docs/DOC-1817). Only if a correctly signed DAT from a trustworthy DAPS includes the above claim, other IDS entities should
accept the claim of a potential IDS Meta Data Broker.<p>
In accordance to the thereby directly included guaranteed features, an IDS Meta Data Broker may provide significantly more services. Several options to include broker features in an International Data Space are possible:<p>
  - only mandatory IDS Meta Data Broker features<p>
  - mandatory and profile features:<p>
    - any set of additional features from the “UsageControl Enabled” and “Advanced Information” profiles<p>
    - all additional features from the “Usage Control Enabled” or/and  “Advanced Information” profiles. <p>
  - general features supporting publication, indexing and discovery which are not mentioned in this specification (not applicable as an IDS Meta Data Broker), or<p>
  - any combination of the categories above.
