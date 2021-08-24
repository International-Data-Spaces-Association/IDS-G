# Removed Requirements
The following listed requirements have been introduced by version 1.0 of this document but needed to be removed in the further specification process. They are kept here for legacy reasons but have no normative meaning anymore.

| No. | Description |
| --- | --- |
| B2 | A broker can offer different services:<p> - Index service<p> - Clearing<p> - Marketplace |
| B9 | If multiple broker index services exist<p> a) index services are isolated and ignore others<p> b) index services from a network and propagate data to others<p> c) combinations from a) and b) are allowed |
| B19 | A Connector may only add or change his own metadata in the index service. The index service has to validate from the id token of the DataHeader, if the sender is authorized to request a specific action.<p> Attention: An Identity provider may use different keys than the International Data Spaces.<p>The index service therefore has to map IdP keys to IDS keys. |
| B22 | A BrokerQueryRequest contains exactly one scope:<p> a) All (query all active or passive datasets)<p> b) Active (query only active datasets)<p> c) Access (query only active datasets, which are available for the questioner) |
| B32 | An index service may also store metadata of data-apps. |
| B33 | The BaseBroker is based on the BaseConnector and Ubuntu with Docker. |
| B34 | The BaseBroker consists of this containers:<p> a) ExecutionCoreContainerwith ActiveMQ and Camel (App Store Image)<p> b) Broker-API(BaseBroker Image)<p> c) ApacheDS(DockerHub Image)<p> d) Keycloak (DockerHubImage) |
| B35 | ApacheDS loads default and additional IDS index service schema for RDF file storage. |
| B36 | Running Keycloak in the Connector is optional. A broker may use any Identity Provider. |
| B37 | Keycloak and index service do not use a shared LDAP.<p>Keycloak just syncs with LDAP sources and uses an internal RDBMS for storage. |
| B39 | Broker-API is a Spring Boot REST service, which parses incoming BrokerMessages and executed corresponding LDAP actions. The connection to LDAP is done with Spring LDAP (based on JNDI). |
| B41 | A camel workflow connects the incoming queue BrokerRequest with Broker-API. |
| B42 | The BaseBroker uses JMS Request/Response pattern for BrokerResponseMessage with a temporary queue. |
