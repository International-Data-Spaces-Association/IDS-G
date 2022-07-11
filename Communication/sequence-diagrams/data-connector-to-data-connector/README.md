# IDS Connector Communication

---

## Message Flows for Connector to Connector Communication

### Connector Self-description Request

![Data_Interaction_pre_v5_Split_Con_Desc](https://user-images.githubusercontent.com/76526301/116513994-ad57ba80-a8ca-11eb-912c-24ed6b824bcd.png)

To get the self-description of the Provider Connector, an 
[ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) is sent 
without the property `ids:RequestedElement`. This indicates that the Consumer wants to request the 
self-description of the Provider Connector and not of a specific element. The Provider validates 
this request and, if the message processing is successful, returns an 
[ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage) 
containing the Provider Connector's self-description. In case of any errors during the message 
processing, the Provider returns an [ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage)
with a `ids:RejectionReason` indicating the error's cause.

### Usage Contract Negotiation

The message flow of the usage contract negotiation is described in following sequence diagram.

![Contract_Negotiation](https://user-images.githubusercontent.com/76526301/117145269-4434e300-adb3-11eb-900e-2055b771af04.png)

First, the Data Consumer sends an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) 
containing a suggested contract (an instance of `ids:ContractRequest`) to the Data 
Provider. This `ids:ContractRequest` can differ from the Provider's previous contract offer. The 
Data Provider then processes the request and responds with an appropriate response message. 
Processing means to first validate the request and then check if the contract request's content of 
the Data Consumer complies with the contract offer by the Provider. 

The Provider can do a counteroffer in form of an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage)
containing an `ids:ContractOffer`. Then, the Consumer Connector may respond with a counteroffer in 
form of an `ids:ContractMessage` as well. This can go back and forth an arbitrary number of times 
until one of the parties either rejects or accepts the contract, or an error occurs. In case of a 
rejection or an error, an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage) 
that contains the rejection's reason is sent to the other party. If the contract is accepted, an 
[ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) including 
the `ids:ContractAgreement` is returned. 

In this description, it is assumed that the Provider Connector is the one that ultimately gives in 
and sends the [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage). 
If it was the other way around, the sequence after the optional block onwards would also be defined 
the other way around. 

As soon as the Consumer receives the [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage), 
it processes the agreement. While processing, the consumer checks whether the received agreement 
contains the correct assigner and whether it fits the suggested rules that where sent within the 
`ids:ContractRequest` or `ids:ContractOffer`. If the agreement is not valid, an error occurs, or if
the Consumer rejects the agreement, an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage) 
is returned. 

