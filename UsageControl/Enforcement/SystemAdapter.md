# System Adapter Technical Documentation
## Introduction

International Data Space System Adapter is an application developed in Java Spring that allows for storing sensitive data in a secure manner. Since International Data Space Ecosystem supports different connectors and micro-services, there is a need to have a possibility to securely store data outside the connectors. Therefore, System Adapter was developed.

System Adapter allows for different kind of databases to be registered. Currently, MySQL and Postgres are supported. There is a plan to add support for other database types in the future.

### Project strategy
System Adapter aims to research International Data Space data security and safety related to when data is securely stored outside its domain. By applying authentication, security and encryption principles, System Adapter aims to provide a secure environment for International Data Space users to store their sensitive data, while at same time, applying their usage policies.

## System Overview

System Adapter has a goal of providing a way of storing data securely outside of IDS ecosystem. To do that, we have designed System Adapter around three principles: authentication, authorization and encryption.

Authentication is handled by Spring Security Library. We use basic form authentication in order to access pages with sensitive data. Authorization is handled through permissions and roles designed inside Spring Application. Encryption is handled by RSA with public and private key. Private key is used for encryption whereas public key is used for decryption.

### Key terms and entitles
|**Term**| **Explanation** |
|--|--|
| System Adapter | A component that aims to provide capabilities to securely store data in different database providers. |
| Policy | A set of rules telling how sensitive data should be handled. |
| Java Spring | A framework developed in Java, suitable for Web Applications and APIs |
| Java Security | A library in Java Spring which handles all security related aspects of application. It contains also facilities for authentication. |
| Security | In the context of System Adapter, security permits specific users of entering or not entering guarded endpoints. |
| Authentication | In the context of System Adapter, authentication is a mechanism which assigns roles to users and checks their user credentials in order to provide access. |
| Encryption | In context of System Adapter, encryption is a mechanism used to encrypt sensitive data. |
| AES | AES is an encryption method. |
| RSA | RSA is an encryption method based on public/private key pair. |


## System Context & Domain
### Domain
The main idea of System Adapter is to allow users a secure way of storing data outside of IDS Ecosystem. To achieve this, we offer different databases with the possibility of encryption.

Users, by connecting to System Adapter through HTTP connection can conduct their transactions. System Adapter is made out:

