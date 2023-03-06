## Specification of Policy Classes in ODRL

In this section, we present the Policy Classes specified in ODRL. The domain-specific elements are referenced by adding the URI of their profiles in the *@Context* part of the policy. Therefore, in the sample policies below, there is a reference to the Information Model of IDS to cover the IDS specific terminology. 

**1. Restrict the usage of the data to a specific Data Consumer**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-data-consumer/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the usage of the data to a specific Data Consumer, regardless of how many connectors they have and without any further usage restriction.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": ["use", "distribute"]
    }]
}
```

**2. Restrict the data usage to specific connectors**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-connector/312",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Connector-restricted Data Usage policy restricts the usage of the data to a specific IDS connector of a specific Data Consumer assuming that the Data Consumer owns one or more IDS Connector(s).",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:CONNECTOR",
           "operator": "isAnyOf",
           "rightOperand":  [
              { "@value": "?connector1URI", "@type": "xsd:anyURI" },
              { "@value": "?connector2URI", "@type": "xsd:anyURI" }
           ]
       }]
    }]
}
```

**3. Restrict the data usage to a group of systems or applications**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-application/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Application-restricted Data Usage policy restricts the usage of the data to a specific application inside an IDS connector.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "execute", "display" ],
        "constraint": [{
           "leftOperand": "idsc:APPLICATION",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "?DataAppURI", "@type": "xsd:anyURI" },
              { "@value": "?DataAppURI", "@type": "xsd:anyURI" }
           ]
       }]
    }]
}
```

**4. Restrict the data usage to a group of users**

Users shall be defined properly.

```json
{
   "@type": "vcard:Individual",
   "@id": "http://example.com/person/sara",
   "vcard:fn": "Sara",
   "vcard:hasEmail": "sara@example.com",
   "odrl:partOf": "http://example.com/ids/party/4565",
   "vcard:hasRole": "Team leader"
}

{
   "@type": "vcard:Individual",
   "@id": "http://example.com/person/murphy",
   "vcard:fn": "Murphy",
   "vcard:hasEmail": "murphy@example.com",
   "odrl:partOf": "http://example.com/ids/party/4565",
   "vcard:hasRole": "Backend Developer"
}

{
   "@type": "vcard:Individual",
   "@id": "http://example.com/person/John",
   "vcard:fn": "John",
   "vcard:hasEmail": "john@example.com",
   "odrl:partOf": "http://example.com/ids/party/4565",
   "vcard:hasRole": "UI Designer"
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-users/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the usage of the data to a specific users (e.g. users with specific roles or users who are members of a specific organization, etc.).",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": {
           "@type": [ "Party", "vcard:Individual" ],
           "uid": "http://example.com/ids/party/1234",
        },
        "assignee": {
           "@type": [ "PartyCollection", "vcard:Group" ],
           "source": "http://example.com/ids/party/4565",
           "refinement": [{
              "leftOperand": "vcard:hasRole",
              "operator": "isPartOf",
              "rightOperand": [
                 { "@value": "Team leader", "@type": "xsd:string" },
                 { "@value": "Backend Developer", "@type": "xsd:string" }
              ]
           }]
        },
        "action": [ "execute", "display" ]
    }]
}
```

**5. Restrict the data usage to specific locations**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-location/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Location-restricted Data Usage policy restricts the usage of the data to a specific location. It may be expressed as names of areas or set of geographic points.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "spatial",
           "operator": "eq",
           "rightOperand": { "@value": "http://ontologi.es/place/DE", "@type": "xsd:anyURI" }
       }]
    }]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-location/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Location-restricted Data Usage policy restricts the usage of the data to a specific location. It may be expressed as names of areas or set of geographic points.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "spatial",
           "operator": "eq",
           "rightOperandReference": { "@value": "?referenceToAllowedLocations", "@type": "xsd:anyURI" }
       }]
    }]
}
```

