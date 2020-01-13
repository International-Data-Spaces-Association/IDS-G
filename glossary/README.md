# Glossary


## A

### App Store

Secure platform for distributing [Data App](#data-app)s; features different search options (e.g. by functional or non-functional properties, pricing model, certification status, community ratings, etc.).

### Applicant

Organization formally applying for being certified by the Certification Body.


## B

### Broker Service Provider

Intermediary managing a metadata repository that provides information about the [Data Source](#data-source)s available in the International Data Spaces; multiple Broker Service Providers may be around at the same time, maintaining references to different, domain-specific subsets of [Data Endpoint](#data-endpoint)s.

## C

### Certification Authority

Trusted third-party entity issuing digital certificates (e.g., x509 certificates); may host services to validate certificates issued.

### Certification Body

Governance body certifying components and entities seeking admission to the International Data Spaces; aside from having the final word on granting or denying a certificate, it is responsible for maintaining the Certification Scheme (including its catalog of requirements), overseeing and approval of Evaluation Facilities, and ensuring compatibility of evaluation procedures carried out by Evaluation Facilities.

### Certification Scheme

Scheme defining the processes, roles, targets, and criteria involved in the certification of components and entities; maintained by the Certification Body.

### Clearing House

Intermediary providing clearing and settlement services for all financial and data exchange transactions within the International Data Spaces.

### Connector

Dedicated communication server for sending and receiving data in compliance with the general Connector specification; different types of Connectors can be distinguished (Base Connector vs. Trusted Connector, or Internal Connector vs. External Connector).

### Connector-Self-description

Description of a [Connector](#connector) participating in the IDS for being read by other IDS Participants; created by the [Data Provider](#data-provider) or Data User as the first step of the [Connector](#connector) configuration process; contains information such as the name of the [Connector](#connector) provider or the name of the maintainer, as well as information about the content and type of the data offered or requested, about data communication interfaces, and about usage policies and contracts.

## D

### DAPS

See [Dynamic Attribute Provisioning Service](#dynamic-attribute-provisioning-service-daps).

### DAT

See [Dynamic Attribute Token](#dynamic-attribute-token-dat).

### Data App

Self-contained, self-descriptive software package that is distributed via the App Store and deployed inside a [Connector](#connector); provides access to data and data processing capabilities; the interface of a Data App is semantically described by the IDS Vocabulary.

### Data Asset

Content exposed for exchange via [Data Endpoint](#data-endpoint)s according to a parametrized Data Service interface; Data Assets are expected to be focused, homogeneous, and consistent over time with regard to granularity, coverage, context, data structure, and conceptual classification.

### Data Consumer

Core Participant in the International Data Spaces requesting and using data provided by a [Data Provider](#data-provider).

### Data Endpoint

Data interface for data publication ([Data Source](#data-source)) and data consumption (Data Sink), respectively.

### Data Exchange Agreement

Contractual agreement between a [Data Provider](#data-provider) and a Data Consumer regarding the exchange of data in the International Data Spaces.

### Data Operation

Method or operation with defined functionality to be invoked on a [Data Endpoint](#data-endpoint).

### Data Owner

Core Participant having complete control over the data it makes available in the International Data Spaces; defines the terms and conditions of use of its data.

### Data Provider

Core Participant exposing [Data Source](#data-source)s via a [Connector](#connector); a Data Provider may be an enterprise or other organization, a data marketplace, an individual, or a “smart thing”.

### Data Sink

[Data Endpoint](#data-endpoint) consuming data uploaded and offered by a [Data Provider](#data-provider).

### Data Source

[Data Endpoint](#data-endpoint) exposing data for being retrieved or subscribed to by a Data Consumer.

### Data Sovereignty

The capability of an entity (natural person or corporate) of being entirely self-determined with regard to its data.

### Demilitarized Zone (DMZ)

A Demilitarized Zone is an IT system (or a part of an IT system) with controlled access. 

### Dynamic Attribute Provisioning Service (DAPS)

Issues Dynamic Attribute Tokens ([DAT](#dynamic-attribute-token-dat)s) to verify dynamic attributes of Participants or [Connector](#connector)s.

### Dynamic Attribute Token (DAT)

Contains signed dynamic attributes for Participants and [Connector](#connector)s.

## E

### Evaluation Facility

Governance body providing services related to the certification of components and entities (certification targets) seeking admission to the International Data Spaces; responsible for detailed technical evaluation of targets in consistence with the Certification Scheme and its catalog of requirements; reports evaluation results to the Certification Body.

## G 

### Governance

Concept defining the rights and duties (“rules of the game”) for formal data management, ensuring quality and trust throughout the International Data Spaces; mission critical to the International Data Spaces, as a central supervisory authority is missing.

## I

### Identity Provider

Intermediary offering services to create, maintain, manage and validate identity information of and for Participants in the International Data Spaces.

### Information Model

Set of vocabularies and related schema information for the semantic description of International Data Spaces entities (e.g.,[Data Endpoint](#data-endpoint)s or [Data App](#data-app)s), data provenance, or licensing information; the core IDS Vocabulary is domain-independent; it can be extended and/or reference third-party vocabularies to express domain-specific aspects.

### IDS

See [International Data Spaces](#international-data-spaces): 

### International Data Spaces

Distributed network of [Data Endpoint](#data-endpoint)s (i.e., instantiations of the International Data Spaces [Connector](#connector)), allowing secure exchange of data and guaranteeing Data Sovereignty.

## P

### Participant

Stakeholder in the International Data Spaces, assuming one or more of the predefined roles; every Participant is given a unique identity by the Identity Provider.

## S

### Security Profile

Defined set of a [Connector](#connector)’s security properties; specifies several security aspects (e.g., isolation level, attestation, or authentication), expressing the minimum requirements a Data Consumer must meet to be granted access to the [Data Endpoint](#data-endpoint)s exposed.

### System Adapter

[Data App](#data-app) used for integration of custom [Data Source](#data-source)s and legacy systems with a [Connector](#connector).

## U

### Usage Contract

Set of rules and conditions regarding one or more transactions in the International Data Spaces.

### Usage Policy

Set of rules specified by the Data Owner restricting usage of its data; covers aspects like time-to-live or forwarding conditions (e.g., anonymization or scope of usage); transmitted along with the respective data, and enforced while residing on the [Connector](#connector) of the Data Consumer.

## V

### Vocabulary Hub

Server providing maintenance facilities for editing, browsing and downloading vocabularies and related documents; mirrors a set of external third-party vocabularies ensuring seamless availability and resolution.

---
