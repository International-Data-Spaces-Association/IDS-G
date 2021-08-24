# Role of an IDS Meta Data Broker
The following statements can be counted as informal requirements and specify the role of the IDS Meta Data Broker within the IDS. As these requirements are just conceptional, 
they are not subject of any certification process.
|  |  |
| --- | --- |
| B001 (B1) | An IDS Meta Data Broker is an optional component of any International Data Space. |
| B002 (B3) | An IDS Meta Data Broker provides an index service, which is a system for the publication of metadata sent by Connectors. |
| B003 | An IDS Meta Data Broker should allow the link-based discovery of data sources. |
| B004 (B4) | The actual transfer of data, except Meta Data, between a provider and consumer Connector does not involve an IDS Meta Data Broker at any time. |
| B005 (B6) | Multiple brokers may exist in International Data Spaces. |
| B006 (B7) | There may be different Meta Data Broker implementations from different providers in International Data Spaces. |
| B007 (B10) | A Connector operator may independently define which (meta-)data partitions of its Connector is sent to one or multiple IDS Meta Data Brokers. |
| B008 | An International Data Spaces Participant may use a generally known or otherwise announced server to host metadata for IDS entities. The recommended procedure is to use an IDS Meta Data Broker for this. |
| B 009 (B13) | The communication between a Connector and An IDS Meta Data Broker is message oriented. There are two categories of broker messages:<p>  - Publishing Messages (delivery of Meta Data to the index services)<p>   - Query Messages (query of Meta Data from the index service) |
| B 010 | Persistent storage can be achieved using triple stores or by any other suitable storage back ends. |
| B 011 (B24) | The persistence of an index service is not predefined. Here is a list of options:<p>  - Filesystem<p>  - NoSQL<p>  - RDBMS<p>  - LDAP/ActiveDirectory<p>  - RDF |
| B 013 | The recommended RDF serialization for any metadata is JSON-LD. |
| B 014 | An IDS Meta Data Broker should accept registration attempts from a valid IDS Connector. |
| B 015 (B28) | An IDS Meta Data Broker may parallelize the processing of Broker Messages, as long as this has no impact on query results. |
 
 
