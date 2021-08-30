# Advanced Information Profile
| | |
| --- | --- |
| B 071 | An IDS Meta Data Broker should indicate why it rejects a request from a valid IDS Connector. |
| B 072 | An IDS Meta Data Broker should keep track of removed components. |
| B 073 | An IDS Meta Data Broker should constantly verify the existence of its indexed IDS components and synchronize its indexed meta-data with the components self-descriptions. |
| B 074 | An IDS Meta Data Broker supports the Search for data sources offered by Connectors. Possible search criteria are key words, taxonomies, multi-criteria facets. |
| B 075 (B18) | An IDS Meta Data Broker should provide version indicators, outlining that metadata has been updated. If implemented, one of the following solutions has to be applied:<p> a) the unique metadata key (URI) contains a version number (not recommended)<p> b) the metadata contains a version number, which is incremented by the Connector or an IDS Meta Data Broker |
| B 076 | The recommended scheme for IDS Identifiers is HTTP. IDS Participants should use dereferenceable URIs according to the Linked Data Principles (Linked Data -Design Issues1) for increased interoperability. |
| B 077 | In case any of the options in B30 does not hold, an IDS Meta Data Broker may update its indexed information but is not expected to. |
| B 078 | An IDS Meta Data Broker may support more than one IDS Information Model versions for incoming messages. |
| B 079 | An IDS Meta Data Broker may support external vocabularies for component or data resource descriptions. In this case, an IDS Meta Data Broker should provide links to further information about these vocabularies (cf. Vocabulary Hub). |
| B 080 | An IDS Meta Data Broker should have:<p> − an HTML-based UI at /browse and<p> − an HTTPS endpoint for IDS Multipart messages at /infrastructure |
| B 081 (B26) | An IDS Meta Data Broker may provide a history feature for changed or deleted metadata. If available, a QueryMessage may contain a timestamp or a version number. Also, if An IDS Meta Data Broker does not provide a history, these arguments can be ignored and marked as such in the response. |
