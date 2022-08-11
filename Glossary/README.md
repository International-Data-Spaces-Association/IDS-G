# Glossary


## A

### App Store
Secure platform for distributing [Data App](#data-app)s; features
 different search options (e.g. by functional or non-functional
 properties, pricing model, certification status, community
 ratings, etc.).

### Applicant
Organization formally applying for being certified by the
 [Certification Body](#certification-body).

### Authorization
The process of verifying whether a requesting party is allowed to 
access a resource or system.

## B

### Broker Service Provider
Intermediary managing a metadata repository that provides information
 about the [Data Source](#data-source)s available in the
 International Data Spaces; multiple Broker Service Providers may be
 around at the same time, maintaining references to different,
 domain-specific subsets of [Data Endpoint](#data-endpoint)s.
 
### Building Block
Building blocks are fundamental components that can be implemented and combined in order to achieve functional data spaces. A building block encompasses at least the basic specifications, and evolves in maturity with more detailed specifications, with sample implementations or templates, and with the instructions, training, testing and support elements that are required to facilitate the delivery of the essential ingredients of data spaces.


## C

### Certificate Authority
Trusted third-party entity issuing digital certificates
 (e.g., x509 certificates); may host services to validate certificates
 issued. (see [Identity Provider](../Components/IdentityProvider/README.md))

### Certification Body
Governance body certifying components and entities seeking admission to
 the International Data Spaces; aside from having the final word on
 granting or denying a certificate, it is responsible for maintaining
 the [Certification Scheme](#certification-scheme) (including its
 catalog of requirements), overseeing and approval of Evaluation
 Facilities, and ensuring compatibility of evaluation procedures carried
 out by Evaluation Facilities.

### Certification Scheme
Scheme defining the processes, roles, targets, and criteria involved in
 the certification of components and entities; maintained by the
 [Certification Body](#certification-body).
- [IDS Certification Scheme 2.0 (current version)](https://www.internationaldataspaces.org/wp-content/uploads/2020/01/IDSA-Strategy-paper-certification-scheme-V.2.pdf)

### Clearing House
Intermediary providing clearing and settlement services for all
 financial and data exchange transactions within the
 International Data Spaces.

- [IDS-G specification "Clearing House"](../Components/ClearingHouse/README.md)
- Shortcut: `IDS-CH`

### Connector
Dedicated communication server for sending and receiving data in
 compliance with the general Connector specification; different types
 of Connectors can be distinguished (Base Connector vs.
 Trusted Connector, or Internal Connector vs. External Connector).
- [IDS-G specification "Connector"](../Components/Connector/README.md)

### Connector-Self-description
Description of a [Connector](#connector) participating in the IDS for
 being read by other IDS [Participant](#participant)s; created by
 the[Data Provider](#data-provider) or Data User as the first step of
 the [Connector](#connector) configuration process; contains information
 such as the name of the [Connector](#connector) provider or the name of
 the maintainer, as well as information about the content and type of
 the data offered or requested, about data communication interfaces,
 and about usage policies and contracts.

## D

### DAPS
- IDS-G specification: [Dynamic Attribute Provisioning Service](../Components/IdentityProvider/DAPS/README.md)
- Glossary: [Dynamic Attribute Provisioning Service](#dynamic-attribute-provisioning-service).

### DAT
- See [Dynamic Attribute Token](#dynamic-attribute-token).

### Data App
Self-contained, self-descriptive software package that is distributed
 via the App Store and deployed inside a [Connector](#connector);
 provides access to data and data processing capabilities; the
 interface of a Data App is semantically described by the IDS Vocabulary.

### Data Asset
Content exposed for exchange via [Data Endpoint](#data-endpoint)s
 according to a parametrized Data Service interface; Data Assets are
 expected to be focused, homogeneous, and consistent over time with
 regard to granularity, coverage, context, data structure, and
 conceptual classification.

### Data Consumer
Core [Participant](#participant) in the International Data Spaces
 requesting and using data provided by a [Data Provider](#data-provider).

### Data Endpoint
Data interface for data publication ([Data Source](#data-source)) and
 data consumption (Data Sink), respectively.

### Data Exchange Agreement
Contractual agreement between a [Data Provider](#data-provider) and a
 [Data Consumer](#data-consumer) regarding the exchange of data in the
 International Data Spaces.

### Data Operation
Method or operation with defined functionality to be invoked on a
 [Data Endpoint](#data-endpoint).

### Data Owner
Core [Participant](#participant) having complete control over the data
 it makes available in the International Data Spaces; defines the terms
 and conditions of use of its data.

### Data Provider
Core [Participant](#participant) exposing [Data Source](#data-source)s
 via a [Connector](#connector); a Data Provider may be an enterprise or
 other organization, a data marketplace, an individual, or a
 “smart thing”.

### Data Sink
[Data Endpoint](#data-endpoint) consuming data uploaded and offered by
 a [Data Provider](#data-provider).

### Data Source
[Data Endpoint](#data-endpoint) exposing data for being retrieved or
 subscribed to by a [Data Consumer](#data-consumer).

### Data Sovereignty
The capability of an entity (natural person or corporate) of being
 entirely self-determined with regard to its data.

### Demilitarized Zone
A Demilitarized Zone is an IT system (or a part of an IT system) with
 controlled access.
- Shortcut: `DMZ`

### DMZ
- See [Demilitarized Zone](#demilitarized-zone).

### Dynamic Attribute Provisioning Service
Issues Dynamic Attribute Tokens ([DAT](#dynamic-attribute-token)s) to
 verify dynamic attributes of [Participant](#participant)s or
 [Connector](#connector)s.

- IDS-G specification [Dynamic Attribute Provisioning Service](../Components/IdentityProvider/DAPS/README.md)
- Shortcut: `DAPS`

### Dynamic Attribute Token
A JSON Web Token containing signed dynamic attributes for [Participant](#participant)s
and [Connector](#connector)s.
 - IDS-G specification: [Dynamic Attribute Token](../Components/IdentityProvider/DAPS#dynamic-attribute-token-dat)
- Shortcut: `DAT`

## E

### Evaluation Facility
Governance body providing services related to the certification of
 components and entities (certification targets) seeking admission to
 the International Data Spaces; responsible for detailed technical
 evaluation of targets in consistence with the
 [Certification Scheme](#certification-scheme) and its catalog of
 requirements; reports evaluation results to the
 [Certification Body](#certification-body).

## G

### Governance
Concept defining the rights and duties (“rules of the game”) for formal
 data management, ensuring quality and trust throughout the
 International Data Spaces; mission critical to the
 International Data Spaces, as a central supervisory authority is
 missing.
 
 ### Graduation Scheme
Structure that consists of rules, processes and evaluation criteria to assess the maturity level of an open source project.

## I

### Identity Provider
Intermediary offering services to create, maintain, manage and validate
 identity information of and for [Participant](#participant)s in the
 International Data Spaces.

### Identity Verification
The process of verifying the validity of a supplied identity proof.

### IDS
- See [International Data Spaces](#international-data-spaces).

### IDS Information Model
Set of vocabularies and related schema information for the semantic
 description of International Data Spaces entities (e.g.,
 [Data Endpoint](#data-endpoint)s or [Data App](#data-app)s), data
 provenance, or licensing information; the core IDS Vocabulary is
 domain-independent; it can be extended and/or reference third-party
 vocabularies to express domain-specific aspects.

See also:
- GitHub [repository](https://github.com/International-Data-Spaces-Association/InformationModel)
- IDS-G specification [Information Model](../Infomodel/README.md)
- Shortcut: `IDS-IM`

### IDS Communication Protocol
- IDS-G specification [IDSCP](../connector-interaction/Communication/protocols/idscp2/README.md) (current V.2)
- Shortcut: `IDSCP`

### IDS Deployment Scenario
IDS Deployment Scenario is any implementation made with IDS-compliant components that allows sovereign data sharing (as defined by IDSA), is built with the intention to resolve a problem by means of secure and sovereign data sharing and is adequately documented to enable others to follow the same path.

### IDSA
- Glossary [International Data Spaces Association](#international-data-spaces-association).

### IDS Graduation Scheme
Set of rules, processes and evaluation criteria to assess the maturity level of a IDS-related open source project. See [IDS Graduation Scheme](https://github.com/International-Data-Spaces-Association/idsa/tree/main/graduation_scheme)

### IDS Graduated
The projects that are in the third grade according to the criteria defined in [IDS Graduation Scheme](https://github.com/International-Data-Spaces-Association/idsa/tree/main/graduation_scheme)
See [projects page](https://github.com/International-Data-Spaces-Association/idsa/blob/main/graduation_scheme/Projects.md).

### IDS Incubating
The projects that are in the second grade according to the criteria defined in [IDS Graduation Scheme](https://github.com/International-Data-Spaces-Association/idsa/tree/main/graduation_scheme)
See [projects page](https://github.com/International-Data-Spaces-Association/idsa/blob/main/graduation_scheme/Projects.md).

### IDS Sandbox
The projects that are in the first grade according to the criteria defined in [IDS Graduation Scheme](https://github.com/International-Data-Spaces-Association/idsa/tree/main/graduation_scheme)
See [projects page](https://github.com/International-Data-Spaces-Association/idsa/blob/main/graduation_scheme/Projects.md).

### IDS Portfolio of Building Blocks
Is the catalog of building blocks that lists various components and frameworks that are developed according to IDS principles.

### IDSA-TSC
The IDSA Technical Steering Committee. 

### IDSA-Working Groups
The IDSA Working Groups.

### IDSCP
- Glossary [IDS Communication Protocol](#ids-communication-protocol).

### Information Model
- [Information Model](https://github.com/International-Data-Spaces-Association/IDS-G-pre/tree/connector-interaction/Infomodel)
The data model of the IDS. It defines all classes, attributes and entities known to the actors in the IDS.

### International Data Spaces
Distributed network of [Data Endpoint](#data-endpoint)s (i.e.,
 instantiations of the International Data Spaces
 [Connector](#connector)), allowing secure exchange of data and
 guaranteeing [Data Sovereignty](#data-sovereignty).
- Shortcut: `IDS`

### International Data Spaces Association
Association for the developement and maintenace of the [IDS-RAM](#ids-reference-architecture-model) and associated standards, see [Executive Summary](https://www.internationaldataspaces.org/publications/sharing-data-while-keeping-data-ownership-the-potential-of-ids-for-the-data-economy/) and [IDSA Homepage](https://www.internationaldataspaces.org).
- Shortcut: `IDSA`

### IDS Reference Architecture Model
Data Exchange and Data Sharing are essential for Data-Driven
 Business-Ecosystems, as well as the need for
 [Data Sovereignty](#data-sovereignty). The International Data Spaces
 Reference Architecture Model (IDS-RAM) defines fundamental concepts
 for [Data Sovereignty](#data-sovereignty), Data Sharing and Data
 Exchange. Focusing on the generalization of concepts, functionality,
 and overall processes involved in the creation of a secure
 “network of trusted data”, the IDS-RAM resides at a higher
 abstraction level than common architecture models of concrete software
 solutions do. The document provides an overview supplemented by
 dedicated architecture specifications defining the individual
 components of the International Data Spaces

The model is made up of five layers: The Business Layer specifies and
 categorizes the different roles which the [Participant](#participant)s
 of the International Data Space can assume, and it specifies the main
 activities and interactions connected with each of these roles. The
 Functional Layer defines the functional requirements of the
 International Data Spaces, plus the concrete features to be derived
 from these. The Process Layer specifies the interactions taking place
 between the different components of the International Data Spaces;
 it provides a dynamic view of the Reference Architecture Model. The
 Information Layer defines a conceptual model which makes use of
 linked-data principles for describing both the static and the dynamic
 aspects of the International Data Spaces’s constituents. The System
 Layer is concerned with the decomposition of the logical software
 components, considering aspects such as integration, configuration,
 deployment, and extensibility of these components.

In addition, the Reference Architecture Model comprises three
 perspectives that need to be implemented across all five layers:
 Security, Certification, and Governance. The Security Perspective
 defines the common security measures for the International Data Spaces
 and the concepts for Data Usage Control. The Certification Perspective
 describes the IDS [Certification Scheme](#certification-scheme) as a
 foundation for every interaction in the IDS. The Governance Perspective
 describes the Responsibilities of the Roles in the IDS.

- [IDS-RAM 3.0 (current version, PDF)](https://www.internationaldataspaces.org/wp-content/uploads/2019/03/IDS-Reference-Architecture-Model-3.0.pdf)

- Shortcut: `IDS-RAM`

## J

### JSON Web Token
- [rfc7523](https://tools.ietf.org/html/rfc7523)
- Shortcut: `JWT`

### JWT
- Glossary: [JSON Web Token](#json-web-token)

## M

### Meta Data Broker
- [IDS-G specification "Meta Data Broker"](../Component/MetaDataBroker/README.md)
- Shortcut: `IDS-MDB`

### Minimum Viable Data Space (MVDS)
A minimum viable data space (MVDS) is a combination of components to initiate a data space with just enough features to be usable for secure and sovereign data exchange.

## P

### ParIS
- Glossary [Participant Information Service](#participant-information-service)
- IDS-G [specification "ParIS"](../Components/IdentityProvider/ParIS/README.md)

### Participant
Stakeholder in the International Data Spaces, assuming one or more of
 the predefined roles; every Participant is given a unique identity by
 the [Identity Provider](#identity-provider).

### Participant Information Service

- [IDS-G specification "ParIS"](../Components/IdentityProvider/ParIS/README.md)
- Shortcut: `ParIS`


## S

### Security Profile
Defined set of a [Connector](#connector)’s security properties;
 specifies several security aspects (e.g., isolation level, attestation,
 or authentication), expressing the minimum requirements a
 [Data Consumer](#data-consumer) must meet to be granted access to the
 [Data Endpoint](#data-endpoint)s exposed.

### System Adapter
[Data App](#data-app) used for integration of custom
 [Data Source](#data-source)s and legacy systems with a
 [Connector](#connector).
 
 
## T

### Technical Due Diligence
see [Technical Due Diligence](https://github.com/International-Data-Spaces-Association/idsa/blob/main/graduation_scheme/due_diligence_reports/Due_Diligence_Project_Review_Template.md))

## U

### Usage Contract
Set of rules and conditions regarding one or more transactions in the
 International Data Spaces.

### Usage Control

### Usage Policy
Set of rules specified by the [Data Owner](#data-owner) restricting
 usage of its data; covers aspects like time-to-live or forwarding
 conditions (e.g., anonymization or scope of usage); transmitted along
 with the respective data, and enforced while residing on the
 [Connector](#connector) of the [Data Consumer](#data-consumer).

## V

### Vocabulary Hub
Server providing maintenance facilities for editing, browsing and
 downloading vocabularies and related documents; mirrors a set of
 external third-party vocabularies ensuring seamless availability
 and resolution.

