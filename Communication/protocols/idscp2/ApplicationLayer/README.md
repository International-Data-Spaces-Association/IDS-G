# IDSCP2 Application Layer

The following document describes the serialization of IDS Messages, consisting of header and payload. In many cases, this IDSCP2 Application Layer protocol it is run on top of the IDSCP2 Transport Layer, but the application layer protocol is agnostic to the concrete implementation of the transport layer. 
 
## Context
IDS messages consist of header and payload.

The header typically contains the "actual IDS message" as a JSON-LD serialization of a Message class as defined in the IDS Infomodel.

The payload of an IDS message contains data structures or arbitrary other data, textual or binary, whose semantics depend on the type of the message as defined by the header contents. Typical examples are serializations of contract offers, contract agreements, or data artifacts.

## Implementation in Protobuf

A well-known framework that supports all of these criteria is the open source solution protobuf.
It has been developed and is currently maintained by Google, but also supported by a large community.

Protobuf defines a message-format for platform-independent definition of message formats, which can be used to serialize common data fields like strings (as UTF-8), binary data, various kinds of fixed size or variable size integers, floating point values etc. into plain byte arrays that can be sent/received via any reliable binary communication protocol. More information is found in the official documentation: https://developers.google.com/protocol-buffers

A message definition for IDS messages looks as follows:

````protobuf
syntax = "proto3";

message IdsMessage {
  // Arbitrary header string
  string header = 1;
  // The actual, generic message payload
  bytes payload = 2;
}
````

The IDSCP2 App Layer Protocol uses exactly this message definition for IDS messages, plus an identically structured message definition for generic messages without a JSON-LD header, for more generic use cases. The IDSCP2 App Layer definitions are found in [idscp2-app.proto](./idscp2-app.proto).

## IDSCP2 Message Types
The message type is held within the header field called ‘idscp2.type’. Since it simply contains a serialization for the IDS Information Model Messages in JSON-LD, any defined IDS Message types can be supported.
Some examples for headers and payloads can be found [here](./Examples.md).

## Clearing House Interactions
Connectors can interact with the Clearing House using the IDSCP2 App Layer Protocol. The Clearing House accepts the IDS message types [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl), [QueryMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) and [RequestMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl). In most cases, the Clearing House expects additional headers. The following sections detail the possible interactions and list the expected headers as well as the results.

### Creating a Processes
The IDS Clearing House structures its logs into processes and requires that all messages are logged under a process id. Processes are meant to represent agreed upon data exchanges that include the storage of the contract agreement and all messages that document the data exchange. Processes can be created by any IDS connector with a valid DAT, who becomes the owner of the process. Additional owners can be defined in the parameters of the request. Only owners of the process may read or write log entries for the process. The Clearing House expects a [RequestMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) to create a process id.

#### Additional Headers
|Name|Type|Required|Description|
|---|---|---|---|
|ch-ids-pid|string|true|The unique identifier of the process that will be used for logging all information connected to the process|
|Content-Type|string|true|The Clearing House expects the content type "application/json"|

#### Expected Response
|Message|Meaning|Description|
|---|---|---|
|[MessageProcessedNotificationMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Created | The unique identifier has been created and is sent back as the payload.|
|[RejectionMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Error | An error occurred. The payload contains an HTTP error code.|


### Logging messages
To log information in the Clearing House a [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) must be sent. The payload contains the information that should be logged. The log entry stored in the Clearing House under the given process *pid*. Returns a signed receipt of the logged data in form of a JSON Web Token (JWT) as payload of the response.

#### Additional Headers
|Name|Type|Required|Description|
|---|---|---|---|
|ch-ids-pid|string|true|The unique identifier of the process under which the data should be logged|

#### Expected Response
|Message|Meaning|Description|
|---|---|---|
|[MessageProcessedNotificationMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Created | The payload contains the signed receipt of the logged data in form of a JWT.|
|[RejectionMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Error | An error occurred. The payload contains an HTTP error code.|

### Query multiple messages of a process
The Clearing House expects a [QueryMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) to retrieve multiple log entries. Pagination is enforced, so retrieving all log entries might require multiple requests. When querying the Clearing House the query may contain the requested *page* of data, the *size* of the requested page, and the sorting *order* of the results. The Clearing House answers the request with a [ResultMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl). The payload contains the log entries found, as well as the pagination information *page*, *size* and *order* that were used in the request. Each log entry is returned as a [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl).

#### Additional Headers
|Name|Type|Required|Description|
|---|---|---|---|
|ch-ids-pid|string|true| The unique identifier of the process under which the log entry was created|
|ch-ids-page|string|false|The requested result page. Defaults to 1|
|ch-ids-size|string|false|The size of the requested result page. Defaults to 100|
|ch-ids-sort|string|false|The order of the requested results. Defaults to "desc"|
|ch-ids-date_to|string|false|Only results before given date. Date format: YYYY-MM-DD|
|ch-ids-date_from|string|false|Only results after given date. Date format: YYYY-MM-DD|
|Content-Type|string|true|The Clearing House expects the content type "application/json"|

#### Expected Response
|Message|Meaning|Description|
|---|---|---|
|[ResultMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Ok | The Clearing House sends back a JSON containing both pagination information and an array of log entries in the form of [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)|
|[RejectionMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Error | An error occurred. The payload contains an HTTP error code.|


The following example illustrates the payload sent by the Clearing House as an answer to a QueryMessage for multiple log entries:
```json
{
  "date_from": "2022-09-13 00:00:00",
  "date_to": "2022-09-27 15:40:02",
  "page": 2,
  "size": 5,
  "order": "asc",
  "documents": [{
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
}]}
```

### Query a single message of a process
Retrieves the log entry that is stored under the given *id* and the given process *pid* in the Clearing House. The Clearing House answers the request with a [ResultMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) that contains as the payload the log entry found. The log entry is returned as a [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl), i.e., the payload of the [ResultMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl) contains a [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl).

#### Additional Headers
|Name|Type|Required|Description|
|---|---|---|---|
|ch-ids-pid|string|true| The unique identifier of the process under which the log entry was created|
|ch-ids-id|string|true|The unique identifier of the log entry requested|
|Content-Type|string|true|The Clearing House expects the content type "application/json"|

#### Expected Response
|Message|Meaning|Description|
|---|---|---|
|[ResultMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Ok | The Clearing House sends back the requested log entry in the form of [LogMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)|
|[RejectionMessage](https://github.com/International-Data-Spaces-Association/InformationModel/blob/v4.1.0/taxonomies/Message.ttl)| Error | An error occurred. The payload contains an HTTP error code.|