# Message Types

## Message subtypes relevant for [Connector to Connector communication](../sequence-diagrams/data-connector-to-data-connector#message-flows-for-connector-to-connector-communication)

There are three Subclasses of the abstract [ids:Message](../Message-Structure/README.md#idsmessage-properties) class. Namely the [ids:RequestMessage](#RequestMessage), [ids:ResponseMessage](#ResponseMessage) and [ids:NotificationMessage](#NotificationMessage). Each subclass itself has subclasses that fulfill a specific purpose in the communication process.

For communication in the IDS, usually the more specific subclasses of the three mentioned ones are used. The message classes relevant for the Connector to Connector communication are listed below. The entire Collection of Messages available in the Information Model can be found [here](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl).

<a name="RequestMessage"></a>
### ids:RequestMessage

Client-generated message initiating a communication, motivated by a certain reason and with an answer expected.

#### ids:DescriptionRequestMessage
Message requesting metadata. If no URI is supplied via the ids:requestedElement field, this messages is treated like a self-description request and the recipient should return its self-description via an ids:DescriptionResponseMessage. However, if a URI is supplied, the Connector should either return metadata about the requested element via an ids:DescriptionResponseMessage, or send an ids:RejectionMessage, e.g., because the element was not found

*Parent:* [ids:RequestMessage](#RequestMessage)

*Additional Message Properties:*

|  Properties /Fields | Description |
|---|---|
| requestedElement  | The element whose metadata is requested.  |

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#RequestMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:ArtifactRequestMessage
Message asking for retrieving the specified Artifact inside an ArtifactResponse message.

*Parent:* [ids:RequestMessage](#RequestMessage)

*Additional Message Properties:*

|  Properties /Fields | Description |
|---|---|
| requestedArtifact*  | References an ids:Artifact.  |

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ArtifactRequestMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:ContractRequestMessage
Message containing a suggested content contract (as offered by the data consumer to the data provider)(which is an instance of ids:ContractRequest).

*Parent:* [ids:RequestMessage](#RequestMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ContractRequestMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

<a name="ResponseMessage"></a>
### ids:ResponseMessage

Response messages hold information about the reaction of a recipient to a formerly sent command or event. They must be correlated to this message.

#### ids:DescriptionResponseMessage
Message containing the metadata, which a Connector previously requested via the ids:DescriptionRequestMessage.

*Parent:* [ids:ResponseMessage](#ResponseMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#DescriptionResponseMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:ArtifactResponseMessage
Message that follows up a ArtifactRequestMessage and contains the Artifact's data.

*Parent:* [ids:ResponseMessage](#ResponseMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ArtifactResponseMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:ContractAgreementMessage
Message containing a contract, as an instance of ids:ContractAgreement, with resource access modalities on which two parties have agreed.

*Parent:* [ids:ResponseMessage](#ResponseMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ContractAgreementMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:RejectionMessage
Rejection messages are specialized response messages that notify the sender of a message that processing of this message has failed.

*Parent:* [ids:ResponseMessage](#ResponseMessage)

*Additional Message Properties:*

|  Properties /Fields | Description |
|---|---|
| RejectionReason  | Code describing the Rejection Reason, e.g., idsc:NOT_FOUND. (see ids:RejectionReason class)  |

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#RejectionMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:ContractRejectionMessage
Message indicating rejection of a contract.

*Parent:* [ids:ResponseMessage](#ResponseMessage)

*Additional Message Properties:*

|  Properties /Fields | Description |
|---|---|
| contractRejectionReason  | Human-readable text describing the reason for contract rejection  |

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ContractRejectionMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

<a name="NotificationMessage"></a>
### ids:NotificationMessage

Event messages are informative and no response is expected by the sender.

#### ids:ContractOfferMessage
Message containing a offered content contract in the associated payload (which is an instance of ContractOffer).

*Parent:* [ids:NotificationMessage](#NotificationMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#ContractOfferMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |

#### ids:MessageProcessedNotificationMessage
Notification that a message has been successfully processed (i.e., not ignored or rejected).

*Parent:* [ids:NotificationMessage](#NotificationMessage)

*Additional Message Properties:*
None

*Implementations:*

|  List of Implementations |
|---|
| [IDS-REST](../protocols/ids-rest/README.md#MessageProcessedNotificationMessage) |
| [idscp2](../protocols/idscp2/ApplicationLayer/README.md) |
| [multipart](../protocols/multipart#2-structure-of-a-multipart-message) |


## Metadata Broker Interaction Messages

See [Connector to Metadata Broker Communication](../sequence-diagrams/data-connector-to-metadata-broker/README.md)
for information how to work with the Metadata Broker.


## Message subtypes for other interaction
Will be added in the future (TODO).
