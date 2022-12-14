# Trust Frameworks #

## Introduction ##

The Trust Framework for IDS is described in the [IDS-RAM Security Perspective](https://docs.internationaldataspaces.org/ids-ram-4/perspectives-of-the-reference-architecture-model/4_perspectives/4_1_security_perspective).

It focusses on:

- Identity and Trust Management
- Securing the platform (out of scope for Communication)
- Securing Applications (out of scope for Communication)
- Securing interaction between connectors

The Trust Framework is not part of the communication guide, but part of the IDSA Rulebook and IDS-RAM. (plus some sentences)

## Trust and security requirements for communication in IDS context
- A connector must be able to validate the identity of its communcation partner
- To initiate communication, a connector must be able to retrieve a Dynamic Attribute Token (DAT) issued by a Dynamic Attribute Provisioning Service (DAPS) and present it to its communication partner
- A connector must be able to validate a DAT presented by its communication partner
- Further requirements can be found in https://github.com/International-Data-Spaces-Association/IDS-G/tree/63-communication-guide-identities
- Communication must be performed encrypted between authenticated entities 
- The transport certificate must be referenced in the DAT, preventing token spoofing
- Remote attestation should be supported if trust anchors are used such as TPM, SGX enclaves and AMD SEV VMs

The trust level is defined by all components involded in the communication. 

## Messages and data types ##

## protocols: state machines for message flows and interaction patterns ##


## API binding ##
