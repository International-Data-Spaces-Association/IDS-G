# Contract Negotiation Protocol

## Introduction: Terms

This document outlines the key elements of the contract negotiation protocol. The following terms are used:

- A _**message type**_ defines the structure of a _message_.
- A _**message**_  is an instantiation of a _message type_.
- The _**contract negotiation protocol**_ is the set of allowable message type sequences and is defined as a state machine (CNP-SM).
- A _**contract negotiation (CN)**_ is an instantiation of the CNP-SM.
- A _**provider**_ is a participant agent that offers an asset.
- A _**consumer**_ is a participant agent that requests access to an offered asset.

## Contract Negotiation Protocol

A contract negotiation (CN) involves two parties, a _provider_ that offers one or more assets under a usage contract and _consumer_ that requests assets.
A CN is uniquly identified through an IRI. Each CN requires a newly generated IRI, which may not be used in a CN after a terminal state has been reached.
A CN progresses through a series of states, which are tracked by the provider and consumer using messages. A CN transitions to a state in response to an acknowledged message from
the counter-party. Both parties have the same state of the CN. In case the states differ, the CN is terminated and a new CN has to be initiated.

The CN states are:

- **CONSUMER_REQUESTED** - A contract for an asset has been requested by the consumer based on an offer and the provider has sent an ACK response.
- **PROVIDER_OFFERED** - The provider has sent a contract offer to the consumer and the consumer has sent an ACK response.
- **CONSUMER_AGREED** - The consumer has accepted the latest contract offer and the provider has sent an ACK response.
- **PROVIDER_AGREED** - The provider has accepted the latest contract offer, sent an agreement to the consumer, and the consumer has sent an ACK response.
- **CONSUMER_VERIFIED** - The consumer has sent an agreement verification to the provider and the provider has sent an ACK response.
- **PROVIDER_FINALIZED** - The provider has sent a finalization message including his own agreement verification to the consumer and the consumer has sent an ACK response. Data is
  now available to the consumer.
- **TERMINATED** - The provider or consumer has placed the contract negotiation in a terminated state. A termination message has been sent by either of the participants and the
  other has sent an ACK response. This is a terminal state.

### Contract Negotiation State Machine

The CN state machine is represented in the following diagram. Note that transitions to the TERMINATED state may occur from any other state and are not shown for simplicity:

![](./contract.negotiation.state.machine.png)

Transitions marked with `C` indicate a message sent by the consumer, transitions marked with `P` indicate a provider message. Terminal states are final; the state machine may
not transition to another state. A new CN may be initiated if, for instance, the CN entered the TERMINATED state due to a network issue.

## Message Types

The CN state machine is transitioned upon receipt and acknowledgement of a message. This section details those messages as abstract message types.

### Notes

- Concrete wire formats are defined by the protocol binding, e.g. HTTPS.
- The `OK` and `ERROR` response message types are empty body responses that are mapped onto a protocol such as HTTPS.
- All ODRL policy types (Offer, Agreement) must contain an ODRL UID that is a URI. GUIDs can also be used in the form of URNs, for instance following the pattern <urn:uuid:{GUID}>.
- The ODRL Agreement must have a target property containing the asset id.

### 1. ContractRequestMessage

**Sent by**: Consumer

**Resulting State**: CONSUMER_REQUESTED, TERMINATED

**Example**: [ContractRequestMessage](./message/contract.request.message.json)

**Response**: [ContractNegotiation](./message/contract.negotiation.json) containing the negotiation id or ERROR.

**Schema**: (xx)[]

#### Description

The _ContractRequestMessage_ is sent by a consumer to initiate a contract negotiation.

#### Notes

- The consumer must include either an `offer` or `offerId` property. If the message includes a `negotiationId` property, the request will be associated with an existing contract
  negotiation and a consumer offer will be created using either the `offer` or `offerId` properties. If the message does not include a `negotiationId`, a new contract negotiation
  will be created using either the `offer` or `offerId` properties.

- It is an error to include both an `offer` and `offerId` property.

- An `offerId` will generall refer to an offer contained in a catalog.

- The dataset id is not technically required but included to avoid an error where the offer is associated with a different data set.

- `callbackAddress` is a URI indicating where messages to the consumer should be sent. If the address is not understood, the provider MUST return an UNRECOVERABLE error.