Furthermore, the Consumer has the option to restart the negotiation process sending an 
[ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) with a modified 
`ids:ContractOffer` at any time. As marked as (1) in the sequence diagram, this would cause the 
process to jump to the offer exchange part again. If instead, the agreement is accepted, and the 
processing is successful, the Consumer returns an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) 
including the `ids:ContractAgreement` to the Provider to confirm the validity. If the Provider 
receives this [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage), 
it finalizes the exchange from its side and marks the agreement as confirmed. Optionally, the 
contract agreement is sent to the Clearing House within an `ids:LogMessage`. If an error occurs 
during processing, the Consumer is informed via an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage). 
On success, the communication concludes with the Provider sending an 
[ids:MessageProcessedNotificationMessage](../../Message-Types/README.md#idsmessageprocessednotificationmessage) 
to the Consumer, this is optional. The Consumer concludes with saving the agreement 
and setting the confirmed status to true.

### Requesting Data

![Data_Interaction_pre_v5_Split_Res_Art_Desc](https://user-images.githubusercontent.com/76526301/116514055-c3fe1180-a8ca-11eb-9e0f-bad3671900bc.png)

Based on the self-description of the Provider Connector and a negotiated contract, the Consumer 
Connector is able to request data. For this, the Consumer Connector sends an 
[ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) with the 
URI of the selected resource as the `ids:RequestedElement` to the Provider Connector.
The Provider Connector first validates the message and then returns the metadata of the requested 
resource to the Consumer via an [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage). 
In case of unsuccessful processing, the Provider returns an [ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage) 
instead. 

The Consumer Connector expects an [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage) 
as the response containing the metadata of the requested resource (an instance of `ids:Resource`). 
The Consumer validates the message and then stores the resource. In case of any occurring error, the 
communication is suspended. If not, the Consumer Connector is then able to choose a specific 
artifact that should be consumed. 

In the third step, the Consumer Connector sends an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage) 
including a set `ids:RequestedArtifact` and a reference to the applicable contract agreement as 
the `ids:TransferContract` to the Provider Connector. The Provider validates the message and the 
transfer contract. If an error occurs during processing, the Provider responds with an 
[ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage) to the Consumer. If the 
message is valid, the Provider returns an [ids:ArtifactResponseMessage](../../Message-Types/README.md#idsartifactresponsemessage) 
containing the data. The Consumer Connector expects this [ids:ArtifactResponseMessage](../../Message-Types/README.md#idsartifactresponsemessage) 
and stores or further processes the attached data.

Note, that even though the diagram shows the artifact request is located right after one description
request, there can be more than one request for a resource's self-description before sending an 
artifact request. Also, there can be more than one artifact request executed as long as the artifact 
URI is known, and an applicable contract agreement exists.

### Example for Combining the Flows

One way to combine the flows is to first use the `Connector Self-description Request` sequence to 
request the self-description of the Provider Connector. The Consumer can then browse the offers of 
the Provider. After that, based on the requirements, it defines a contract request and starts the 
`Usage Contract Negotiation` flow. If this is successful, the Consumer is allowed to request 
specific data via the `Requesting Data` Flow.

## Conformance Requirements for the Connector to Connector Communication

| Key | Value |
| --- | --- |
| ID | ta-001 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | The self-description of a specific Connector is needed. |
| Behaviour | Send an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) to this specific Connector. The property `ids:RequestedElement` is not set. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-002 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) without the property `ids:RequestedElement`. |
| Behaviour | Validation of the received message. |
| Terms | Validation in this case means to ensure: <ul><li>The message is not empty.</li><li>The message has the expected syntax.</li><li>The message contains a valid DAT.</li><li>The message version is supported.</li></ul> |

| Key | Value |
| --- | --- |
| ID | ta-003 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) without the property `ids:RequestedElement`. This message is successfully validated. |
| Behaviour | Get the self-description of type `ids:InfrastructureComponent` of the receiving Connector and return it within an [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage) to the requesting Connector. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-004 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) without the property `ids:RequestedElement` set. This message is not successfully validated, or an error occurs during processing. |
| Behaviour | Return an [ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | [ids:RejectionReason](https://github.com/International-Data-Spaces-Association/InformationModel/blob/master/codes/RejectionReason.ttl) |

| Key | Value |
| --- | --- |
| ID | ta-005 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | The metadata of a specific offered resource is needed. The resource URI is known. |
| Behaviour | Be able to send an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) to the offering Connector. The property `ids:RequestedElement` is filled with the specific resource URI. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-006 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) with a resource URI as `ids:RequestedElement`. |
| Behaviour | Validation of the received message. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-007 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) with a resource URI as `ids:RequestedElement`. This message is successfully validated. |
| Behaviour | Get the metadata of the requested resource that is provided by the receiving Connector and send it within an [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage) to the requesting Connector. |
| Terms | The requested resource is the one with the matching resource URI. |

| Key | Value |
| --- | --- |
| ID | ta-008 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage) with a resource URI as `ids:RequestedElement`. This message is not successfully validated, or an error occurs during processing. |
| Behaviour | Return an [ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | [ids:RejectionReason](https://github.com/International-Data-Spaces-Association/InformationModel/blob/master/codes/RejectionReason.ttl) |

| Key | Value |
| --- | --- |
| ID | ta-009 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage) as a response to an [ids:DescriptionRequestMessage](../../Message-Types/README.md#idsdescriptionrequestmessage). |
| Behaviour | Validation of the received message. |
| Terms | Validation in this case means to ensure: <ul><li>The response message is of type [ids:DescriptionResponseMessage](../../Message-Types/README.md#idsdescriptionresponsemessage)</li><li>_see ta-002_</li></ul> |

| Key | Value |
| --- | --- |
| ID | ta-010 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | The data of a specific artifact offered by a specific Connector is needed. The artifact URI is known. An applicable contract agreement has been negotiated. |
| Behaviour | Be able to send an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage) to this specific Connector.  The property `ids:RequestedArtifact` is filled in with the specific artifact URI. The property `ids:TransferContract` is filled with a reference to the applicable contract agreement. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-011 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage) with an artifact URI as `ids:RequestedArtifact`. The property `ids:TransferContract` is filled with a reference to the applicable contract agreement. |
| Behaviour | Validation of the received message. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-012 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage) with an artifact URI as `ids:RequestedArtifact`. The property `ids:TransferContract` is filled with a reference to the applicable contract agreement. Validation of the message is successful. |
| Behaviour | Get the data from the requested artifact and send it within an [ids:ArtifactResponseMessage](../../Message-Types/README.md#idsartifactresponsemessage) to the requesting entity. |
| Terms | The requested artifact is the one with the matching artifact ID. |

| Key | Value |
| --- | --- |
| ID | ta-013 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage) with an artifact URI as `ids:RequestedArtifact`. The property `ids:TransferContract` is filled with a reference to the applicable contract agreement. Validation of the message is not successful, or an error occurs during processing. |
| Behaviour | Return an [ids:RejectionMessage](../../Message-Types/README.md#idsrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | [ids:RejectionReason](https://github.com/International-Data-Spaces-Association/InformationModel/blob/master/codes/RejectionReason.ttl) |

| Key | Value |
| --- | --- |
| ID | ta-014 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ArtifactResponseMessage](../../Message-Types/README.md#idsartifactresponsemessage) containing the requested data as the response to an [ids:ArtifactRequestMessage](../../Message-Types/README.md#idsartifactrequestmessage). |
| Behaviour | Validation of the received message. |
| Terms | Validation in this case means to ensure: <ul><li>That the response message is of type [ids:ArtifactResponseMessage](../../Message-Types/README.md#idsartifactresponsemessage).</li><li>_see ta-002_</li></ul> |

| Key | Value |
| --- | --- |
| ID | ta-015 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | A contract agreement needs to be negotiated with a specific Connector. |
| Behaviour | Be able to send an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) containing an `ids:ContractRequest` to this specific Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-016 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) containing an `ids:ContractRequest`. |
| Behaviour | Validate received message and check if the contract can be agreed upon. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-017 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) containing an `ids:ContractRequest`. The validation of the message and the contract request is successful. |
| Behaviour | Return an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` to the requesting Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-018 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) containing an `ids:ContractRequest`. The validation of the message and/or the contract request is not successful, and/or an error occurs during processing. |
| Behaviour | Return an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-019 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer`. |
| Behaviour | Validation of the received message. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-020 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer`. The validation of the message and the contract offer is successful. |
| Behaviour | Return an `ids:ContractAgreementMessage` containing an `ids:ContractAgreement` to the requesting Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-021 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer`. The validation of the message and/or contract offer is not successful, and/or an error occurs during processing. |
| Behaviour | Return an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-022 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer`. The validation is successful. The contract should not be rejected, but a counter offer should be sent. |
| Behaviour | Return an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer` to the requesting Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-023 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector that has sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` as a response. |
| Behaviour | Validation of the received message. |
| Terms | Validation in this case means to ensure: <ul><li>That the response message is of type [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage).</li><li>_see ta-002_</li></ul> |

| Key | Value |
| --- | --- |
| ID | ta-024 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector that has sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` as a response. |
| Behaviour | Check the contract agreement. |
| Terms | Checking the contract agreement means to: <ul><li>Check if the assigner is the expected one.</li><li>Check if the rules of the contract agreement match the offered ones.</li></ul> |

