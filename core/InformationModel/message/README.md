# ids:Message

Core `ids:Message` class with it's properties, which are equal for all messages. For communication in the IDS, use the specific message types.


## Properties

### securityToken

Token representing a claim, that the sender supports a certain security profile.


### Table of Message Properties

|**Property**|| cardinality |
|:---|:---|--|
|**[securotyProfile](#securityToken)**       | mandantory | 1
|||

## Message Types

The table contans descriptions for each message, information about the payload as well as changes in the properties, i.e., new properties and additional mandatory properties. "Message Class" and "Message Subclass" are just for structural / hierarchial description. The "Message Name" contains the actual Message which is used. New properties, which are mandatory are marked with an (*). Existing properties, which are mandatory for a specific message are listed in the corresponding column.
All mandatory property declarations of the core ids:Message above still hold. Optional properties are supported but not required.


### Request Messages

#### RequestMeasage

### Response Messages

##### ResponseMessage

Response messages hold information about the reaction of a recipient to a formerly sent command or event. They must be correlated to this message. 
May be used for messages, which are not covered by the core IDS messages.

### Notification Message

#### Connector Notification Message

##### ConnectorAvailableMessage

### Table of Message Types

|**Message Class**| | Message Name
|:---|:---|--|
|**[Request Messages](#requestmessages)**       || `RequestMessage`
|                                               || `CommandMessage`
|                                               || `InvokeOperationMessage`
|||

---
