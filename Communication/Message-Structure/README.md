# Message Structure/Format

<a name="Message"></a>
## ids:Message properties

Core [ids:Message class](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/model/communication/Message.ttl) with its properties, which are applicable to all messages.

|  Properties /Fields | Description |
|---|---|
| modelVersion*  | Information Model version, against which the Message should be interpreted.  |
| issued*  | Date of issuing the message. Value: Datetime in XML datetime pattern:  YYYY-MM-DDThh:mm:ss  |
| correlationMessage  | Correlated message, e.g., response to a previous message. Value: URI of the correlatedMessage  |
| issuerConnector*  | Origin Connector of the message. Value: URI of origin Connector  |
| recipientConnector†  | Target Connector. Value: URI of target Connector  |
| senderAgent*  | Agent, which initiated the message. Value: URI of an instance of ids:Agent.  |
| recipientAgent†  | Agent, for which the message is intended. Value: URI of an instance of ids:Agent  |
| securityToken*  | Token representing a claim, that the sender supports a certain security profile. Value: Instance of ids:DynamicAttributeToken.  |
| authorizationToken  | An authorization token. The token can be issued from the Connector of the Data Provider (A) to the Connector of the Data Consumer (B). Can be used to avoid full authentication via DAPS, if Connector B wants to access the data of Connector A. Value: Instance of ids:Token  |
| transferContract  | Contract which is (or will be) the legal basis of the data transfer. Value: Instance of class ids:Contract.  |
| contentVersion  | Value describing the version of the content. Value: Version number of the content.  |

\* -> Mandatory Property

† -> Can have multiple values at the same time


## Self-Description
The Connector self-description is a serialized instantiation of the Connector class (ids:Connector) as defined by the IDS Information Model. The Connector self-description provides information about the Connector, as well as the catalog of Resources that are provided or requested by the Connector. A full explanation of what Resources are and how they are defined in the IDS is beyond the scope of this document. The IDS-RAM contains detailed information about this. In summary, the provided catalog of Resources, which are part of the Connector self-description, contain serialized instances of the Information Model class ids:Resource. Resources are units of information that embody a Digital Content (superclass ids:DigitalContent). Instances of ids:Resource contain metadata about the actual content and may also contain references to interfaces and representations. Interfaces (class ids:Interface) define operations that are supported by a Resource. Representations (class ids:Representation) further describe a Resource with, i.e., a particular media type or a specific data schema the data is based on. Note that Resources can have several Representations. The materialized Representations are called Artifacts (class ids:Artifact). Artifacts are served by Endpoints (class ids:Endpoint).



A Connector has following properties, which are used for it's self-description.


|Property |	Description |
| ------------- |:-------------|
|title†	|Connector title|
|description†	|Connector description|
|maintainer*	|Maintainer of the Connector. Value is an instance of class ids:Participant with an associated ID|
|curator*	|Participant responsible for the correctness of the offered content.  Value is an instance of class ids:Participant with an associated ID|
|outboundModelVersion*	|Information Model version being produced by the Connector|
|inboundModelVersion*†	|Information Model version that the Connector is capable of reading/processing|
|host†	|Network host of the Connector capable of serving / consuming Digital Contents and services|
|defaultHost	|Default host that should be used for basic infrastructure interactions, e.g., providing the self description|
|authInfo	|Information of the authentication service used by the Connector (e.g., to access a Connector’s data)|
|securityProfile |Set of security guarantees claimed by a Connector. Value contains one of the default security profile codes, such as idsc:BASE_CONNECTOR_SECURITY_PROFILE. All profiles can be found here: https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/codes/SecurityGuarantee.ttl Valid attributes are: idsc:BASE_SECURITY_PROFILE idsc:TRUST_SECURITY_PROFILE idsc:TRUST_PLUS_SECURITY_PROFILE|
|extendedGuarantee	|Reference to additional security guarantees that, if used in combination with a security profile instance, overrides the respective guarantee of the given predefined instance. Value is a pre-defined code for the claimed guarante, e.g., ids:USAGE_CONTROL_POLICY_ENFORCEMENT|
|transportCertSha256†	|SHA256 fingerprints of currently valid transport certificates|
|componentCertification	|Certification issued for the given Connector. Value is an instance of class ids:ComponentCertification|
|publicKey	|Public Key that has been created for the Connector|
|catalog	|Catalog of published / requested Resources. Value is a list of requested / offered ids:Resource instances|
|physicalLocation |Physical location of the Connector. Value is an instance of any of the subclasses of ids:Location. ids:GeoPoint, ids:GeoFeature, or ids:BoundingPolygon. |
|lifeCicleActivity† |Activity that occurs during the lifecycle of the Connector. Value is in instance of ids:Activity |

\*  -> mandatory fields,

† -> fields that can have multiple values at the same time


The GitHub repository of the IDS Information Model contains a minimal working example of a Connector’s self-description ([RDF/TTL](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/examples/TRUSTED_CONNECTOR.ttl) / [JSON-LD](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/examples/TRUSTED_CONNECTOR.jsonld)). There is also an example of ids:Resource instance ([RDF/TTL](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/examples/TEXT_RESOURCE.ttl), [JSON_LD](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/examples/TEXT_RESOURCE.jsonld)). Both examples are available in RDF/Turtle and JSON-LD serialization.
