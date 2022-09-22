# IDS over Multipart Messages


# 1. Messages
The IDS Information Model defines a generic [Message](../../Message-Structure/README.md) class and properties as well as specific [message subclasses](../../Message-Types/README.md), which are used for communication in the IDS. 

The generic Message class itself is usually not used for communication in the IDS, but to model similar properties of the different messages. The Information Model provides a set of specific message types for different scenarios. The message types can be grouped into three main message classes:

* RequestMessages
* ResponseMessages
* NotificationMessages

The message type and properties are part of the Message header. Further describing the individual message types would go beyond the scope of this document. All available message types with additional information about their usage, payloads, property restrictions and new properties can be found in the following table: [Link to Message table](http://htmlpreview.github.io/?https://github.com/International-Data-Spaces-Association/InformationModel/blob/feature/message_taxonomy_description/model/communication/Message_Description.htm) (updated continuously).  

The Information Model does not provide an explicit modeling about the payload of a message, except the formalization in the description fields of the message types and the Message documentation mentioned above.



## 2. Structure of a Multipart Message
For HTTP Multipart communication, each request and response in the IDS is represented as a message of content type multipart/mixed or multipart/form-data with at least one part. The first part MUST contain the instantiated message class of the information model (e.g., SelfDescriptionRequest), serialized as JSON-LD (named "header"). The second part of the request contains the associated payload of the message type as defined in the information model (consequently named "payload"). For instance for a BrokerQueryMessage, this part contains the textual query (e.g., SPARQL SELECT or any other supported query format) that should be processed by the Broker. Messages structured in that way are independent of the chosen communication method and protocol, i.e., they may be transferred synchronously by using HTTPS, asynchronously by using MQTT or by other session or stream-based protocols (IDSCP). For IDSCP, however, metadata is exchanged just once for each connection and this only is true if multipart messages are tunneled.

As an example, let's consider an IDS message of type ids:DescriptionRequestMessage which does not require any payload part. In order to send this message to a connector that supports the synchronous HTTP API, the serialized instance of the SelfDescriptionRequest class needs to be sent through an HTTP POST request to the /infrastructure endpoint of the receiving connector. Note that this request message type does not require a payload section, as all necessary information is supplied through the header attributes. The POST body itself MUST be of content-type multipart/form-data or multipart/mixed, e.g., multipart/form-data; boundary=--msg-boundary with this exemplary raw content:

```
--msg-boundary
Content-Disposition: form-data; name="header"

{
"@context" : "https://w3id.org/idsa/contexts/context.jsonld",
"@type" : "ids:DescriptionRequestMessage",
"modelVersion" : "4.1.0",
"issued" : {
    "@value" : "2019-12-21T13:32:33.073+02:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
"issuerConnector" : { "@id": "http://example.org#connector" },
"requestedElement" : { "@id": "http://example.org/some-URI" },
"@id" : "https://w3id.org/idsa/autogen/selfDescriptionRequest/b0731661-7df1-43e5-bb75-50f0709f31c9"
}
--msg-boundary--
```

However, in this minimal example, the receiving connector cannot verify the authenticity of the requesting connector, i.e., it cannot determine if the sender is (still) a valid participant on the IDS ecosystem or if the claimed facts in its self-description are actually true or not. In order to provide this kind of security, each request needs to be accompanied by a Dynamic Attribute Token (DAT) (see XXX). A connector that is in possession of this token SHOULD/MUST present it at each outgoing message exchange so that the receiver can base access control decisions on it. Note that it is up to the receiving connector to handle and interpret the security token DAT. The header part of the above example with included security token looks like this:

```
--msg-boundary
Content-Disposition: form-data; name="header"

{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:DescriptionRequestMessage",
  "modelVersion" : "4.1.0",
  "issued" : {
      "@value" : "2019-12-21T13:32:33.073+02:00",
      "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
    },
  "issuerConnector" : {"@id" : "http://example.org#connector" },
  "securityToken" : {
      "@type" : "ids:DysnamicAttributeToken",
      "tokenValue" : "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImRlZmF1bHQifQ.eyJ...",
      "tokenFormat" : { "@id": "https://w3id.org/idsa/code/tokenformat/JWT" }
    },
    "requestedElement" : { "@id": "http://example.org/some-URI" },
  "@id" : "https://w3id.org/idsa/autogen/selfDescriptionRequest/b0731661-7df1-43e5-bb75-50f0709f31c9"
}
--msg-boundary--
```
The message below shows the receiving connector's response to the incoming message. When the response is received using HTTP, the content-type header would be "multipart/form-data; boundary=mPLw1UTMYjqqYqh1Bb_ttWBKcdSPfB9FBgz3;charset=UTF-8". Note that the boundary is just an example and is usualy auto-generated by the communication tool or library that is used by the sending connector. As can be seen in the "header" part of the message, the message type is DescriptionResponse. It is linked to the previous message through the correlationMessage property.

JSON-LD is the required format for any header content. In the example, it has been generated by the publicly available Java library that implements the Information Model. Using this library is not mandatory but eases the parsing of valid IDS statements and messages. Implentations in other programming languages may also come in the future. As the message below is a response to a self-description request, it contains the requesting connector's self description in its payload section. Note that the content of the payload depends on the message type: if, for instance, the message type is a general OperationResult, then the payload might be binary data or any other format, depending on the operation that was invoked by the original request.

```
--msgpart
Content-Disposition: form-data; name="header"
Content-Type: application/json
Content-Length: 409

{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:DescriptionResponseMessage",
  "issued" : {
      "@value" : "2019-12-21T13:11:14.596+02:00",
      "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
    },
  "issuerConnector" : {"@id" : "https://broker.ids.isst.fraunhofer.de/" },
  "correlationMessage" : {"@id" : "https://w3id.org/idsa/autogen/selfDescriptionRequest/b0731661-7df1-43e5-bb75-50f0709f31c9" },
  "modelVersion" : "4.1.0",
  "@id" : "https://w3id.org/idsa/autogen/selfDescriptionResponse/851e3218-2bb7-45f9-8795-7f99c1f19680"
}
--msgpart
Content-Disposition: form-data; name="payload"
Content-Type: application/ld+json
Content-Length: 965

{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:Broker",
  "outboundModelVersion" : "4.1.0",
  "descriptions" : [ {
    "@value" : "A Broker with a graph persistence layer",
    "@language" : "en"
  } ],
  "inboundModelVersions" : [ "4.0.0", "4.1.0", "4.2.0" ],
  "titles" : [ {
    "@value" : "IAIS Broker",
    "@language" : "en"
  } ],
  "maintainer" : {"@id" : "https://www.iais.fraunhofer.de" },
  "curator" : {"@id" : "https://www.iais.fraunhofer.de" },
  "resourceCatalog" : {
    "@type" : "ids:ResourceCatalog",
    "@id" : "https://w3id.org/idsa/autogen/catalog/a50c93a3-388b-4bbd-959e-a446b7ec2946"
  },
  "securityProfile" : {
      "@type" : "ids:SecurityProfile",
      "@id" : "https://w3id.org/idsa/code/BASE_CONNECTOR_SECURITY_PROFILE"
    },
    "@id" : "https://w3id.org/idsa/autogen/securityProfile/cca6c0e6-ad34-4d66-8137-ab94e3fad424"
  },
  "@id" : "https://broker.ids.isst.fraunhofer.de/"
}
--msgpart--
```


## 3. Self-Description

Information about the Self-Description can be found [here](../../Message-Structure/README.md#self-description).


## 4. Interactions with Infrastructure Components

### 4.1 Metadata Broker Communication

The IDS Broker acts as a registration and discovery service for the other connectors in the IDS. As such, it receives information about the availability of connectors, their characterisitics and offers. The Broker persists these information and enables queries. As not only connectors would like to search for resources, a Broker should also host a website enabling the necessary interactions in a user-friendly manner.

The default interaction protocol for the IDS Broker is HTTPS. Any IDS Broker should therefore serve the following endpoints:

| Enpoint |	Description |
| :--- | --- |
| https://<host>/<path/to/broker>/ |	Optional: the IDS Broker, as any IDS Connector, should publish a self-description at its root |
| https://<host>/<path/to/broker>/infrastructure |	The general endpoint for infrastructure messages. Main endpoint for any Broker interaction, for instance for ConnectorInactiveMessages and ConnectorUpdateMessages. |
| https://<host>/<path/to/broker>/data |	The connector-specific endpoint of the Broker. Can be used for DescriptionRequestMessages and QueryMessages. |
| https://<host>/<path/to/broker>/browse  |	Optional: The website hosting query functionalities for the human user. |

##### *Table 1: Endpoints of an IDS Metadata Broker*

The path segment of the URL is optional. In cases the Broker is one service of many of a certain authority, the path is necessary in order to link to the correct server.


#### 4.1.2 Hosts and Endpoints
An endpoint is defined in each resource (ids:Resource) and contains information regarding the access to a Resource’s contents, e.g., the Artifact. There are two types of endpoints:

* **Static Endpoints** (ids:StaticEndpoint), which are used for exposing an Artifact
* **Interactive Endpoints** (ids:InteractiveEndpoint), which do not reference a specific Artifact, but an operation binding (ids:OperationBinding). Operation bindings refer to a specific operation and an API document that describes the protocol details
Additionally, an endpoint contains the following fields / properties:


|Property |	Description |
| ------------- |:-------------|
|endpointHost*	|Reference to a network host of a connector (type ids:Host), with an access URL and protocol (e.g., HTTP2, IDSCP), to access the contents of a resource|
|endpointArtifact* (only ids:StaticEndpoint)	|Points to an Artifact (see above, Static Endpoints)|
|endpointOperationBinding* (only ids:InteractiveEndpoint)	|Reference an Operation Binding (see above, Interactive Endpoints)|
|path*	|Relative path, at which the content is published by the related host|
|inboundPath	|Path used for inbound communication via this endpoint, i.e., communication to this endpoint is performed via this path|
|outboundPath	|Relative path used for outbound communication via this endpoint, i.e., communication from the endpoint is performed via this path|

\* mandatory fields



The **endpointHost** maps to a network host of a Connector, from which the data consumer can get access to the contents of the resource. The following example contains a partial snippet of a Connector’s self-description for the network host specification as well as a snippet of a Static Endpoint in a Resource (ids:Resource)



Partial Snippet of Connector's self-description (ids:host property)

```
"host" : [{
   "@type" : "ids:Host",
   "protocol" : {
       "@id" : "https://w3id.org/idsa/code/HTTP2"
   },
   "accessUrl" : {
       "@id" :  "https://connector.fit.fraunhofer.de/httphost"
   }
}]
```


Partial Snippet of a Resource's metadata (ids:resourceEndpoint property)
```
"resourceEndpoint": {
   "@type": "ids:StaticEndpoint",
   "endpointHost": "<reference to HTTP2 host of the connector>",
   "endpointArtifact": "<Reference to the Artifact>",
   "path": "/reports/Highway_accident_statistics.pdf"
}
```


#### 4.1.4 Connector Registration

A Connector is registered at an IDS Metadata Broker through a IDS ConnectorUpdateMessage.

#### 4.1.5 Connector Update

The Connector Update Workflow is the same as the one for registering a new
Connector. Note that the old Connector Self-Description is completely overwritten
by the new request.:

```
--msgpart
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="header"

{
"@context" : {
  "ids" : "https://w3id.org/idsa/core/",
  "idsc" : "https://w3id.org/idsa/code/"
},
"@type" : "ids:ConnectorUpdateMessage",
"@id" : "https://w3id.org/idsa/autogen/1119667d-ea7b-4f3b-8a74-8cecaab1fa41",
"ids:securityToken" : {
  "@type" : "ids:DynamicAttributeToken",
  "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/ec96a61a-8725-4227-90e7-a1976e6d4dfe",
  "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0IiwiYWxnIjoiUlMyNTYifQ...",
  "ids:tokenFormat" : {
    "@id" : "idsc:JWT"
  }
},
"ids:senderAgent" : {
  "@id" : "http://example.org"
},
"ids:issued" : {
  "@value" : "2020-06-23T17:16:02.723+02:00",
  "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
},
"ids:issuerConnector" : {
  "@id" : "https://test.connector.de/"
},
"ids:modelVersion" : "4.1.0",
"ids:affectedConnector" : {
  "@id" : "https://test.connector.de/"
}
}

--msgpart
Content-Type: application/json
Content-Disposition: form-data; name="payload"

<Self-description in JSON-LD>
```
##### *Listing 1: Multipart message to register a new Connector.*


#### 4.1.6 ConnectorUnavailableMessage

To remove a Connector registration from the Broker, a ConnectorUnavailableMessage
is used.

```
--msgpart
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="header"

{
    "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
    "@type" : "ids:ConnectorUnavailableMessage",
    "@id" : "http://industrialdataspace.org/connectorUnavailableMessage/1a421b8c-3407-44a8-aeb9-253f145c869a",
    "issued" : {
      "@value" : "2019-10-25T15:35:34.589+02:00",
      "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
    },
    "modelVersion" : "4.1.0",
    "ids:issuerConnector" : {
      "@id" : "https://test.connector.de/"
    },
    "securityToken" : {
      "@type" : "ids:DynamicAttributeToken",
      "@id" : "http://industrialdataspace.org/token/e43c08e1-157b-4207-94a8-754e53f48839",
      "ids:tokenFormat" : {
        "@id" : "idsc:JWT"
      },
      "tokenValue" : "eyJhbGciOiJSUzI1NiIsInR5cCI..."
    }
}
--msgpart--
```
Note that no payload is required. The mentioned connector is always the issuning connector. That implies that no third party can send this type of message. An IDS Broker may however allow other options to remove a connector's descriptions, for instance through its Web UI.


#### 4.1.7 Browsing/Querying Broker Data
A Broker might provide query functionality by accepting SPARQL messages like the one below. However, a Broker instance might support different query languages as e.g. SQL or Cypher.


#### 4.1.8 QueryMessage

A IDS QueryMessage is used to send complex queriies, for instance to search for
all registered Connectors and the information model versions they support:

```
--msgpart
Content-Type: application/json
Content-Disposition: form-data; name="header"

{
"@context" : {
  "ids" : "https://w3id.org/idsa/core/",
  "idsc" : "https://w3id.org/idsa/code/"
},
"@type" : "ids:QueryMessage",
"@id" : "https://w3id.org/idsa/autogen/queryMessage/dbb77622-7508-4630-9830-aa07b196eebc",
"ids:securityToken" : {
  "@type" : "ids:DynamicAttributeToken",
  "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/f9f2b139-0e9b-4e6f-b320-abf22a7224aa",
  "ids:tokenFormat" : {
    "@id" : "idsc:JWT"
  },
  "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0IiwiYWxnIjoiUlMyNTYifQ..."
},
"ids:senderAgent" : {
  "@id" : "http://example.org"
},
"ids:modelVersion" : "4.1.0",
"ids:issued" : {
  "@value" : "2020-06-23T16:10:57.781+02:00",
  "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
},
"ids:issuerConnector" : {
  "@id" : "https://test.connector.de/"
},
"ids:queryLanguage" : {
  "@id" : "idsc:SPARQL"
},
"ids:queryScope" : {
  "@id" : "idsc:ALL"
}
}
--msgpart
Content-Type: text/plain
Content-Disposition: form-data; name="payload"

PREFIX ids: <https://w3id.org/idsa/core/>
SELECT ?connectorUri ?version WHERE {
  ?connectorUri a ids:BaseConnector;
  ids:inboundModelVersion ?version .
}
--msgpart--
```

The Broker might answer with a ResultMessage or reject the request
(RejectionMessage with a helpful rejection reason).


#### 4.1.8 Requesting explicit Connectors and Resources

Send a DescriptionRequestMessage to receive the Self-Description of known
Connectors or Resources.


### 4.2 Clearing House Interactions
The Clearing House is a central component that provides access to a trustworthy persistence store. Connectors may log messages in the Clearing House and read messages from it. Using the Clearing House is optional for all connectors. A valid DAPS token is required in all requests to the Clearing House.

#### 4.2.1 Endpoints
The default interaction protocol for the IDS Clearing House is HTTPS. Any IDS Clearing House should therefore serve the following endpoints:

| Endpoint |	Description |
| :--- | --- |
| `https://<host>/<opt-path-to-ch>/` |	Optional: the IDS Clearing House, as any IDS Connector, should publish a self-description at its root |
| `https://<host>/<opt-path-to-ch>/process` |	The endpoint for creating process in the Clearing House |
| `https://<host>/<opt-path-to-ch>/messages/log` |	The endpoint for logging messages in the Clearing House |
| `https://<host>/<opt-path-to-ch>/messages/query` | The endpoint for querying messages from the Clearing House |


##### *Table 1: Endpoints of an IDS Clearing House


#### 4.2.2 Creating a Processes
The IDS Clearing House structures its logs into processes and requires that all messages are logged under a process id. Processes are meant to represent agreed upon data exchanges that include the storage of the contract agreement and all messages that document the data exchange. Processes can be created by any IDS connector with a valid DAT, who becomes the owner of the process. Additional owners can be defined in the parameters of the request. Only owners of the process may read or write log entries for the process.

|Request method | Description |
|---|---|
|`POST /process/<pid>`| Creates a new log process id|

#### Parameters
|Name|In|Type|Required|Description|
|---|---|---|---|---|
|pid|path|string|true|The unique identifier of the process that will be used for logging all information connected to the process|
|owners|body|json|false|A list of owners that may log and access data in the given pid|

#### Response Codes
|Status|Meaning|Description|Schema|
|---|---|---|---|
|201|[Created](https://tools.ietf.org/html/rfc7231#section-6.3.2)|Created|[MessageProcessedNotificationMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|Unauthorized|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Error|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|

#### 4.2.3 Logging messages
The information that should be logged in the Clearing House (*logData*) is sent in the payload of a [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure). The log entry stored in the Clearing House consists of the payload and meta-data from the [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) and is stored under the given process *pid*. Returns a signed receipt of the logged data in form of a JSON Web Token (JWT) as payload of the response.

|Request method | Description |
|---|---|
|`POST /messages/log/<pid>`| Creates a new log entry under the given process id|

#### Parameters
|Name|In|Type|Required|Description|
|---|---|---|---|---|
|pid|path|string|true|Process id under which the message should be logged.|
|logData|body|[LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|true|[LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) with information that should be logged in the payload|

#### Response Codes
|Status|Meaning|Description|Schema|
|---|---|---|---|
|201|[Created](https://tools.ietf.org/html/rfc7231#section-6.3.2)|Created|[MessageProcessedNotificationMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|Unauthorized|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Error|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|

#### Example Http Multipart Request
```http
POST /logs/messages/test-process HTTP/1.1
Host: ch-ids.aisec.fraunhofer.de
Content-Type: multipart/form-data; boundary=X-TEST-REQUEST-BOUNDARY
Accept: */*

--X-TEST-REQUEST-BOUNDARY
Content-Disposition: form-data; name="header"
Content-Type: application/json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:LogMessage",
  "@id" : "https://w3id.org/idsa/autogen/logMessage/c6c15a90-7799-4aa1-ac21-9323b87a7xv9",
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/6378asd9-480d-80df-c5cb02e4e260",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    },
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0IiwiYWxnIjoi....."
  },
  "ids:senderAgent" : "http://example.org",
  "ids:modelVersion" : "4.1.0",
  "ids:issued" : {
    "@value" : "2020-12-14T08:57:57.057+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:issuerConnector" : {
    "@id" : "https://companyA.com/connector/59a68243-dd96-4c8d-88a9-0f0e03e13b1b"
  }
}
--X-TEST-REQUEST-BOUNDARY
Content-Disposition: form-data; name="payload"
Content-Type: application/json
{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:ConnectorUpdateMessage",
  "id" : "http://industrialdataspace.org/connectorAvailableMessage/34d761cf-5ca4-4a77-a7f4-b14d8f75636a",
  "issued" : "2019-12-02T08:25:08.245Z",
  "modelVersion" : "4.1.0",
  "issuerConnector" : "https://companyA.com/connector/59a68243-dd96-4c8d-88a9-0f0e03e13b1b",
  "securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "tokenFormat" : "https://w3id.org/idsa/code/tokenformat/JWT",
    "tokenValue" : "eyJhbGciOiJSUzI1NiIsInR5cCI..."
}
--X-TEST-REQUEST-BOUNDARY--
```

#### 4.2.4 Query all messages of a process
Retrieves all log entries that are stored under the given *pid* in the Clearing House. The Clearing House answers the request with a [ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) that contains as the payload all log entries found. Each log entry is returned as a [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure), i.e., the payload of the [ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) contains a `json` array of [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure).

|Request method | Description |
|---|---|
|`POST /messages/query/<pid>`| Finds all log entries stored under the given process id *pid*|

#### Parameters
|Name|In|Type|Required|Description|
|---|---|---|---|---|
|pid|path|string|true|Process id under which the log entry is stored|
|query|body|[QueryMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|true|[QueryMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|

#### Response Codes
|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful operation|[ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) containing a `json` array of [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|Unauthorized|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Error|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|

The following example illustrates an answer of the Clearing House to a multipart request with a QueryMessage:
```http
HTTP/1.1 200 OK
Server: nginx/1.21.1
Date: Tue, 20 Jul 2021 12:50:05 GMT
Content-Type: multipart/form-data; boundary=336749cd-8331-46b4-b75d-d9d2ae80e3ac
Transfer-Encoding: chunked
Connection: keep-alive
Accept: */*
permissions-policy: interest-cohort=()
x-content-type-options: nosniff
x-frame-options: SAMEORIGIN

--336749cd-8331-46b4-b75d-d9d2ae80e3ac
content-disposition: form-data; name="header"
content-type: application/json; charset=UTF-8
content-transfer-encoding: 8bit
{
  "@context": {
    "ids": "https://w3id.org/idsa/core/",
    "idsc": "https://w3id.org/idsa/code/"
  },
  "@type": "ids:ResultMessage",
  "@id": "https://w3id.org/idsa/autogen/ResultMessage/cfe8bd4f-c8b5-42e4-aaae-1f594fa80719",
  "ids:issued": {
    "@value": "2021-07-20T12:50:04.916Z",
    "@type": "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:issuerConnector": {
    "@id": "https://clearinghouse.aisec.fraunhofer.de/"
  },
  "ids:recipientConnector": [
    {
      "@id": "https://broker.ids.isst.fraunhofer.de/"
    }
  ],
  "ids:securityToken": {
    "@type": "ids:DynamicAttributeToken",
    "@id": "https://w3id.org/idsa/autogen/dynamicAttributeToken/d9bd7b02-89ca-4e08-be3c-f3c149d62a62",
    "ids:tokenValue": "eyJ0eXAiOiJKV1QiLCJraWQiOi...",
    "ids:tokenFormat": {
      "@id": "idsc:JWT"
    }
  },
  "ids:senderAgent": {
    "@id": "https://clearinghouse.aisec.fraunhofer.de"
  },
  "ids:recipientAgent": [
    {
      "@id": "http://example.org"
    }
  ],
  "ids:correlationMessage": {
    "@id": "https://w3id.org/idsa/autogen/logMessage/c6c15a90-7799-4aa1-ac21-9323b87a7xv9"
  },
  "ids:modelVersion": "4.1.0"
}

--336749cd-8331-46b4-b75d-d9d2ae80e3ac
content-disposition: form-data; name="payload"
content-type: application/json
content-transfer-encoding: 8bit
[
  {
    "@context": {
      "idsc": "https://w3id.org/idsa/code/",
      "ids": "https://w3id.org/idsa/core/"
    },
    "@type": "ids:LogMessage",
    "@id": "https://w3id.org/idsa/autogen/logMessage/c6c15a90-7799-4aa1-ac21-9323b87a7xv9",
    "ids:modelVersion": "4.1.0",
    "ids:issued": "2021-07-20T12:50:04.916267339+00:00",
    "ids:issuerConnector": "https://provider.ids.isst.fraunhofer.de/",
    "ids:senderAgent": "http://example.org",
    "payload": "\"YXNhZnNzd2V3c2Vyd2VmcndlZnJ3ZWZydw==\"",
    "payload_type": "\"text/plain\""
  },
  {
    "@context": {
      "idsc": "https://w3id.org/idsa/code/",
      "ids": "https://w3id.org/idsa/core/"
    },
    "@type": "ids:LogMessage",
    "@id": "https://w3id.org/idsa/autogen/logMessage/c6c15a90-7799-4aa1-ac21-9323b87a7xv9",
    "ids:modelVersion": "4.1.0",
    "ids:issued": "2021-07-20T12:50:04.916327776+00:00",
    "ids:issuerConnector": "https://consumer.ids.isst.fraunhofer.de/",
    "ids:senderAgent": "http://example.org",
    "payload": "\"YXNhZnNzd2V3c2Vyd2VmcndlZnJ3ZWZydw==\"",
    "payload_type": "\"text/plain\""
  }
]
--336749cd-8331-46b4-b75d-d9d2ae80e3ac--
```

#### 4.2.5 Query a single message of a process
Retrieves the log entry that is stored under the given *id* and the given process *pid* in the Clearing House. The Clearing House answers the request with a [ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) that contains as the payload the log entry found. The log entry is returned as a [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure), i.e., the payload of the [ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) contains a [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure).

|Request method | Description |
|---|---|
|`POST /messages/query/<pid>/<id>`| Finds the log entry with the given *id* stored under the given process id *pid*|

#### Parameters
|Name|In|Type|Required|Description|
|---|---|---|---|---|
|pid|path|string|true|Process id under which the log entry is stored|
|id|path|string|true|Id of the log entry|
|query|body|[QueryMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|true|[QueryMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|

#### Response Codes
|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful operation|[ResultMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure) containing a `json` array of [LogMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|Unauthorized|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Error|[RejectionMessage](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Communication/Message-Structure)|