### 2. ContractAgreementMessage

**Sent by**: Provider

**Resulting State**: PROVIDER_AGREED, TERMINATED

**Example**: [ContractAgreementMessage](./message/contract.agreement.message.json)

**Response**: OK or ERROR

**Schema**: (xx)[]

#### Description

The _ContractAgreementMessage_ is sent by a provider when it agrees to a contract. It contains the contract agreement with the provider's signature.

### 3. ContractAgreementVerificationMessage

**Sent by**: Consumer

**Resulting State**: CONSUMER_VERIFIED, TERMINATED

**Example**: [ContractAgreementVerificationMessage](./message/contract.agreement.verification.message.json)

**Response**: OK or ERROR

**Schema**: (xx)[]

#### Description

The _ContractAgreementVerificationMessage_ is sent by a consumer to verify the acceptance of a contract agreement. It contains the contract agreement with the consumer's signature.
A provider responds with an error if the signature can't be validated or is incorrect.

### 4. ContractNegotiationEventMessage

**Sent by**: Provider or Consumer

**Resulting State**: PROVIDER_FINALIZED, CONSUMER_AGREED, TERMINATED

**Example**: [ContractNegotiationEventMessage](./message/contract.negotiation.event.message.json)

**Response**: OK or ERROR

**Schema**: (xx)[]

#### Description

When the _ContractNegotiationEventMessage_ is sent by a provider with an `eventType` property set to `finalized`, a contract agreement has been finalized and the associated asset
is accessible. The state machine is transitioned to the PROVIDER_FINALIZED state. Other event types may be defined in the future. A consumer responds with an error if the signature
can't be validated or is incorrect.

It is an error for a consumer to send a ContractNegotiationEventMessage with an eventType `finalized` to the provider.

When the _ContractNegotiationEventMessage_ is sent by a consumer with an `eventType` set to  `accepted`, the state machine is placed in the CONSUMER_AGREED state.

It is an error for a provider to send a ContractNegotiationEventMessage with an eventType `accepted` to the consumer.

Note that contract events are not intended for propagation of agreement state after a contract negotiation has entered a terminal state. It is considered an error for a consumer or
provider to send a contract negotiation event after the negotiation state machine has entered a terminal state.

### 5. ContractNegotiationTerminationMessage

**Sent by**: Consumer or Provider

**Resulting State**: TERMINATED

**Example**: [ContractNegotiationTermination](./message/contract.negotiation.termination.message.json)

**Schema**: (xx)[]

#### Description

The _ContractNegotiationTermination_ is sent by a consumer or provider indicating it has cancelled the negotiation.

#### Notes

- A contract negotiation may be terminated for a variety of reasons, for example, an unrecoverable error was encountered or one of the parties no longer wishes to continue. A
  connector's operator may remove terminated contract negotiation resources after it has reached the terminated state.

- If an error is received in response to a ContractNegotiationTermination, the sending party may choose to ignore the error.

### 6. ContractNegotiationErrorMessage

**Example**: [NegotiationErrorMessage](./message/contract.negotiation.error.message.json)

**Schema**: (xx)[]

#### Description

The _ContractNegotiationErrorMessage_ is a response body returned by a consumer or provider indicating an error has occurred. It does not cause a state transition.

## Checksum Calculations

Checksums are calculated by creating the [[JWS/CT]](#references) of ...

## References

- [[JCS] JSON Canonicalization Scheme](https://www.ietf.org/archive/id/draft-jordan-jws-ct-08.html)
- [[JWS/CT] JWS Clear Text JSON Signature Option](https://www.ietf.org/archive/id/draft-jordan-jws-ct-08.html)
- [[JWS] JSON Web Signature](https://www.rfc-editor.org/rfc/rfc7797.html)

## JWS Clear Text JSON Signature Option

- Adopt JWS/CT, an extension to the JSON Web Signature (JWS) standard.
- Combines the detached mode of JWS with the JSON Canonicalization Scheme (JCS). Detached mode is when the payload section of the JWS is replaced
  by an empty string:  XXXX.PAYLOAD.YYYY becomes XXXX..YYYY. Detached mode is described in the JWS spec.
- Maintains Signed JSON data in JSON format.











