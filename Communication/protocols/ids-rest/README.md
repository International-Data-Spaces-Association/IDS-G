# IDS REST

Status: [on its way to `IDS-G`](https://github.com/International-Data-Spaces-Association/IDS-G/)


## Scope of this Document

Linked Data Platform (LDP) is the W3C recommended architecture to allow read/write interactions on distributed data using Semantic Web technologies. It combines the well-established identification methods of Web resources (HTTP URIs/URLs) and
 vocabularies from RDF with the access concepts from Linked Data (data retrieval using HTTP GET, Content-Negotiation).
 This document presents a binding of IDS interactions to the LDP Recommendation in order to merge the
 advantages of both approaches: LDP allows the dynamic interaction with unknown entities
 while the IDS defines patterns to ensure a sovereign and controlled data ecosystem.

The merged approach in this specification presents a union of both LDP and IDS concepts, called IDS-REST.
As such, the resulting binding limits the interaction opportunities of the original LDP
Recommendation regarding the requirements of the IDS specifications and vice versa. The benefits of the
combined approach lie in (a) the simplification of IDS interactions using the LDP best practices, while (b) the trustworthiness of the IDS ecosystem is introduced into LDP-conform applications.


## Terminology and Roles

| Term  | Explanation | Example |
| :--- | --- | :--- |
| LDP Client	| User Agent behaving according to the LDP Recommendation	| |
| HTTP Client / Client| The network socket sending HTTP requests and receiving response messages. Usually tightly connected with the User-Agent. | Web Browser, cURL |
| HTTP Server / Server | The network socket receiving HTTP requests and sending response messages. Usually tightly connected with the Origin Server.	| One Origin Server could have several server sockets, for instance, one for HTTP, one for MQTT, and one for WebSockets. As all supply the same resources, none of them can be the origin server. The resources are maintained by the Origin Server but their interactions happen through the respective protocol-dependent servers. |
| User Agent | Software artifact acting on behalf of a user or another component. | Web Browser, HTTP client socket |
| Connector Operator / Operator | Legal entity controlling and maintaining a specific IDS Connector.	| |
| LDP Server | HTTP Server acting as the origin server for an LDP Client. | Marmotta Reference Server, SOLID Server, GAIAboX |
| resource | Generic data entity, identifiable through an URI | |
| IDS Connector | Gateway providing and requesting data according to IDS specifications. | IDS Metadata Broker, Trusted Connector |
| Origin server | Entity containing the original data ("the truth") as defined by REST | IDS Connector, HTTP Server |
| LDP Resource | A Web-based resource which is conform to the LDP specification. | |
| LDP Container | A Web-based resource which conforms to the LDP specification and can host further resources as children. | |
| IDS Resource	| A resource which is also an instance of the class ids:Resource.  | |
| ids: | Prefix for the IDS namespace: <https://w3id.org/idsa/core/> | [ids:Connector](https://w3id.org/idsa/core/Connector) |
| ldp: | Prefix for the LDP namespace: <http://www.w3.org/ns/ldp#> | [ldp:BasicContainer](http://www.w3.org/ns/ldp#BasicContainer) |

##### *Table 1: Relevant terms and their roles.*


## Resources of Interest

The LDP defines a special type of resources that are exposed by servers: Containers. Containers are defined as Resources that can contain further Resources or Containers. This creates a
 tree-like structure which can be explored following hypermedia
 links. Therefore, user agents are able to discover the data content of a LDP server on-the-fly and without any external information.

On the other side, an IDS Connector and the IDS Resources it provides must follow the
 scheme of the IDS Information Model. The rich semantics and the thereby imposed implications of the different IDS classes are crucial for any IDS conform interaction.
 The thereby imposed complex operation semantics however is explicitly not regarded by the LDP Recommendation. That implies the following consequences for a LDP binding in the IDS:

- IDS Connectors are the single Origin Server of an IDS Ressource.
- The root container must be an instance of the class ids:Connector or its sub classes.
- Offered data is collected in an instance of ids:Catalog, which at the same time appears as a ldp:Container.
- ids:/ldp:Resources inside an ids:Catalog can have further child resources.
- Allowed operations, for instance the creation or deletion of a resource, depends on the location of the target resource in the data tree. Certain resources (instance of ids:Connector at the root level and its catalog) can be deleted or updated only by the Connector Operator.


## Motivation

At the time of writing this document, the IDS interactions follow a message-driven pattern.
The IDS attributes are partitioned and encoded in defined message types according to the Multipart format. With this specification all IDS interactions are supported. Therefore, the obvious
 questions are: Why is it necessary to define yet another interaction pattern? This contradiction is crucial to answer as every additional layer of specifications,
 requirements and standardization increases the onboarding efforts of developers and thereby
 hinders the adoption of the IDS in general.

It has been shown that the proposed Multipart and message-driven communication is significantly
 complex to implement for a huge proportion of developers. The success of the IDS however is directly dependent on a vivid and motivated developer
 community. Therefore, the IDS must provide the patterns that developers are familiar with
 instead of insisting on rarely used technologies. In addition, the tool support for Multipart
 messages is rather limited, while there is a massive trend towards REST APIs in the Web but also in any kind of distributed architectures.

First and foremost, REST APIs restrict the number of assumptions a consuming
 application needs to make about a remote endpoint. The protocol, possible states,
 state transformations, exchange sequences, error behavior, and so on need to be
 understood by all parties. REST is already a well-known and commonly accepted
 paradigm providing recommendations (not necessarily answers!) to a lot of these
 topics. Reusing them simplifies the life of the developers, increases the
 maintainability, and last but not least presents the condensed lessons learned
 of a huge community.

Apart from these design advantages, the tool support for REST APIs is significantly
 better - especially in terms of open source projects - than for any other communication pattern.

Following this argumentation, the IDS community agreed that a REST binding is
 required. Whether it - at some point - might even replace the currently used
 interaction patterns is not in the scope of this document. The hereby presented
 binding is intended as an coequal binding with the same relevance and commitment
 than the other bindings.


## Operations and Interactions

The instance of an IDS Messages, as defined in the IDS Information Model, encodes i.a. the intended command, content, target, expected response
 . For example, an ArtifactRequestMessage
 contains its operational semantics ('return an IDS Artifact'), the target
 resource, expected response (ArtifactResponseMessage), data format (Multipart) etc.
 This implies that both the sender and the receiver of the message need to share all
 these assumptions and deeply understand the IDS requirements and side-effects.
 Obviously, this is especially hard for people joining the IDS community but also
 for experienced IDS developers.

The REST paradigm on the other hand separates the command semantics from the content
 and deeply integrates the communication pattern with HTTP methods. This reduces
 the necessary assumptions but also restricts the expressiveness of the interaction.
 In addition, REST interactions are stateless. In terms of the IDS communication
 and data exchange, this means that neither the server nor the client is expected
 to remember previous interactions or how a current request fits into an ongoing
 sequence of requests. As a consequence, all requests must be complete so that they
 provide all necessary information for their correct execution. Hence, User-Agents interact with
 IDS Connectors only through the URI pointing at the target resource, the eight
 HTTP methods (GET, PUT, POST, DELETE, HEAD, OPTIONS, PATCH, TRACE), the HTTP Headers
 and the optional request body. Therefore, an explicit mapping of the defined message
 types to the resource- and state-based view of REST is required. The following
 sections outline this mapping in detail.


 ## IDS Message Mapping

 | Notification Message Name  | LDP-Mapping |  Description |
 | :--- | --- | --- |
 |	<a id="RequestMessage">RequestMessage</a>	|	GET {ids:Resource URL}/ <br/><br/> Identification URI *must* be identical with the URL the resource is hosted at.	|	Client-generated message initiating a communication, motivated by a certain reason and with an answer expected. May be used for messages, which are not covered by the core IDS messages.	|
 |		|		| 	|
 |	<a id="ResultMessage"></a>ResultMessage	|	HTTP Response	|	Result messages are intended to annotate the results of a query command.	|
 |		|		| 	|
 |	<a id="ResponseMessage"></a>ResponseMessage	|	HTTP Response	|	Response messages hold information about the reaction of a recipient to a formerly sent command or event. They must be correlated to this message . |
 |		|		|	May be used for messages, which are not covered by the core IDS messages.	|
 |		|		|  |
 |	<a id="ResourceUpdateMessage"></a>ResourceUpdateMessage	|	PUT {Resource URL} <br/><br/>	Resource instance as serialized RDF in the request body	|	Message indicating an update of a specific resource	|
 |		|	POST {Connector Inbox URL} <br/><br/> message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="ResourceUnavailableMessage"></a>ResourceUnavailableMessage	|	DELETE {Resource URL}	|	Message indicating that a specific resource is unavailable 	|
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="ResourceAvailableMessage"></a>ResourceAvailableMessage	|	POST {Connector Offers URL at the Broker Catalog}<br/><br/>Resource instance as serialized RDF in the request body	|	Message indicating that a specific resource is available.	|
 |		|	PUT {URL of the connector at the server}<br/><br/>Resource instance as serialized RDF in the request body	|  |
 |		|	POST {Connector Inbox URL}<br/><br/>Message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="RequestInProcessMessage"></a>RequestInProcessMessage	|	HTTP Response with status code 1xx	|	Notification that a request has been accepted and is being processed.	|
 |		|		| 	|
 |	<a id="RejectionMessage"></a>RejectionMessage	|	HTTP Response with status code 4xx or 5xx	|	Rejection messages are specialized response messages that notify the sender of a message that processing of this message has failed.	|
 |		|		| 	|
 |	<a id="QueryMessage"></a>QueryMessage	|	GET {Connector Root or Query Endpoint}<br/><br/>Query in Query Parameters	|	Query message intended to be consumed by specific components.	|
 |		|	POST {Connector Root or Query Endpoint}<br/><br/>Query in the request body.	|		|
 |		|		| 	|
 |	<a id="ParticipantUpdateMessage"></a>ParticipantUpdateMessage	|	PUT {Participant URL}	<br/><br/> Participant instance as serialized RDF in the request body	|	that is NOT a direct subject of the certification process. |
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="ParticipantUnavailableMessage"></a>ParticipantUnavailableMessage	|	DELETE {Participant URL}	|	Event notifying the recipient(s) that a participant will be unavailable and never be available again	|
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="ParticipantResponseMessage"></a>ParticipantResponseMessage	|	HTTP Response	|	Message that follows up a ParticipantRequestMessage and contains the Participant's information in the payload section.	|
 |		|		| 	|
 |	<a id="ParticipantRequestMessage"></a>ParticipantRequestMessage	|	GET {Participant URL}<br/><br/>Header: "Accept: application/ld+json" or any other well-known RDF MIME type	|	Message asking for retrieving the specified Participants information as the payload of an ParticipantResponse message	|
 |		|		| 	|
 |	<a id="ParticipantCertificateUnavailableMessage"></a>ParticipantCertificateUnavailableMessage	|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|	Indicates that a (previously certified) Participant is no more certified. This could happen, for instance, if the Certification Body revokes a granted certificate or if the certificate has just expired.	|
 |		|		| 	|
 |	<a id="ParticipantCertificateGrantedMessage"></a>ParticipantCertificateGrantedMessage	|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|	Whenever a Participant has been successfully certified by the Certification Body, the Identity Provider can use this message to notify Infrastructure Components	|
 |		|		| 	|
 |	<a id="ParticipantAvailableMessage"></a>ParticipantAvailableMessage	|	POST {Connector Offers URL at the ParIS Catalog}<br/><br/>Participant instance as serialized RDF in the request body	|	Event notifying the recipient(s) about the availability of a new participant.	|
 |		|	PUT {URL of the Participant at the server}<br/><br/>Participant instance as serialized RDF in the request body	|		|
 |		|	POST {Connector Inbox URL}<br/><br/>Message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="OperationResultMessage"></a>OperationResultMessage	|	HTTP Response	|	Message indicating that the result of a former InvokeOperation message is available. May transfer the result data in its associated payload section	|
 |		|		| 	|
 |	<a id="NotificationMessage"></a>NotificationMessage	|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|	Notification messages are informative and no response is expected by the sender. May be used for scenarios, which are not covered by the core IDS messages.	|

 | Request Message Name  | LDP-Mapping |  Description |
 | :--- | --- | --- |
 |	<a id="MessageProcessedNotificationMessage"></a>MessageProcessedNotificationMessage	|	HTTP Response with status code 2xx	|	Notification that a message has been successfully processed (i.e., not ignored or rejected). 	|
 |		|		| 	|
 |	<a id="LogMessage"></a>LogMessage	|	POST {Connector Inbox URL} <br/><br/> message content as serialized RDF in the request body	|	Log Message which can be used to transfer logs e.g. to the clearing house.	|
 |		|		| 	|
 |	<a id="InvokeOperationMessage"></a>InvokeOperationMessage	|	POST {ids:Interactive Endpoint URL}[?{query parameter}]	|	Message requesting the recipient to invoke a specific operation.	|
 |		|		| 	|
 |	<a id="DescriptionResponseMessage"></a>DescriptionResponseMessage	|	HTTP Response	|	Message containing the metadata, which a Connector previously requested via the ids:DescriptionRequestMessage, in its payload.	|
 |		|		| 	|
 |	<a id="DescriptionRequestMessage"></a>DescriptionRequestMessage	|	GET {resource URL} <br/><br/> Header: "Accept: application/ld+json" or any other well-known RDF MIME type	|	Message requesting metadata. If no URI is supplied via the ids:requestedElement field, this messages is treated like a self-description request and the recipient should return its self-description via an ids:DescriptionResponseMessage. However, if a URI is supplied, the Connector should either return metadata about the requested element via an ids:DescriptionResponseMessage, or send an ids:RejectionMessage, e.g. because the element was not found 	|
 |		|		| 	|
 |	<a id="ContractSupplementMessage"></a>ContractSupplementMessage	|	undefined	|	Message containing supplemental information to access resources of a contract.	|
 |		|		| 	|
 |	<a id="ContractResponseMessage"></a>ContractResponseMessage	|	undefined	|	Message containing a response to a contract request (of a data consumer) in form of a counter-proposal of a contract in the associated payload (which is an instance of ContractOffcer).	|
 |		|		| 	|
 |	<a id="ContractRequestMessage"></a>ContractRequestMessage	|	POST {ids:Interactive Endpoint URL} or {Target resource URL}	|	Message containing a suggested content contract (as offered by the data consumer to the data provider) in the associated payload (which is an instance of ids:ContractRequest).	|
 |		|		| 	|
 |	<a id="ContractRejectionMessage"></a>ContractRejectionMessage	|	HTTP Response with status code 400	|	Message indicating rejection of a contract. .	|
 |		|		| 	|
 |	<a id="ContractOfferMessage"></a>ContractOfferMessage	|	POST {ids:Interactive Endpoint URL} or {Target resource URL}	|	Message containing a offered content contract (as offered by a data provider to the data consumer) in the associated payload (which is an instance of ContractOffer). In contrast to the ids:ContractResponseMessage, the ids:ContractOfferMessage is not related to a previous contract request.	|
 |		|		| 	|
 |	<a id="ContractAgreementMessage"></a>ContractAgreementMessage	|	HTTP Response to a ContractOfferMessage or ContractRequestMessage	|	Message containing a contract with resource access modalities on which two parties have agreed. .	|
 |		|		| 	|
 |	<a id="ConnectorUpdateMessage"></a>ConnectorUpdateMessage	|	PUT {URL of the connector at the server}<br/><br/>Connector instance as serialized RDF in the request body |	Event notifying the recipient(s) about a connector's configuration change. The payload of the message must contain the updated connector's self-description.	|
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|  |
 |		|		| 	|
 |	<a id="ConnectorUnavailableMessage"></a>ConnectorUnavailableMessage	|	DELETE {URL of the connector at the server}	|	Event notifying the recipient(s) that a connector will be unavailable and never be available again.	|
 |		|	POST {Connector Inbox URL}	|	message content as serialized RDF in the request body	|
 |		|		| 	|
 |	<a id="ConnectorInactiveMessage"></a>ConnectorInactiveMessage	|	DELETE {URL of the connector at the server}<br/><br/>Connector instance as serialized RDF in the request body	|	Message notifying the recipient(s), that a connector will be inactive and most likely be available again in the future.	|
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="ConnectorCertificateRevokedMessage"></a>ConnectorCertificateRevokedMessage	|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|	Indicates that a (previously certified) Connector is no more certified. This could happen, for instance, if the Certification Body revokes a granted certificate or if the certificate has just expired.	|
 |		|		| 	|
 |	<a id="ConnectorCertificateGrantedMessage"></a>ConnectorCertificateGrantedMessage	|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|	Whenever a Connector has been successfully certified by the Certification Body, the Identity Provider can use this message to notify Infrastructure Components.	|
 |		|		| 	|
 |	<a id="ConnectorAvailableMessage"></a>ConnectorAvailableMessage	|	POST {Broker Catalog URL}<br/><br/>Connector instance as serialized RDF in the request body	|	Event notifying the recipient(s) about the availability of a new connector. The payload of the message must contain the new connector's self-description.	|
 |		|	PUT {URL of the connector at the server}<br/><br/>Connector instance as serialized RDF in the request body	|		|
 |		|	POST {Connector Inbox URL}<br/><br/>message content as serialized RDF in the request body	|		|
 |		|		| 	|
 |	<a id="CommandMessage"></a>CommandMessage	|	POST {ids:Interactive Endpoint URL}[?{query parameter}]<br/><br/><br/><br/>A dedicated ids:Endpoint does not behave as a ldp:Container. A POST will not create a new child resource but trigger the distinct service offered by this endpoint.	|	Command messages are usually sent when a response is expected by the sender. Changes state on the recipient side. Therefore, commands are not 'safe' in the sense of REST.	|
 |		|		| 	|
 |	<a id="ArtifactResponseMessage"></a>ArtifactResponseMessage	|	HTTP Response	|	Message that follows up a ArtifactRequestMessage and contains the Artifact's data in the payload section.	|
 |		|		| 	|
 |	<a id="ArtifactRequestMessage"></a>ArtifactRequestMessage	|	GET {Artifact/Representation/Resource URL}<br/><br/>Header: "Accept: application/octet-stream"	|	Message asking for retrieving the specified Artifact as the payload of an ArtifactResponse message.	|
 |		|		| 	|
 |	<a id="AccessTokenRequestMessage"></a>AccessTokenRequestMessage	|	POST {Authorization Server} <br/><br/>Query Parameter: CLIENT_ID, CLIENT_SECRET, GRANT_TYPE, (AUTHORIZATION_)CODE, CALLBACK_URL, SCOPE, STATE	|	Message requesting an access token. This is intended for point-to-point communication with, e.g., Brokers.	|
 |		|		| 	|
 |	<a id="AccessTokenResponseMessage"></a>AccessTokenResponseMessage	|	See [RFC 6749]	|	Response to an access token request, intended for point-to-point communication.	|

 ##### *Table 2: IDS Message Types and their representation in the IDS REST protocol.*


### Encryption

Currently, TLS encryption of all messages is required in the IDS. Unencrypted
 requests, for instance plain HTTP, must always be rejected. Furthermore,
 certificates and keys used for the communication encryption must be valid
 and signed by a trustworthy CA. A client must not expect a reasonable
 response for non-encrypted requests, even if the server
 responds with a redirect to the HTTPS URL.


### Identification and Location

Following the IDS Information Model, IDS entities, like Participants, Connectors, or Resources, are represented by information
 documents containing a self-description  . In addition,
 each IDS entity has its own unique identifier. This identifier must be a unique URI.

IDS components can use this URI to find a document representing the entity.
 For instance, Connector A (https://example.org/ConnectorA) asks the IDS Metadata
 Broker for information about a Connector B (https://example.org/ConnectorB). Subsequently, the Broker
 responds with a document containing a representation of the current state of the
 Connector B (ConnectorB.jsonld) as far as the Metadata Broker has stored it.

Note that in this example, the URI of Connector B is not necessarily equal to the URL
 (https://companyB.com/ids/connector/) at which it's endpoints (https://companyB.com/ids/connector/data/)
 are located. Furthermore, Connector A must know the Broker's endpoint URL
 (https://industrialdataspace.jiveon.com/external-link.jspa?url=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc6749 )
 in advance. Connector A can then lookup the received document representing Connector B
 (ConnectorB.jsonld), discover the endpoint URL, and send a request to it.

The REST and LDP approach is significantly different. The identifying URI is bound to the
 location of the entity itself. Therefore, Connector B must not use
 https://example.org/ConnectorB but https://companyB.com/ids/connector/
 and provide at least a self-description document at this location. This enables
 Connector A by learning about a Connector B (identified by https://companyB.com/ids/connector/ )
 to directly communicate with it without further lookups on endpoints. The identification
 URI must always be the same as the root URL under which the entity is hosted. However,
 this simplification on the client connector-side increases the effort on the server
 connector-side as the later must always synchronize identities with hosting locations.


### Authentication

One of LDP's implementations is the Solid project to create a decentralized and self-controled social network. Different to the IDS, Solid relies on WebIDs for the identification of entities and their authentication.
The IDS transfers and validates identity claims using the Dynamic Attribute
 Token (DAT). The DAT contains, among other attributes, the signed identity
 of an IDS Connector.

Each IDS Connector involved in an interaction, both the origin server and the
 user agent, must verify the respective DAT of the counterpart by the following sequence

1. Compare the signature with the public key of the referenced Dynamic Attribute Provisioning Service (DAPS)
2. Compare the announced public keys with the certificate used for the TLS channel
3. Verify the iat, exp, nbf time stamps.
4. Check if the security claims match their own security specifications.


### Protocol

The operation semantics follows the typical REST semantics for CRUD operations. The
 following items state the usual interpretation of the eight HTTP methods
 as remote commands.

- GET: Read the target resource
- PUT: Overwrite the target resource state. If it did not exist, create it with the sent state. The desired resource state is in the request body.
- POST: Create a new resource  (if the target resource is an LDP Container). The desired resource state is in the request body. Undefined for non-LDP Containers.
- DELETE: Delete the target resource.
- HEAD: Read the meta-information of the target resource.
- OPTIONS: Read the allowed HTTP operations on the target resource.
- PATCH: Attach content to the target resource.
- CONNECT: unclear

The IDS REST binding follows this pattern. The following sections explain the
 generally used parts like headers, body and response structure, the error
 codes, and maps the IDS Messages to these interaction items.


### Request Headers

| Header Key  | Header Value |  Cardinality |
| :--- | --- | --- |
| ids-authorizationToken | Header to set a Connector-specific authorization token. | `optional` |
| ids-contentVersion | The version/revision of the data content in the body. | `optional` |
| ids-issuerConnector | Original Connector of the request. | `optional` |
| ids-modelVersion     | Information Model version, against which the Message should be interpreted. | `mandatory` if the body contains an IDS Entity. |
| ids-issued           | XSD DateTimeStamp (`xsd:dateTimeStamp`) of issuing the response.	 | `mandantory` |
| ids-senderAgent      | Agent who initiated the response | `mandantory` |
| ids-recipientAgent   | Agent, for which the response is intended. | `optional`, implicitly defined by the target resource |
| ids-securityToken    | Dynamic Attribute Token (`DAT`) representing the security-related claims, for instance that the sender supports a certain security profile. | `mandantory` |
| ids-transferContract | The contract which is (or will be) the legal basis of the data transfer. | `optional` |
|  |  |  |
| Accept | A comma-separated list of MIME types, which the client wants to accept. | `optional` |
| Content-Type         | MIME type of the data in the response body. | `mandatory` if the body is present. |
| If-Match | The version of the resource the client wants to change. Is used to detect collisions. The client is informed by the server of correct versions through the `ETag` response header. | `mandatory`, if the client wants to overwrite a remote resource |
| Slug | The desired new name of a resource, which the client wants to create. | `optional` |

##### *Table 3: Request headers.*

Full list of http-header attributes to use see [here](./header).:


## Request Body

For every class except an ids:Artifact, the body of the HTTP request must always contain a
 valid RDF serialization.  An ids:Artifact can also contain non-RDF or even binary data.
 In any case, the serialization of the body must be announced in the corresponding value
 of the "Content-Type" header. The default serialization in the IDS is JSON-LD
 (media type: 'application/ld+json'). Systems not able to operate on RDF can also treat
 the content as plain JSON and parse it like that (media type: 'application/json').

Still, every IDS compliant message exchange must use valid JSON-LD serializations,
 in particular by using the JSON key "@context" with either a proper local context
 definition or by linking to an appropriate remote context document. The default
 context element for the IDS is  "@context": "https://w3id.org/idsa/contexts/context.jsonld".

The context key-value pair must always be the first one in any IDS compliant JSON or JSON-LD object.



## Access Control

Access control (AC) to a given resource is inspired by the W3C work on the "Web
Access Control" (WAC) language. However, the permissions and restrictions that
can be expressed by WAC are not sufficient for the IDS scenarios, mainly due to
the missing support for Contract Classes. For instance, the context 'time' is
not considered by WAC.

In general, an IDS-REST client must add an DAT to its request. Without it, or if
the DAT is not valid or contains conflicting statements, for instance issued-at
before not-after, the IDS-REST server must directly reject the request. The
IDS-REST server must return a 40x status code and may return an additional
indication in the response body. Independent of incorrect DATs, an IDS-REST
server may also require custom Access Tokens. The recommended behavior is to use
the ids-authorizationToken request header. The tokens can be maintained through
well-established standards, like OAuth2, or through proprietary processes. This
gives the Providers of Connectors the ability to create own data spaces where
only dedicated Participants can access protected resources.



## Usage Control

The IDS-REST binding solely targets the interactions and operations at the
interface of a Connector to the outside network. Both client (Data Consumer)
and server (Data Provider) therefore only focus on Access restrictions for
enforcement. The explicit Usage Control tasks happen inside the Data Consumer
and are not directly affecting the client-server interaction anymore.

Nevertheless, the IDS-REST binding contains the means to express usage
restrictions as instances of Contracts. Contracts are represented as any other
Web resource and can retrieved and manipulated accordingly. Their distinct
meaning, to outline and control operations on data, is defined by the IDS
Information Model and further specifications. The IDS-REST binding is therefore
an agnostic layer to pave the way for Usage Control but does not by itself
add further specifications.


## Response

Every request message must be answered with the according HTTP response
 message. In case the receiving IDS Connector assumes a misuse, for
 instance, a denial of service attack, it could also block the requester.
 In any other case, the response message must contain a reference to the
 originating message, an HTTP status code, a set of response headers and
 optional data in the response body.


## Response Header

The response headers are the same as the request header.

Full list of http-header attributes to use see [here](./header).:


| Header Key  | Header Value |  Cardinality |
| :--- | --- | --- |
| ids-modelVersion     | Information Model version, against which the Message should be interpreted. | `mandatory` if the body contains an IDS Entity. |
| ids-issued           | XSD DateTimeStamp (`xsd:dateTimeStamp`) of issuing the response.	 | `mandantory` |
| ids-senderAgent      | Agent who initiated the response | `mandantory` |
| ids-recipientAgent   | Agent, for which the response is intended. | `optional`, implicitly defined by the target resource |
| ids-securityToken    | Dynamic Attribute Token (`DAT`) representing the security-related claims, for instance that the sender supports a certain security profile. | `mandantory` |
| ids-transferContract | The contract which is (or will be) the legal basis of the data transfer. | `optional` |
|  |  |  |
| Content-Type         | MIME type of the data in the response body. | `mandatory` if a response body is present. |
| Accept-Post	       | A comma-separated list of allowed media types, for instance "text/turtle", "application/ld+json" (for descriptions conforming to the IDS Information Model), or "application/octet-stream" (for binary data or IDS Artifacts). | `optional`  |
| Link                 | '<IDS class URI>; rel="type"' and/or '<http://www.w3.org/ns/ldp#Resource>; rel="type"' and/or '<http://www.w3.org/ns/ldp#BasicContainer>; rel="type"' | `mandatory` if the response body contains a LDP or IDS resource. |
| ETag                 | A unique version identifier representing the current state of the requested resource. | `mandatory` |
| Allow                | The enabled HTTP methods, selection of GET, OPTIONS, HEAD, PUT, PATCH, POST, DELETE, CONNECT | `mandatory` |

##### *Table 4: Response headers.*


## Status Codes

Although the IDS Information Model contains its own status codes, the
 commonly known HTTP codes are used as much as possible. The following
 table lists the available ones:

| Code  | Meaning |  Example |
| :--- | --- | --- |
| 1xx   |	The origin server has received the request and will continue working on it. The IDS not explicitly specifies the meaning of this group of status codes. ||
| 102   | Processing. The server needs some time to process the request. |  |
| 200   | Ok. The request is valid. May or may not respond with data. | |
| 201   | Created. A new resource has been created. The URL of the new resource has to be announced through the "Link" header. | |
| 204   | No Content. The server has received the request but does not send any content back. | |
| 301	| Moved Permanently. The target resource will not appear again at this location. Must return the new target URL. The client is assumed to send the request again to the announced URL. | |
| 303   | See other. The client is assumed to send the request again to the announced URL. | |
| 400   | Bad Request. The request was not compliant to this specification. The server may or may not indicate the problem. | |
| 403   | Forbidden. The user agent has no permission to access the target resource. The typical response also for invalid DATs. | |
| 404   | Not Found. The target resource does not exist. | |
| 405   | Method Not Allowed. The target method can not treat the request method. Could happen that IDS specifications do not allow the desired operation on the target resource. | Deleting the root resource (ids:Connector) results in a 405 error code. |
| 406   | Not Acceptable. The desired content types (see "Accept" header) can not be served. | |
| 409   | Conflict. The request does not fit to the current state of the target resource or violates specifications. The server may or may not indicate the problem. | |
| 410   | Gone. The resource exists no longer. Similar to 404 but with more information for the client. ||
| 500   | Internal Server Error. Something went wrong. The server may or may not indicate the problem or how to solve it. | |
| 501   | Not (Yet) Implemented. Endpoint might show up later. | |
| 503   | Service (Temporarily) Unavailable. Try later. | |

##### *Table 5: Status codes of responses.*


## Complex Operations and Workflows

Composed workflows or more elaborate interactions that can not be directly
 mapped to the previously outlined plain HTTP operations have to be managed
 by the User-Agent and the Origin Server respectively. While each Rest
 interaction - containing exactly one request/response pair - is exhaustive
 by definition, typical IDS workflows require more complex communication patterns.

That implies that even though the single interactions are stateless, and
 therefore only defined by the current state of the target resource and the
 HTTP message, the Origin Server and the User-Agent must remember the
 results of previous interactions to some degree. The respective obligations
 depend on the type of intended operation and is specified through the
 sub-classes of the IDS Message class. The following table outlines the
 binding of the Message semantic with the respective LDP binding for the IDS:

| Interaction Pattern  | IDS-REST Request Binding |  IDS-REST Response Binding | Consequence |
| :--- | --- | --- | --- |
|	Send a command (ids:CommandMessage)	|	POST to the operation endpoint. Further parameters or requirements need to be described by the endpoint itself.	|	102: Command execution takes more time, please wait.	|	unsafe, not idempotent	|
|		|		|	200: Operation successful, response body may contain the command result	|		|
|		|		|	40x: Error in the request, optional explanation in the response body.	|		|
|	Retrieve the description of any IDS instance	|	GET at the URL of the IDS instance ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve an IDS Contract(ids:DescriptionRequest)	|	GET at the respective path of the IDS Contract ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve an IDS Artifact (ids:ArtifactRequestMessage)	|	GET at the respective path of the IDS Artifact ("Accept: application/octet-stream" or any other fitting MIME type value, for instance "text/csv", "image/jpeg" etc.)	|	200: Body containing the target resource representation	|	safe, idempotent, memorizing not necessary	|
|		|		| 	|		|
|	Retrieve a description (ids:DescriptionRequest) of an IDS Resource	|	GET at the respective path of the IDS Resource ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve a description of an IDS Participant (ids:ParticipantRequest-Message)	|	GET at the respective path of the IDS Contract ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target participant representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve a description (ids:DescriptionRequest) of an IDS Representation	|	GET at the respective path of the IDS Representation ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve a description (ids:DescriptionRequest) of an IDS Connector	|	GET at the root path of the IDS Connector ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Retrieve a description (ids:DescriptionRequest) of an IDS Artifact	|	GET at the respective path of the IDS Artifact ("Accept: application/ld+json" or "Accept: application/json")	|	200: Body containing the target resource representation;	|	safe, idempotent, memorizing not necessary	|
|		|		|	403 or 406: Optional explanation in the response body.	|		|
|		|		| 	|		|
|	Response Message	|	Response messages are directly defined by HTTP Responses. No (asynchronous) interaction sequence is intended by the IDS-REST Binding.	|		|		|
|		|		| 	|		|
|	Request an Access Token (ids:AccessTokenRequest-Message)	|	POST to the Authorization Server as specified by OAuth2: CLIENT_ID, CLIENT_SECRET, GRANT_TYPE, (AUTHORIZATION_)CODE, CALLBACK_URL, SCOPE, STATE	|	200: AccessToken in response body.	|		|
|		|		|	invalid_request	|	see RFC 6749 - The OAuth 2.0 Authorization Framework 	|
|		|		|	unauthorized_client	|		|
|		|		|	access_denied	|		|
|		|		|	unsupported_response_type	|		|
|		|		|	invalid_scope	|		|
|		|		|	server_error	|		|
|		|		|	temporarily_unavailable (instead of 503)	|		|
|		|		| 	|		|
|	Register an IDS Resource at an IDS Metadata Broker	|	PUT to the offers list of a connector instance.	|	200: OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Register an IDS Participant at ParIS	|	POST to a ParIS Catalog. Slug Header contains the desired instance name, which the server is should use if possible.	|	201: Resouce Created, must return the "Location: <new URL>" Header	|	unsafe, not idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Register an IDS Participant at ParIS	|	PUT to a ParIS Catalog child instance.	|	200: OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Register an IDS Connector at an IDS Metadata Broker	|	POST to an IDS Catalog. <br/><br/> Slug Header contains the desired instance name, which the server is should use if possible.	|	201: Resouce Created, must return the "Location: <new URL>" Header	|	unsafe, not idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Register an IDS Connector at an IDS Metadata Broker	|	PUT to an IDS Catalog child instance.	|	201: Resouce Created, must return the "Location: <new URL>" Header	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Overwrite the state of an IDS Resource at an IDS Metadata Broker	|	PUT to an IDS Resource URL. The request body must contain the complete new instance state.	|	200: OK	|	unsafe, idempotent	|
|		|	If-Match header must be set accordingly.	|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Overwrite the state of an IDS Participant at a ParIS	|	PUT to a ParIS Catalog child instance.	|	200:OK	|	unsafe, idempotent	|
|		|	If-Match header must be set accordingly.	|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Overwrite the state of an IDS instance	|	PUT to a resource URL, which shall be created, for instance an IDS Participants, IDS Artifact, IDS Contract. The request body must contain the complete new instance state.	|	200:OK	|	unsafe, idempotent	|
|		|	If-Match header must be set accordingly.	|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Overwrite the state of an IDS Connector at an IDS Metadata Broker	|	PUT to a Catalog instance URL. The request body must contain the complete new instance state.	|	200:OK	|	unsafe, idempotent	|
|		|	If-Match header must be set accordingly.	|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	ids:ContractRequestMessage	|	POST to the referenced ContractOffer. If no (suitable) ids:ContractOffer is provided, target resource is the ids:Resource itself. Must contain a valid IDS Contract Request (in JSON-LD, if the server has not signaled other supported content types before).	|	200: ContractRequest is accepted, ContractAgreement is in the response body.	|	unsafe, not idempotent, both connectors must store at least the resulting ids:ContractAgreement.	|
|		|		|	400: HTTP Request itself is fine but the Contract Request is not accepted. A desired respective Contract Offer may be sent in the response payload.	|		|
|		|		| 	|		|
|	ids:ContractOfferMessage	|	POST to the referenced ContractRequest. If no (suitable) ids:ContractRequest is provided, target resource is the ids:Resource itself. Must contain a valid IDS Contract Offer (in JSON-LD, if the server has not signaled other supported content types before).	|	200: ContractOffer is accepted, ContractAgreement is in the response body.	|	unsafe, not idempotent, both connectors must store at least the resulting ids:ContractAgreement. 	|
|		|		|	400: HTTP Request itself is fine but the Contract Request is not accepted. A desired respective Contract Offer may be sent in the response payload.	|		|
|		|		| 	|		|
|	Execute a remote operation (ids:InvokeOperationMessage)	|	POST to the operation endpoint. Further parameters or requirements need to be described by the endpoint itself.	|	102: Operation execution takes more time, please wait.	|	unsafe, not idempotent	|
|		|		|	200: Operation successful, response body may contain the operation result	|		|
|		|		|	40x: Error in the request, optional explanation in the response body.	|		|
|		|		| 	|		|
|	Execute a complex query (ids:QueryMessage)	|	POST to the query endpoint. Query is included in the request body. <br/><br/> Content-Type Header presents the used query language, for instance "Content-Type: application/sparql-query". <br/><br/> The Accept Header outlines the desired response format, for instance "Accept: text/csv".	|	200: Query successful, response body containing the query result	|	safe, idempotent	|
|		|		|	400: Error in the request or the query, optional explanation in the response body.	|		|
|		|		| 	|		|
|	Execute a complex query (ids:QueryMessage)	|	GET to the query endpoint. Query parameters are percent-encoded using the HTTP query parameters 'query', 'default-graph-uri', and 'named-graph-uri'.	|	200: Query successful, response body containing the query result	|	safe, idempotent	|
|		|		|	400: Error in the request or the query, optional explanation in the response body.	|		|
|		|		| 	|		|
|	Delete the state of an IDS Participant at a ParIS	|	DELETE to the URL which represents the Participant in the ParIS Catalog.	|	200:OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		| 	|		|
|	Delete an IDS Resource at an IDS Metadata Broker	|	DELETE to the URL which represents the Resource in the Broker Catalog.	|	200:OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		| 	|		|
|	Delete an IDS instance	|	DELETE to a resource URL	|	200:OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		| 	|		|
|	Delete an IDS Connector at an IDS Metadata Broker	|	DELETE to a the URL which represents the Connector in the Broker Catalog.	|	200:OK	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		| 	|		|
|	Create an IDS instance	|	PUT to a resource URL, which shall be created, for instance an IDS Participants, IDS Artifact, IDS Contract. The request body must contain the complete new instance state.	|	201: Resouce Created	|	unsafe, idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|
|		|		| 	|		|
|	Create an IDS instance	|	POST to a parent instance, for example, an IDS Connector, IDS Resource, or IDS Catalog. Slug Header contains the desired instance name, which the server is should use if possible. If no request body is provided, the server creates a default instance, which must conform to the IDS Information Model.	|	201: Resouce Created, must return the "Location: <new URL>" Header	|	unsafe, not idempotent	|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
|		|		|	409: Conflict	|		|

##### *Table 6: Interaction patterns and their mapping in the IDS-REST binding.*



IDS REST conform Connectors must, in addition to the general criteria for IDS
Connectors, implement the following features:

- It must have a self-description hosted at its root path. Note that the root
path is not necessarily the first segment of an URL path but the first dedicated
section for the connector. For instance, for https://example.org/ids/connector/some/ids/endpoint/ the root path can start after 'connector/'
<!--  - a inbox for notifications at /inbox -->
- All entities have at least one URL which points to their location on a Connector server.
- All Resources/Representations/Artifacts must be able to handle related Contract
 interactions sent to them. At least an error message must be returned.


## Notifications

Notifications and publish/subscribe or push-based communication are not yet parts
of the IDS REST protocol binding. While this functionality may be introduced in the
future, the Linked Data Notification may serve as a preliminary reference.
See [here `IDS Linked Data Notification (IDS-LDN)`](./ldn)


## Identification and Naming of Resources

Even though the structure of the URL of a resource must not necessarily reflect its location on the server, it is
 recommended to apply this pattern by the Origin Server. For instance, creating a resource called
 https://bad-example.org/some-resource can be created at an IDS Connector at https://example.org/connector/:

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix ids:  <https://w3id.org/idsa/core/> .
@prefix ldp:  <http://www.w3.org/ns/ldp#> .

<https://example.org/connector/>
    a ids:BaseConnector, ldp:BaseContainer ;
    ids:catalog [
        a <https://example.org/connector/Catalog> ;
        ids:offers <https://bad-example.org/some-resource> ;
        ldp:contains <https://bad-example.org/some-resource> ;
    ] ;
    ldp:contains <https://example.org/connector/Catalog> ;
    #...
.
```

However, without further specifications, the Origin Server has the complete naming authority.
 It could therefore respond instead:

```http request
201

Link: https://example.org/connector/Catalog/some-resource
```


And update its state as:
```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix ids:  <https://w3id.org/idsa/core/> .
@prefix ldp:  <http://www.w3.org/ns/ldp#> .

<https://example.org/connector/>
    a ids:BaseConnector, ldp:BaseContainer ;
    ids:catalog [
          a <https://example.org/connector/Catalog> ;
          ids:offers <https://example.org/connector/Catalog/some-resourcehttps://bad-example.org/some-resource> ;
          ldp:contains <https://example.org/connector/Catalog/some-resource> ;
    ] ;
    ldp:contains <https://example.org/connector/Catalog> ;
    #...
.
```

Therefore, the User Agent must not expect its originally suggested name to be used by the Origin Server.
 However, the Origin Server should take the User Agent's suggestions into account, and serve them as long as possible.
  The client can use the "Slug" header to indicate the desired path extension.



## Equality of Resources

Despite ids:Contracts, the equality or non-equality of resources can be expected. For instance, the same
 `ids:DataResource` can be hosted at several IDS Connectors with no obligation to announce this fact.
 This is different for Contracts - and especially ids:ContractAgreements - as those imply legal consequences.
 A Contract published at one place must not have an extended copy at some other place. While the Contract itself can
 of course be copied, it must always the same URI.

Furthermore, no IDS Connector is allowed to rename, extend, update or shorten an ids:ContractAgreement.




## REST API Documentation (non-normative)

- [IDSA Swagger API](https://app.swaggerhub.com/apis/idsa/ids-connector/)

### Community-provided Descriptions
- https://app.swaggerhub.com/apis/jlangkau/IDS-LDP/
- [PolicyInformationPoint API Description](https://app.swaggerhub.com/apis/IDS_Association/PolicyInformationPoint/0.4.0#/IDS-REST)


## Example IDS REST Request (non-normative)


The example below shows a typical Multipart Message, sent with a POST request:

```http
--msgpart
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="header"

{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:ConnectorAvailableMessage",
  "@id" : "http://industrialdataspace.org/connectorAvailableMessage/34d761cf-5ca4-4a77-a7f4-b14d8f75636a",
  "issued" : "2019-12-02T08:25:08.245Z",
  "modelVersion" : "4.0.0",
  "issuerConnector" : "https://companyA.com/connector/59a68243-dd96-4c8d-88a9-0f0e03e13b1b",
  "securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "tokenFormat" : "https://w3id.org/idsa/code/tokenformat/JWT",
    "tokenValue" : "eyJhbGciOiJSUzI1NiIsInR5cCI..."
  }
}


--msgpart
Content-Type: application/json
Content-Disposition: form-data; name="payload"

<Payload: Self-description of the Connector>
```

This Multipart Message can be transformed to the following REST request:

```http
GET https://www.example.org/server/cpu_3
ids-modelVersion : "3.0.0",
ids-issuerConnector: "https://wall-e.nicos-rd.com/"
ids-securityToken: "eyJ1c2VyIjoiam90dCJ9...42"
ids-issued: "2019-12-02T08:25:08.245Z",
Accept: text/turtle
```


## References and Related Resources

| Name | Description | Location |
| :--- | --- | --- |
|	IDS Message Taxonomy	|	Documentation of the Message Types and their attributes. | [Website](http://htmlpreview.github.io/?https://github.com/International-Data-Spaces-Association/InformationModel/blob/feature/message_taxonomy_description/model/communication/Message_Description.htm) |
|	Linked Data Platform 1.0	|	Standard for read/write Linked Data	| [W3C Recommendation](https://www.w3.org/TR/ldp/) |
|	Web Access Control (WAC) Language	|	Method and Vocabulary to restrict access to Web Resources	| [W3C Wiki](https://www.w3.org/wiki/WebAccessControl) |
|	Web Access Control (WAC) specification | WAC specification as used by the Solid project	|		| [web access control spec](https://solid.github.io/web-access-control-spec/) |
| Learning Linked Data | Introduction to Linked Data	| [GitHub](https://github.com/solid/information/blob/master/documentation/learning-linked-data.md) |
|	The IDS Communication Guide	|	IDSA-internal guide how to interact with IDS Messages	| [IDSA Community Portal](https://industrialdataspace.jiveon.com/docs/DOC-2524)
|	ODRL Information Model 2.2	|	Vocabulary for the expression of Digital Rights	| [W3C Recommendation](https://www.w3.org/TR/odrl-model/) |
|	RFC 6749	|	RFC for OAuth 2	| [RFC 6749](https://tools.ietf.org/html/rfc6749) |
|	Linked Data Notifications	|	Specification how to exchange push messages conform to LDP interactions. | [W3C Recommendation](https://www.w3.org/TR/ldn/)	|


## Authors
| Name | Affiliation | Channel | Account |
| :--- | :--- | :--- | :--- |
| Sebastian Bader  | [Fraunhofer IAIS](https://www.iais.fraunhofer.de/) | GitHub  | [sebbader](https://github.com/sebbader)  |
| Jörg Langkau     | [nicos AG](https://www.nicos-ag.com/) | Mail    | jlangkau@nicos-rd.com  |
|                  |  | GitHub  | [jlangkau](https://github.com/jlangkau)  |
|                  |  | twitter | [@LostInDataSpace](https://twitter.com/LostInDataSpace)  |

## Contributers
| Name | Affiliation | Channel | Account |
| :--- | :--- | :--- | :--- |
| Stefanie Koslowski  | [Fraunhofer IAIS](https://www.iais.fraunhofer.de/) | GitHub  | [stefkoslowski](https://github.com/stefkoslowski)  |



## Version History
| Revision | Date | Author | Change |
| :--- | :--- | :--- | :--- |
| 0.6 | 17.06.2021 | [Bader](https://github.com/sebbader) | finalizing the renaming from IDS-LDP to IDS-REST |
| 0.5 | 30.04.2021 | [Bader](https://github.com/sebbader) | addressing the rest of the review comments and reworking the document for the upcoming specification; adding the Request Header Table |
| 0.4 | 15.01.2021 | [Bader](https://github.com/sebbader) | addressing the review comments |
| 0.3 | 04.11.2020 | [Bader](https://github.com/sebbader) | Correcting br tags and Table1 |
| 0.2 | 03.11.2020 | [Bader](https://github.com/sebbader) | adjusting tables, and deleting a few copy paste errors |
| 0.1 | 31.08.2020 | [Bader](https://github.com/sebbader) | Initial file |

---