**6. Restrict the data usage for specific purposes**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-by-purpose/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Purpose-restricted Data Usage policy restricts the usage of the data to specific purposes such as marketing, research, Defect Analysis, etc.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "constraint": [{
           "leftOperand": "purpose",
           "operator": "eq",
           "rightOperand": { "@value": "Research", "@type": "xsd:string" }
       }]
    }]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-by-purpose/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Purpose-restricted Data Usage policy restricts the usage of the data to specific purposes such as marketing, research, Defect Analysis, etc.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "constraint": [{
           "leftOperand": "purpose",
           "operator": "isPartOf",
           "rightOperand": [{ "@value": "Educational Use", "@type": "xsd:string" },
                            { "@value": "Risk Management", "@type": "xsd:string" },
                            { "@value": "Defect Analysis", "@type": "xsd:string" }]
       }]
    }]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-by-purpose/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Purpose-restricted Data Usage policy restricts the usage of the data to specific purposes such as marketing, research, Defect Analysis, etc.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "constraint": [{
           "leftOperand": "purpose",
           "operator": "eq",
           "rightOperandReference": { "@value": "?referenceToAllowedPurposes", "@type": "xsd:anyURI" }
       }]
    }]
}
```

**7. Restrict the data usage when a specific event has occurred**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-on-event/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Event-restricted Data Usage policy restricts the usage of the data to specific events such as Hannover Messe 2022.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "constraint": [{
           "leftOperand": "event",
           "operator": "eq",
           "rightOperand": { "@value": "Hannover Messe 2022", "@type": "xsd:string" }
       }]
    }]
}
```

**8. Restrict the data usage to the security level of the connectors**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-security-level/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Security Level-restricted Data Usage policy restricts the usage of the data to a Specific IDS Connector when it is certified for a specified security level (i.e. idsc:BASE_SECURITY_PROFILE, idsc:TRUST_SECURITY_PROFILE and idsc:TRUST_PLUS_SECURITY_PROFILE).",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:SECURITY_LEVEL",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", "@type": "xsd:string" },
              { "@value": "idsc:TRUST_SECURITY_PROFILE", "@type": "xsd:string" }
           ]
       }]
    }]
}
```

**9. Restrict the data usage to a specific time interval**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-time-interval/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The time interval-restricted Data Usage policy restricts the usage of the data to a specific time interval.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "dateTime",
           "operator": "gteq",
           "rightOperand": { "@value": "2022-06-01T08:00Z", "@type": "xsd:dateTimeStamp" }
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand": { "@value": "2022-10-01T08:00Z", "@type": "xsd:dateTimeStamp" }
       }]
    }]
}
```

**10. Restrict the data usage to a specific time duration**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-duration/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Duration-restricted Data Usage policy restricts the usage of the data to a specific period.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "elapsedTime",
           "operator": "eq",
           "rightOperand": { "@value": "P3M", "@type": "xsd:duration" }
       }]
    }]
}
```

**11. Use the data not more than N times**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-on-count-usage/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy may be specified as: the Data Consumer is allowed to use my data not more than n times",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "count",
           "operator": "lteq",
           "rightOperand": { "@value": "10", "@type": "xsd:integer" }
       }]
    }]
}
```

**12. Use data and delete it after**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/delete-data/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to delete data either immediately after it is used, or after a delay period or before a defined deadline.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "delete" },
              "refinement": [{
                 "leftOperand": "delayPeriod",
                 "operator": "eq",
                 "rightOperand": { "@value": "P1M", "@type": "xsd:duration" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**13. Modify data (in transit)**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/modify-in-transit/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to intercept the data flow and modify the data in transit.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:REPLACE" },
              "includedIn": "anonymize",
              "refinement": [{
                 "leftOperand": "idsc:REPLACE_WITH",
                 "operator": "eq",
                 "rightOperand": { "@value": "xxxx", "@type": "xsd:string" }
              },{
                 "leftOperand": "idsc:JSON_PATH",
                 "operator": "eq",
                 "rightOperand": { "@value": "$.name", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "lt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**14. Modify data (in rest)**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/modify-in-rest/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to modify the data that is located in a repository",
    "dc:issued": "2022-05-19T12:00",
    "obligation": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [{
           "rdf:value": { "@id": "anonymize" }
        }]
    }]
}
```

**15. Log the data usage information**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/log-usage-information/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to log the Data Usage information either locally or on the Clearing House.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:log" },
              "includedIn": "inform",
              "refinement": [{
                 "leftOperand": "systemDevice",
                 "operator": "eq",
                 "rightOperand": { "@value": "?loggingServer", "@type": "xsd:anyURI" }
              },{
                 "leftOperand": "idsc:LOG_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**16. Notify a party or a specific group of users about the data usage information**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/inform-about-usage-information/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to inform a participant (e.g. person, organization), or even the Clearing House about the Data Usage. For example, inform a party via Email or a notification system.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "inform" },
              "refinement": [{
                 "leftOperand": "idsc:NOTIFICATION_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**17. Attach policy when distribute the data to a third-party**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/next-policy/22",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to pass a policy to a third-party before distributing the data to them.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "distribute",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "nextPolicy" }
           }],
           "target": "http://example.com/policy/modify-in-transit/42"
        }]
    }]
}
```

**18. Restrict the data usage to specific artifact states**

For example, *distribute the data only if it is encrypted*.

The artifact states shall be properly defined.

```json
{
   "@type": "dc:Dataset",
   "@id": "http://example.com/asset:111.json",
   "dc:title": "production plan",
   "dc:fileFormat": "application/json",
   "odrl:partOf": "http://example.com/ids/data/789",
   "ids:artifact-state": ["idsc:encrypted", "idsc:combined", "idsc:anonymized"]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-artifact-state/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The Artifact state-restricted Data Usage policy restricts that the data is used (or distributed) when it is encrypted, anonymized, combined, etc.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": {
           "@type": "AssetCollection",
           "source":  "http://example.com/ids/data/789",
           "refinement": [{
              "leftOperand": "ids:artifact-state",
              "operator": "isAllOf",
              "rightOperand": [
                 { "@value": "idsc:encrypted", "@type": "xsd:string" },
                 { "@value": "idsc:anonymized", "@type": "xsd:string" }
              ]
           }]
        },
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "distribute"
    }]
}
```

**19. Perpetual data sale restrictions**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-on-payment/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "In this policy it is assumed that the payment has been done once. The concept for the data ownership and authority is still open.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:compensate" },
              "refinement": [{
                 "leftOperand": "payAmount",
                 "operator": "eq",
                 "rightOperand": { "@value": "500.00", "@type": "xsd:decimal" },
                 "unit": "http://dbpedia.org/resource/Euro"
              }]
            }],
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

