# ids:Message

Core `ids:Message` class with it's properties, which are equal for all messages.
 For communication in the IDS, use the specific message types.


## Properties of ids:Message

### ids:correlationMessage

Correlated message. Usually needed, if a messages responds to a previous

> TODO: MessageProcessedNotification >>> MessageProcessedNotification?

 message. A [Connector](../../Connector/) may, e.g., send a [MessageProcessedNotification](#idsmessageprocessednotificationmessage)` as
 a response to an incoming message and therefore needs this property to
 refer to the incoming message.


### ids:securityToken

The `mandantory` property 'securityToken' contains (value) a token, representing a claim, that
 the sender supports a certain security profile.

This token is called [Dynamic Attribute Token (DAT)](../../DAPS/README.md#dynamic-attribute-token-dat),
 (see further explanations there), so it is an instance of ids:DynamicAttributeToken and will be provided by [Dynamic Attribute Provisioning Service (DAPS)](/DAPS/README.md#dynamic-attribute-provisioning-service-daps). 

---

### Table of Message Properties

|**Property**|mandantory|cardinality|
|:---|:---:|:---|
|**[ids:correlationMessage](#idscorrelationmessage)**|x|0..1|                        
|**[ids:securityToken](#idssecuritytoken)**|x|1|                        
|||

---

## Message Types

The table contans descriptions for each message, information about the payload
 as well as changes in the properties, i.e., new properties and additional
 mandatory properties. "Message Class" and "Message Subclass" are just for
 structural / hierarchical descriptions. The "Message Name" contains the
 actual Message which is used.
 
New properties, which are mandatory are marked with an (*). Existing
 properties, which are mandatory for a specific messages are listed
 in the corresponding column.

All mandatory property declarations of the core ids:Message above still hold.
 Optional properties are supported but not required.


### Request Messages

##### ids:RequestMessage

##### ids:DescriptionRequestMessage

Message requesting metadata. If no URI is supplied via the ids:requestedElement field,
 this messages is treated like a self-description request and the recipient should
 return its self-description via an ids:DescriptionResponseMessage. However, if a URI
 is supplied, the Connector should either return metadata about the requested element
 via an ids:DescriptionResponseMessage, or send an ids:RejectionMessage, e.g. because
 the element was not found.

Requests: 
- [DescriptionRequestMessage, POST](./requests/DescriptionRequestMessage_POST.md)

### Response Messages

##### ids:ResponseMessage

Response messages hold information about the reaction of a recipient to a
 formerly sent command or event. They must be correlated to this message. May
 be used for messages, which are not covered by the core IDS messages.


### Notification Messages

##### ids:MessageProcessedNotificationMessage

#### ids:ConnectorNotificationMessage

Event notifying the recipient(s) about the availability of a new connector.
 The payload of the message must contain the new connector's self-description.

See also: [International Data Space Information Model](https://w3id.org/idsa/core)

##### ids:ConnectorAvailableMessage

---

### Table of Message Types

| **Message Class** | Sub Class | Message Name |
|:---|:---|:---|
|**[Request Messages](#request-messages)**            |                                 | [ids:RequestMessage](#idsrequestmessage)
|**[Response Messages](#response-messages)**            |                               | [ids:ResponseMessage](#idsresponsemessage)
|**[Notification Messages](#notification-messages)**  | [ids:ConnectorNotificationMessage](#idsconnectornotificationmessage) | [ids:ConnectorAvailableMessage](#idsconnectoravailablemessage)
|||


## Requests

Collection of valid request templates can be found [here](./requests/README.md).

---
