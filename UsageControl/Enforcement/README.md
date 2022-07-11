# IDS Policy Enforcement
## Usage Control Enforcement
At runtime, the usage control enforcement prevents IDS connectors from treating data in an undesired way, for example by forwarding personal data to public endpoints.

![image](https://user-images.githubusercontent.com/69632955/133073147-06aadf7a-d7ab-48ff-a81f-bd6678e15a29.png)

For enforcing usage restrictions, data flows need to be monitored and potentially intercepted by control points (i.e., PEPs). These intercepted data flows are given to the decision engine (i.e., the PDP) for requesting permission or denial of the data flow. In addition to just allowing or denying the data flow, the decision can also require a modification of data.

![uc-components](https://user-images.githubusercontent.com/69632955/133071484-307a2171-e110-4431-9ac4-9739af2aeb45.jpg)

More information: [Usage Control in the International Data Spaces](https://internationaldataspaces.org/data-sovereignty-updated-position-paper-on-data-usage-control-in-the-ids/)

**Enforcement Layers**

 - Connector/Container Layer
 - Route/Interceptor Layer
 - Application Layer
 
 ---
 
## Usage Control Enforcement via Route/Interceptor

**Use case:** The Data App is allowed to **use** (process, display, etc.) the data

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)

**Condition:** 
 - Data App inside a connector
 - UC app inside a Connector

![image](https://user-images.githubusercontent.com/69632955/133073748-5e302961-1387-4f21-9002-76dd7ad7bdef.png)

---

**Use case:** The Data App is allowed to **use** (process, display, etc.) and **store** the data

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)
 - Log usage information
 - Inform a Party about the usage
 - Setting up a Delete Data PXP

**Condition:** 
 - Data App inside a connector
 - Data sink inside a Connector
 - UC app inside a Connector

![image](https://user-images.githubusercontent.com/69632955/133073974-c8221409-f7e5-4e3b-8bc7-def86796beef.png)

---

**Use case:** The Data App is allowed to **use** (process, display, etc.) the data and **distribute** it outside the connector

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)
 - Log usage information
 - Inform a Party about the usage

**Condition:** 
 - Data App inside a connector
 - System Adapter inside a Connector
 - UC app inside a Connector

![image](https://user-images.githubusercontent.com/69632955/133074546-433d85fb-4dd7-4d2a-942d-699513368e4b.png)

---

## Usage Control Aware External Apps (http-based Web Services)

Characteristics:
 - Data format must be json compatible
 - Hooking points must be known and implemented

Advantages:
 - One UC service can be used by many Data Apps (reusable)
 - Easier Policy Management
---
**Use case:** The Data App is restricted to **use** (process, print, display and store) the data

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)
 - Log usage information
 - Inform a Party about the usage
 - Hook into the data flow (for fine-grained actions)
 - Modify in transit
 - Count usage

**Condition:** 
 - Data App inside a connector
 - UC app inside a Connector
 - JSON data exchange
 
 ![image](https://user-images.githubusercontent.com/69632955/133074885-5cb66b98-f13c-4b12-a680-1a9ff79d8e2a.png)
 
---

**Use case:** The Data App shall **read** and **store** the data via System Adapter App
 - System Adapter App encrypts and decrypts data that is stored/retrieved from a database outside a connector.

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)
 - Log usage information
 - Inform a Party about the usage
 - Hook into the data flow in the System Adapter App
 - Modify in transit
 - Setting up a Delete Data PXP

**Condition:** 
 - Data App inside a connector
 - System Adapter inside a Connector
 - UC app inside a Connector
 - Data Storage outside a Connector
 
![image](https://user-images.githubusercontent.com/69632955/133075313-f30dc826-e6ac-484e-a680-508d5df62c69.png)
 
 ---
 
## Usage Control Aware Internal Apps

Characteristics:
 - It can be MYDATA or any other implementation.
 - Data format depends on the implementation of the Usage Control technology
 - Hooking points must be known and implemented

Advantages:
 - More independent solution (wrt. Programming language, data format, etc.)

Disadvantages:
 - Higher implementation effort
 - Higher effort for Policy Management
---

**Use case:** The Data App is allowed to **use** (print and display) the data

**Usage Control tasks:** 
 - Check the conditions (Time interval, Data App, Events, etc.)
 - Log usage information
 - Inform a Party about the usage
 - Hook into the data flow (for fine-grained actions)
 - Modify in transit
 - Count Usage

**Condition:** 
 - Data App inside a connector
 - UC app inside a Data App

![image](https://user-images.githubusercontent.com/69632955/133076139-d939912d-9a66-4e42-a686-e1a64f5fea9f.png)

---