**20. Rental data restrictions**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-on-payment/42",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "In this policy it is assumed that the payment shall happen on a regular basis (e.g. monthly).",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "read", "use" ],
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:compensate" },
              "refinement": [{
                 "leftOperand": "payAmount",
                 "operator": "eq",
                 "rightOperand": { "@value": "500.00", "@type": "xsd:decimal" },
                 "unit": "http://dbpedia.org/resource/Euro",
                 "dc:frequency": {"@id":"freq:annual" }
              }]
            }],
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

**21. Restrict the data usage to specific state**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-on-state/312",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "The State-restricted Data Usage policy restricts the usage of the data to specific environment state (e.g. emergency).",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "constraint": [{
           "leftOperand": "idsc:state",
           "operator": "eq",
           "rightOperand":  [{ "@value": "emergency", "@type": "xsd:string" }]
       }]
    }]
}
```

## Extension of Policy Classes

**22. Restrict the data usage to the location of the participant**

The location of Participants shall be properly defined.

```json
{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org1",
   "vcard:fn": "org 1",
   "vcard:hasEmail": "org1@example.com",
   "odrl:partOf": "http://example.com/ids/party/456456",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-participants-by-location/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the usage of the data to the location of the Data Consumer party.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": {
           "@type": [ "PartyCollection", "vcard:Organization" ],
           "uid": "http://example.com/ids/party/123123",
        },
        "assignee": {
           "@type": [ "PartyCollection", "vcard:Group" ],
           "source": "http://example.com/ids/party/456456",
           "refinement": [{
              "leftOperand": "vcard:hasGeo",
              "operator": "eq",
              "rightOperand": [{ "@value": "http://ontologi.es/place/DE", "@type": "xsd:anyURI" }]
           }]
        },
        "action": "use"
    }]
}
```

**23. Restrict the data usage to the user consent**

The Participants and users shall be propertly defined.

```json
{
   "@type": "vcard:Individual",
   "@id": "http://example.com/person/sara",
   "vcard:fn": "Sara",
   "vcard:hasEmail": "sara@example.com"
}

{
   "@type": "dc:Dataset",
   "@id": "http://example.com/asset:sara-data.json",
   "dc:title": "Sara's health data",
   "dc:fileFormat": "application/json",
   "odrl:partOf": "http://example.com/ids/data/health-data-collection1"
}

{
   "@type": "vcard:Individual",
   "@id": "http://example.com/ids/party/john",
   "vcard:fn": "John",
   "vcard:hasEmail": "john@example.com"
}

{
   "@type": "dc:Dataset",
   "@id": "http://example.com/asset:john-data.json",
   "dc:title": "John's health data",
   "dc:fileFormat": "application/json",
   "odrl:partOf": "http://example.com/ids/data/health-data-collection1"
}

{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org1",
   "vcard:fn": "org 1",
   "vcard:hasEmail": "org1@example.com",
   "odrl:partOf": "http://example.com/ids/party/dataprovider1",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/obtain-consent/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to obtain consent from the Data Owner before using the data.",
    "dc:issued": "2022-05-25T12:00",
    "permission": [{
        "target": "http://example.com/asset:sara-data.json",
        "assigner": "http://example.com/ids/party/dataprovider1",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:obtainConsent" }
            }],
            "consentingParty": ["http://example.com/person/sara"]
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

We may need consents from more than one Participant or user.

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/obtain-consent/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "this policy restricts the usage of the data to obtaining consent from the Data Provider/Owner.",
    "dc:issued": "2022-05-25T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/health-data-collection1",
        "assigner": "http://example.com/ids/party/dataprovider1",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:obtainConsent" }
            }],
            "consentingParty": ["http://example.com/person/sara","http://example.com/ids/party/john"]
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