![image](https://user-images.githubusercontent.com/69632955/159886685-009c3ce1-46ae-4297-850d-0301a70423d9.png)

The Front-End is handled using REACT bundled up with Material UI, whereas Back-End is handled by Java Spring. Connections to the database are handled by JPA.

One of the main objectives of IDS is to ensure data security. There we designed our security around these three elements:

![image](https://user-images.githubusercontent.com/69632955/159887061-d6cb7f80-c1bc-4bd7-b07b-4843a3f2f699.png)

## Architecture Drivers

### Key Functional Requirements

| # | Description |
|--|--|
| FR.01 | System Adapter should have a possibility to register Databases. |
| FR.02 | System Adapter should provide at least 2 different Databases. |
| FR.03 | System Adapter should provide different encryption techniques. |
| FR.04 | System Adapter should be able to serialize and deserialize data. |
| FR.05 | System Adapter should support registration of users. |
| FR.06 | System Adapter should support accessing data through user interface. |
| FR.07 | System Adapter should support header Id for data that is to be stored. |


### Quality Requirements

![image](https://user-images.githubusercontent.com/69632955/159888276-5cdef9f1-0017-40a9-be1a-c018ff9765b9.png)

![image](https://user-images.githubusercontent.com/69632955/159888385-18055e09-a107-410d-8b84-95ca6b5e8a2c.png)

![image](https://user-images.githubusercontent.com/69632955/159888829-7dd4a7e9-aff4-43a9-a7da-5da36fff78f4.png)

## System Decomposition

### Solution Approach and Key Architecture Decisions

![image](https://user-images.githubusercontent.com/69632955/159889055-63557ce7-81a9-4f54-b88d-2d129193cd72.png)

![image](https://user-images.githubusercontent.com/69632955/159889118-55270ea4-67ab-4931-b198-4abd0c5b4f6a.png)

![image](https://user-images.githubusercontent.com/69632955/159889193-0b17b728-e3a5-4a68-bd0c-6e41975b2c97.png)

![image](https://user-images.githubusercontent.com/69632955/159889303-50e84d1e-dd80-4245-aa52-fc76365abcc3.png)

![image](https://user-images.githubusercontent.com/69632955/159889386-1e7311ca-360e-4003-984b-53043c26cd60.png)

![image](https://user-images.githubusercontent.com/69632955/159889489-1cf43bef-569f-4176-8424-cd9899697a17.png)

![image](https://user-images.githubusercontent.com/69632955/159889556-3a7ad872-6a12-466d-bad2-f1012328c5b7.png)

![image](https://user-images.githubusercontent.com/69632955/159889637-4598f9f4-7e12-42f9-80bf-9171b45cf6a8.png)

![image](https://user-images.githubusercontent.com/69632955/159889678-92ca7c7f-663d-4815-a114-40fb9364687c.png)

![image](https://user-images.githubusercontent.com/69632955/159889727-fda417c5-6bc1-4bc5-a9b3-59fd0419bf1d.png)

![image](https://user-images.githubusercontent.com/69632955/159889766-3b4c94d3-12f1-4cea-9e1f-c1e17838612f.png)

![image](https://user-images.githubusercontent.com/69632955/159889812-0d2dd7f7-d2a8-4546-a9cf-515490bce38a.png)

![image](https://user-images.githubusercontent.com/69632955/159889852-32d0d0fb-5909-44a2-93a9-7ed3203956f2.png)

![image](https://user-images.githubusercontent.com/69632955/159889886-000831b1-9477-4894-a66b-756cb7ba6b0c.png)

![image](https://user-images.githubusercontent.com/69632955/159889929-1cdc6e0f-7e29-482b-9a71-a0f90b3462d8.png)

![image](https://user-images.githubusercontent.com/69632955/159890007-2c3f9cd6-76f1-4654-a86a-0e2c73e6694f.png)

![image](https://user-images.githubusercontent.com/69632955/159890122-71568c66-179e-4911-b812-86a3710ddb90.png)

### Data Model

In the scope of System Adapter we used the following models: User, Payload, Registration, and Role.

Our User Model is constructed to be compatible with JPA Repository Interface. User Model looks like this:

![image](https://user-images.githubusercontent.com/69632955/159890638-fc78107b-f557-49dc-81d3-27bc6807202b.png)

As we see, we have standard accessor methods for name, surname, email and password fields. As for the other fields, they are used by Spring Security system. Finally, registration and roles fields are used by other tables in form of foreign keys.

In the scope of Payload Model we have the following:

![image](https://user-images.githubusercontent.com/69632955/159890766-0e7792dd-c52c-47ac-b695-0af624460597.png)

Payload Model is used to handle sensitive data that is stored outside Fraunhofer Data Space. Most importantly, data field is created using a converter. This converter does the encryption and handling of the data.

In the scope of Registration Model we have the following:

![image](https://user-images.githubusercontent.com/69632955/159890855-3c06119e-c1e3-4c24-9d43-9aebb02d4629.png)

In the scope of Role Model we have the following:

![image](https://user-images.githubusercontent.com/69632955/159890897-8ce60578-45d6-4847-bad6-6fc08ced52b4.png)

This model support Role Based Authorization system that we build. Therefore, for each registered user there is a one or more corresponding entries in role table.

### Code Organization

Back-End code was organized in packages, whereas Front-End code was organized in React classes. As far as Back-End code is concerned, we defined following packages:

![image](https://user-images.githubusercontent.com/69632955/159891022-2eed0c93-85ef-447b-ac67-88f206c6b79f.png)

| Package | Explanation |
|--|--|
| System Adapter | Contains main method. Here, we bootstrap our application, fill in the database with dummy data, create user, and create routes which are exposed to React Front-End. |
| Configuration Database | Classes where developers can register new database providers.  |
| Configuration Security | Classes where developers can specify protection od routes, choose type of security, add CORS mapping, etc. |
| Configuration Security Auth | Classes where developers implement User Details interface in order to communicate with Configuration Security by providing DAO Authentication Provider with login data.  |
| Configuration Security Role | Enums where developers define user roles and their permissions. |
| Data | Container for data that is to be stored safely. |
| Data Database Type | Enum for registering different database providers. |
| Data Formatter | Interface containing function signature for formatting data. |
| Database Dummy | Classes for creating dummy data for Databases and Users. |
| Encryption | Classes implementing different encryption protocols that we described above. |
| Form RequestBody | Classes containing request bodies for different access end-points. |
| Form RequestResponse | Classes containing request response bodies for different access end-points. |
| Form User | Classes in form of Rest Controllers containing access points related to operations of registered users. |
| Hashing | Class containing configuration for BCrypt password encoder.  |
| Model MySQL | Contains data models for MySQL database provider. |
| Model Postgres | Contains data models for Postgres database provider. |
| Repository MySQL | Contains JPA Information for MySQL database provider. |
| Repository Postgres | Contains JPA Information for Postgres database provider. |
| Rest | Classes in form of Rest Controllers containing access points related to Database providers. |
| Service MySQL | Classes containing services for MySQL Database provider. |
| Service Postgres | Classes containing services for Postgres Database provider. |
| Validation Annotations | Annotations for Capital Letter check, Not Blank check, and Size check. |
| Validation Validator | Classes containing implementations for annotations |

As far as Front-End is concerned, we defined following classes:

![image](https://user-images.githubusercontent.com/69632955/159892193-56cca37b-4ab2-4cdd-a92b-270f1622b163.png)

| Folder | Explanation |
|--|--|
| App | This folder contains main files from React application responsible for starting the application. |
| FormRequestBodyObjects | This folder contains files that prepare request to access web points in the Back-End. |
| Header | This folder contains information about navigating pages and access links. |
| Page | This folder contains all pages that are rendered by React UI.   |
| PageContainer | This folder contains logic behind changing the contents of the pages. |
| Images | This folder resources used by pages, such as images. |

### Security

Authentication is handled completely by Spring Security package. Spring Security package brings the infrastructure for handling different forms of Authentication. Some of the Authentication forms that Spring Security supports are: Basic, Login Form, OAuth, etc. In our case, we are using Basic Authentication. In the beginning, we secured our routes with these restrictions:

![image](https://user-images.githubusercontent.com/69632955/159892578-04e6a3e8-df01-4565-b8d4-97531835702c.png)

In the beginning, we disable csrf protection and make sure that for the requests that are presented inside antMatchers method to require authentication. Of course, we permit all users to access some content with permitAll method. After this step, we also allow CORS requests in this part:

![image](https://user-images.githubusercontent.com/69632955/159892628-3baeaa60-8e4d-4322-af3f-71eaf2945364.png)

We allow CORS Mappings by overriding addCorsMappings method and choosing which routes we want to map. In our case we allow for all.

After fixing the route, we defined UserAuthApplicationWrapper and UserAuthServiceWrapper in order to assist us to authenticate users. The UserAuthApplicationWrapper contains the following:

![image](https://user-images.githubusercontent.com/69632955/159892674-6eb2f26c-98e5-4db0-a432-635065d7a66c.png)

We see that this is very similar to the User Model. However, the differences lie on the way UID and Roles are organized. Furthermore, this class implements UserDetails interface which is than consumed by Spring Security's DaoAuthenticationProvider class.

By providing all of this information, Spring Security can perform authentication. This way, are routes are secured and only authenticated users can access sensitive data.

Authorization is handled by Spring Security Package. In our case, we revolved our authorization in form of user roles and permissions. We defined user permissions like this:

![image](https://user-images.githubusercontent.com/69632955/159892721-9897d74e-7028-4351-aaa0-721f79058f57.png)

As we can see, we have in total 6 permissions. Their access methods were defined inside ApplicationUserPermission class. This way we can use permissions to allow or disallow certain actions. To embedd these decisions, we created user roles. The roles that we devised are:

![image](https://user-images.githubusercontent.com/69632955/159892761-80425bf2-2d88-4553-806e-ad7a9095a3e4.png)

We defined three user roles in total. The routes that they can access are expressed with antMatchers function in scope of Authentication.

Since we are storing sensitive data, we decided to use Encryption as a measure to safely store data. We offer different forms of encryption data. In total we offer:

1.	AES Encryption
2.	AES Encryption Values
3.	AES Encryption Values Secret Key
4.	RSA Encryption

In terms of security, AES encryption is the least secure, whereas RSA Encryption is the most secure. We implemented all of these mechanisms using Converter Interface and Annotation from Java Spring.

| Converter | Explanation |
|--|--|
| AES | AES Encryption is done by defining two instances of Cipher: one for encrypting, one for decrypting. In the beginning we convert the data into Base64 format and then feed it to encrypt Cipher. After this step we save the data to database. As far as decrypting is concerned, the data we received is decoded using Base64 decoder and then decrypted using decrypt Cipher. |
| AES Values | AES Encryption is done by defining two instances of Cipher: one for encrypting, one for decrypting. In the beginning we convert the data into Base64 format and then feed it to encrypt Cipher. We only encrypt the values part, without encrypting the keys part. After this step we save the data to database. As far as decrypting is concerned, the data we received is decoded using Base64 decoder, then decrypted using decrypt Cipher and finally keys are added. |
| AES Values Secret Key | AES Encryption is done by defining two instances of Cipher: one for encrypting, one for decrypting. In the beginning we convert the data into Base64 format and than feed it to encrypt Cipher. Before encryption itself takes place , we define a random generated secret key, which is fed to the Cipher. We only encrypt the values part, without encrypting the keys part of the data. After this step we save the data to database. As far as decrypting is concerned, the data we received is decoded using Base64 decoder, than decrypted using decrypt Cipher and randomly generated secret key. |
| RSA | AES Encryption is done by defining two instances of Cipher: one for encrypting, one for decrypting. In the beginning we convert the data into Base64 format and than feed it to encrypt Cipher. Before encryption itself takes place , we define a random generated secret key using RSA, which is fed to the Cipher. We only encrypt the values part, without encrypting the keys part of the data. After this step we save the data to database. As far as decrypting is concerned, the data we received is decoded using Base64 decoder, than decrypted using decrypt Cipher and randomly generated secret key. |

### User Interface

User Interface was built by using Material UI, a library written specifically to be used in React. When we navigate to root page we get to see this:

![image](https://user-images.githubusercontent.com/69632955/159893237-7990774e-4bcc-4390-b3b2-a50d59db9139.png)

On top we have header with navigation links, whereas the content is below. All pages share this similar structure. As far as Documentation is concerned, it looks like this:

![image](https://user-images.githubusercontent.com/69632955/159893462-2c4cad3b-aa05-4c4c-b95f-9de5d258e4a6.png)

As far as pages with restrictions are concerned, they look like this:

![image](https://user-images.githubusercontent.com/69632955/159893619-580fd592-bf76-40ee-a891-297d60bdb4f0.png)

### Deployment and Operation

The System Adapter app is a Java Spring Application. Therefore, we use available infrastructure in order to deploy our application. The Back-End is deployed on TomCat Server. The Front-End is deployed using React facilities. Database is deployed using MySQL and Postgres facilities. All of these components are wrapped around in Docker containers. 


## Authors

| | | |
|:---|:---|:---|
| Arian Ajdari                   | Mail         | [arian.ajdari@iese.fraunhofer.de](mailto:arian.ajdari@iese.fraunhofer.de)  |
| Robin Brandstädter             | Mail         | [robin.brandstaedter@iese.fraunhofer.de](mailto:robin.brandstaedter@iese.fraunhofer.de)  |
| Arghavan Hosseinzadeh          | Mail         | [arghavan.hosseinzadeh@iese.fraunhofer.de](mailto:arghavan.hosseinzadeh@iese.fraunhofer.de)  |
| | | |
