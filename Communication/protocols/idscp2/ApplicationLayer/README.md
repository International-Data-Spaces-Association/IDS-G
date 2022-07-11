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