## Usage Control Policy

One can instantiate and combine a set of the above mentioned Policy Classes and build a Usage Control policy. In order to specify a policy in ODRL language, the following template can be used.

**ODRL Policy Template without IDS Information Model referenced in the *@context*:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/"}
    ],
    "@type": "Set",
    "uid": " ",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": " ",
    "dc:description": " ",
    "dc:issued": " ",
    "permission": [{
        "target": " ",
        "assigner": " ",
        "assignee": " ",
        "action": [" ", " "],
        "constraint": [{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }]
       },{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }]
       }],
         "duty": [{
           "action": [" "],
           "constraint": [{
               "leftOperand": "event",
               "operator": " ",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**ODRL Policy Template with IDS Information Model referenced in the *@context*:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Set",
    "uid": " ",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": " ",
    "dc:description": " ",
    "dc:issued": " ",
    "ids:provider": " ",
    "ids:consumer": " ",
    "permission": [{
        "target": " ",
        "assigner": " ",
        "assignee": " ",
        "action": [" ", " "],
        "constraint": [{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
       }],
         "duty": [{
           "action": [" "],
           "constraint": [{
               "leftOperand": "event",
               "operator": " ",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
        }]
    }]
}

```

**Sample ODRL Usage Control policy:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/"}
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-security-level/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restrict the usage of the data (perform derive and display actions) to the Security Levels of the Consumer Connectors, purposes and a time interval. In addition, it demands to log the usage information and delete the data after a delay period of one month.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:SECURITY_LEVEL",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", "@type": "xsd:string" },
              { "@value": "idsc:TRUST_SECURITY_PROFILE", "@type": "xsd:string" }
           ]
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand": { "@value": "2022-10-01T08:00Z", "@type": "xsd:dateTimeStamp" }
       },{
           "leftOperand": "purpose",
           "operator": "isPartOf",
           "rightOperand": [{ "@value": "Educational Use", "@type": "xsd:string" },
                            { "@value": "Risk Management", "@type": "xsd:string" },
                            { "@value": "Defect Analysis", "@type": "xsd:string" }]
       }],
         "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:log" },
              "includedIn": "inform",
              "refinement": [{
                 "leftOperand": "systemDevice",
                 "operator": "eq",
                 "rightOperand": { "@value": "?loggingServer", "@type": "xsd:anyURI" }
              },{
                 "leftOperand": "idsc:LOG_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        },{
           "action": [{
              "rdf:value": { "@id": "delete" },
              "refinement": [{
                 "leftOperand": "delayPeriod",
                 "operator": "eq",
                 "rightOperand": { "@value": "P1M", "@type": "xsd:duration" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**Sample ODRL Usage Control policy with IDS Information Model referenced in the *@context*:**

Including the IDS Information Model in the Context, we can additionally represent the PIP and PXP interfaces in the policy.

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-security-level/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restrict the usage of the data (perform derive and display actions) to the Security Levels of the Consumer Connectors, purposes and a time interval. In addition, it demands to log the usage information and delete the data after a delay period of one month.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "ids:consumer": "http://example.com/ids/party/456",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:SECURITY_LEVEL",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", "@type": "xsd:string" },
              { "@value": "idsc:TRUST_SECURITY_PROFILE", "@type": "xsd:string" }
           ],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand": { "@value": "2022-10-01T08:00Z", "@type": "xsd:dateTimeStamp" },
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": "purpose",
           "operator": "isPartOf",
           "rightOperand": [{ "@value": "Educational Use", "@type": "xsd:string" },
                            { "@value": "Risk Management", "@type": "xsd:string" },
                            { "@value": "Defect Analysis", "@type": "xsd:string" }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       }],
         "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:log" },
              "includedIn": "inform",
              "refinement": [{
                 "leftOperand": "systemDevice",
                 "operator": "eq",
                 "rightOperand": { "@value": "?loggingServer", "@type": "xsd:anyURI" }
              },{
                 "leftOperand": "idsc:LOG_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
        },{
           "action": [{
              "rdf:value": { "@id": "delete" },
              "refinement": [{
                 "leftOperand": "delayPeriod",
                 "operator": "eq",
                 "rightOperand": { "@value": "P1M", "@type": "xsd:duration" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
        }]
    }]
}
```