| Key | Value |
| --- | --- |
| ID | ta-025 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector that has sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` as a response. The validation is successful, and the checks result in accepting the contract agreement. |
| Behaviour | Return an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing the `ids:ContractAgreement` to the other Connector to confirm the contract agreement. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-026 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector that has sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` as a response. The validation is not successful, an error occurs, or the contract agreement should be rejected. |
| Behaviour | Return an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage) containing the appropriate `ids:RejectionReason`. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-027 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector that has sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement` as a response. The validation is successful. Some modifications to the contract are pending. |
| Behaviour | Return an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) containing an `ids:ContractOffer` to the other Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-028 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement`. The Connector has not sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) before. |
| Behaviour | Validation of the received message. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-029 |
| Product | Connector |
| Strictness level | MAY |
| Prerequisites | Connector receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement`. The Connector has not sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) before. The validation of the message is successful. |
| Behaviour | Send an [ids:MessageProcessedNotificationMessage](../../Message-Types/README.md#idsmessageprocessednotificationmessage) with the filled `ids:CorrelationMessage` to the Consumer Connector. |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-030 |
| Product | Connector |
| Strictness level | MUST |
| Prerequisites | Connector receives an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing an `ids:ContractAgreement`. The Connector has not sent an [ids:ContractRequestMessage](../../Message-Types/README.md#idscontractrequestmessage) or an [ids:ContractOfferMessage](../../Message-Types/README.md#idscontractoffermessage) before. The validation of the message is not successful, or an error occurs during processing. |
| Behaviour | Return an [ids:ContractRejectionMessage](../../Message-Types/README.md#idscontractrejectionmessage). |
| Terms | - |

| Key | Value |
| --- | --- |
| ID | ta-031 |
| Product | Connector |
| Strictness level | MAY |
| Prerequisites | Connector that has sent an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing the `ids:ContractAgreement` to the other party to confirm the contact agreement receives an [ids:MessageProcessedNotificationMessage](../../Message-Types/README.md#idsmessageprocessednotificationmessage) as a response. |
| Behaviour | Validation of the received message. |
| Terms | _see ta-002_ |

| Key | Value |
| --- | --- |
| ID | ta-32 |
| Product | Connector |
| Strictness level | MAY |
| Prerequisites | Connector that has sent an [ids:ContractAgreementMessage](../../Message-Types/README.md#idscontractagreementmessage) containing the `ids:ContractAgreement` to the other party to confirm the contract agreement receives an [ids:MessageProcessedNotificationMessage](../../Message-Types/README.md#idsmessageprocessednotificationmessage) as a response. The validation is successful. |
| Behaviour | Communication concludes. |
| Terms | - |
