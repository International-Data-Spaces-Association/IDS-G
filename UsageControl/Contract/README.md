# IDS Usage Control Contract
## Usage Control Terms in IDS Contracts
An IDS Contract is *implicitly* divided to two main sections: the contract specific metadata and the *IDS Usage Control Policy* of the contract.

![uc_terms](https://user-images.githubusercontent.com/69632955/123940369-66a14200-d999-11eb-94ee-195c0ba21f1c.png)

The *IDS Usage Control Policy* is the key motive of organizational and technical Usage Control enforcement and contains several Data Usage Control statements (e.g., permissions, prohibitions and obligations) called *IDS Rules* and is specified in the *IDS Usage Control Language* which is the IDS technology independent language.
## Usage Policy Specification
![big-pic4](https://user-images.githubusercontent.com/69632955/123969279-5cdb0700-d9b8-11eb-9f62-42a394da7b29.png)

### Policy Classes
A Policy Class is an atomic policy template which refers to a specific restriction on Data Usage.
Here are a list of 21 Policy Classes:

**Restrict the usage of data to:**
 1. Data Consumer
 
This policy restricts the usage of the data to a specific Data Consumer, regardless of how many connectors they have and without any further usage restriction.

```
{    
 "@context": {
 	"ids":"https://w3id.org/idsa/core/",
 	"idsc" : "https://w3id.org/idsa/code/"
 },    
 "@type": "ids:ContractAgreement",    
 "@id": "https://w3id.org/idsa/autogen/contract/complex-policy-access",    
 "profile": "http://example.com/ids-profile",    
 "ids:provider": "http://example.com/party/my-party",    
 "ids:consumer": "http://example.com/ids/party/consumer-party",    
 "ids:permission": [{    
 	"ids:target": {
 		"@id":"http://example.com/ids/data/d1234"
 	},    
 	"ids:action": [{
 		"@id":"idsc:USE"
      	}]
  }] 
} 
```

 2. IDS Connector
 
The Connector-restricted Data Usage policy restricts the usage of the data to a specific IDS connector of a specific Data Consumer assuming that the Data Consumer owns one or more IDS Connector(s).

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:CONNECTOR"},  
	"ids:operator": { "@id": "idsc:IN"},  
	"ids:rightOperand": [{ 
		"@value": "?connector1URI", 
		"@type": "xsd:anyURI"
	},{ 
		"@value": "?connector2URI", 
		"@type": "xsd:anyURI"
	}], 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/connector", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
			"@value":"https://consumer.org/pip/ep/connector", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

3. Security Level

The Security Level-restricted Data Usage policy restricts the usage of the data to a Specific IDS Connector when it is certified for a specified security level (i.e. *idsc:BASE_SECURITY_PROFILE*, *idsc:TRUST_SECURITY_PROFILE* and *idsc:TRUST_PLUS_SECURITY_PROFILE*).

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:SECURITY_LEVEL"},  
	"ids:operator": { "@id": "idsc:EQUALS"},  
	"ids:rightOperand": { 
		"@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", 
		"@type": "xsd:string"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/security", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/security", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:SECURITY_LEVEL"},  
	"ids:operator": { "@id": "idsc:IN"},  
	"ids:rightOperand": [{ 
		"@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", 
		"@type": "xsd:string"
	},{ 
		"@value": "idsc:TRUST_SECURITY_PROFILE", 
		"@type": "xsd:string"
	}], 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/security", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
			"@value":"https://consumer.org/pip/ep/security", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

4. Application inside a Connector

The Application-restricted Data Usage policy restricts the usage of the data to a specific application inside an IDS connector.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:APPLICATION"},  
	"ids:operator": { "@id": "idsc:SAME_AS"},  
	"ids:rightOperand": { 
		"@value": "http://example.com/ids/application/smart-app", 
		"@type": "xsd:anyURI"
	}, 
	"ids:pipEndpoint":{
	"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/application", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/application", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:APPLICATION"},  
	"ids:operator": { "@id": "idsc:IN"},  
	"ids:rightOperand": [{ 
		"@value": "http://example.com/ids/application/smart-app1", 
		"@type": "xsd:anyURI"
	},{ 
		"@value": "http://example.com/ids/application/smart-app2", 
		"@type": "xsd:anyURI"
	}], 
	"ids:pipEndpoint":{
	"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/application", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/application", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```


5. User Role

This policy restricts the usage of the data to a specific users (e.g. users with specific roles or users who are members of a specific organization, etc.). 

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:ROLE"},  
	"ids:operator": { "@id": "idsc:EQUALS"},  
	"ids:rightOperand": { 
		"@value": "?userRole", 
		"@type": "xsd:string"
	}, 
	"ids:pipEndpoint":{
	"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/role", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/role", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

6. Time Interval

The time interval-restricted Data Usage policy restricts the usage of the data to a specific time interval.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:DATE_TIME"},  
	"ids:operator": { "@id": "idsc:TEMPORAL_EQUALS"},  
	"ids:rightOperand": { 
		"@type": "ids:Interval", 
		"ids:begin": {
			"@type": "ids:Instant",
			"dateTime": {
				"@value": "2021-07-01T08:00:00Z",
				"@type": "xsd:dateTimeStamp"
			}
		}, 
		"ids:end": {
			"@type": "ids:Instant",
			"dateTime": {
				"@value": "2021-09-30T08:00:00Z",
				"@type": "xsd:dateTimeStamp"
			}
		}
	},
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/dt", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/dt ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:DATE_TIME"},  
	"ids:operator": { "@id": "idsc:BEFORE"},  
	"ids:rightOperand": { 
		"@type": "ids:Instant", 
		"ids:dateTime": {
			"@value": "2021-07-31T08:00",
			"@type": "xsd:dateTimeStamp"
		}
	},	
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/dt", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/dt ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

7. Duration

The Duration-restricted Data Usage policy restricts the usage of the data to a specific period.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:ELAPSED_TIME"},  
	"ids:operator": { "@id": "idsc:SHORTER_EQ"},  
	"ids:rightOperand": { 
		"@type": "ids:durationEntity", 
		"ids:begin": {
			"@value": "2021-07-01T08:00:00Z",
			"@type": "xsd:dateTimeStamp"
		}, 
		"ids:hasDuration": {
			"@value": "P3M",
			"@type": "xsd:duration"
		} 
	},	
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/dt", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/dt ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

8. Location

The Location-restricted Data Usage policy restricts the usage of the data to a specific location. It may be expressed as names of areas or set of geographic points.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:ABSOLUTE_SPATIAL_POSITION"},  
	"ids:operator": { "@id": "idsc:SAME_AS"},  
	"ids:rightOperand": { 
		"@value": "http://ontologi.es/place/DE", 
		"@type": "xsd:anyURI"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/location", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/location ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

```
"ids:constraint": [{
	"@type": "ids:Constraint",
	"ids:leftOperand": { "@id": "idsc:ABSOLUTE_SPATIAL_POSITION" },
	"ids:operator": { "@id": "idsc:INSIDE" },
	"ids:rightOperand": [{
		"@type": "ids:GeoPoint",
		"ids:latitude": {
			"@value": "?lat",
			"@type": "xsd:float"
		},
		"ids:longitude": {
			"@value": "?lng",
			"@type": "xsd:float"
		}
	}],
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/location", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/location ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```


```
"ids:constraint": [{
	"@type": "ids:Constraint",
	"ids:leftOperand": { "@id": "idsc:ABSOLUTE_SPATIAL_POSITION" },
	"ids:operator": { "@id": "?spatialOperator" },
	"ids:rightOperand": {
		"@type": "ids:BoundingPolygon",
		[{
			"@type": "ids:GeoPoint",
			"ids:latitude": {
				"@value": "?lat",
				"@type": "xsd:float"
			},
			"ids:longitude": {
				"@value": "?lng",
				"@type": "xsd:float"
			}
		},{
			"@type": "ids:GeoPoint",
			"ids:latitude": {
				"@value": "?lat",
				"@type": "xsd:float"
			},
			"ids:longitude": {
				"@value": "?lng",
				"@type": "xsd:float"
			}
		},{
			"@type": "ids:GeoPoint",
			"ids:latitude": {
				"@value": "?lat",
				"@type": "xsd:float"
			},
			"ids:longitude": {
				"@value": "?lng",
				"@type": "xsd:float"
			}
		}]
	},
, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/location", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/location ", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

9. Purpose

The Purpose-restricted Data Usage policy restricts the usage of the data to specific purposes such as "marketing", "research", "Defect Analysis".

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:PURPOSE"},  
	"ids:operator": { "@id": "idsc:SAME_AS"},  
	"ids:rightOperand": { 
		"@value": "http://example.com/ids/purpose/marketing", 
		"@type": "xsd:anyURI"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/purpose", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/purpose", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

10. Event

The Event-restricted Data Usage policy restricts the usage of the data to specific events such as "Hannover Messe 2018" .

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:EVENT"},  
	"ids:operator": { "@id": "idsc:SAME_AS"},  
	"ids:rightOperand": { 
		"@value": "http://example.com/ids/event/exhibition", 
		"@type": "xsd:anyURI"
	}, 
	"ids:pipEndpoint":{
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/event", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/event", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

11. Data Sale Contract

In this policy it is assumed that the payment has been done once.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:PAY_AMOUNT"},  
	"ids:operator": { "@id": "idsc:EQ"},  
	"ids:rightOperand": { 
		"@value": "?fee", 
		"@type": "http://www.w3.org/2001/XMLSchema#double"
	},
	"ids:contract": "http://dbpedia.org/page/Sale",
	"ids:unit": "http://dbpedia.org/resource/Euro",	
	"ids:pipEndpoint":{
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/payment", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://provider.org/pip/ep/payment", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

12. Data Rental Contract

In this policy it is assumed that the payment shall happen frequently (e.g. monthly)

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:PAY_AMOUNT"},  
	"ids:operator": { "@id": "idsc:EQ"},  
	"ids:rightOperand": { 
		"@value": "?fee", 
		"@type": "http://www.w3.org/2001/XMLSchema#double"
	},
	"ids:contract": "http://dbpedia.org/page/Rent",
	"ids:unit": "http://dbpedia.org/resource/Euro",	
	"ids:frequency": {"@id":"idsc:ANNUAL" },
	"ids:pipEndpoint":{
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/payment", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://provider.org/pip/ep/payment", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

13. Artifact State

The Artifact state-restricted Data Usage policy restricts that the data is used (or distributed) when it is encrypted, anonymized, combined, etc.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:ARTIFACT_STATE"},  
	"ids:operator": { "@id": "idsc:EQUALS"},  
	"ids:rightOperand": { 
		"@value": "idsc:ENCRYPTED",
		"@type": "xsd:string"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/artifact", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/artifact", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

14. State

The State-restricted Data Usage policy restricts the usage of the data to specific environment state. For example, the data only can be used in an application when the firewall is activated.

```
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:STATE"},  
	"ids:operator": { "@id": "idsc:EQUALS"},  
	"ids:rightOperand": { 
		"@value": "?state",
		"@type": "xsd:string"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/state", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/state", 
			"@type":"xsd:anyURI"
		} 
	} 
}]
```

15. Number of usage

This policy may be specified as “the Data Consumer is allowed to use my data not more than *n* times”

```
"ids:action": [{
	"@id":"idsc:USE"
}],
"ids:constraint": [{    
	"@type":"ids:Constraint",  
	"ids:leftOperand": { "@id": "idsc:COUNT"},  
	"ids:operator": { "@id": "idsc:LTEQ"},  
	"ids:rightOperand": { 
		"@value": "30",
		"@type": "xsd:decimal"
	}, 
	"ids:pipEndpoint":{
		"@type":"ids:PIP",
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/pip/id/count", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
		     "@value":"https://consumer.org/pip/ep/count", 
			"@type":"xsd:anyURI"
		} 
	} 
}],
"ids:postDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:INCREMENT_COUNTER", 
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/increment_counter", 
				"@type":"xsd:anyURI"
			}, 
				"ids:endpointURI":{
				"@value":"https://consumer.org/ep/increment_counter", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

**Execute one or more actions before data usage:**

16. Modify Data in Rest

This policy demands to modify the data fields of a repository inside a connector.
```
{    
 "@context": {
 	"ids":"https://w3id.org/idsa/core/",
 	"idsc" : "https://w3id.org/idsa/code/"
 },    
 "@type": "ids:ContractAgreement",    
 "@id": "https://w3id.org/idsa/autogen/contract/complex-policy-access",    
 "profile": "http://example.com/ids-profile",    
 "ids:provider": "http://example.com/party/my-party",    
 "ids:consumer": "http://example.com/ids/party/consumer-party",    
 "ids:obligation": [{    
 	"ids:target": {
 		"@id":"http://example.com/ids/data/d1234"
 	},    
 	"ids:action": [{
 		"@id":"idsc:ANONYMIZE"
	}],
	"ids:pxpEndpoint":{
		"@type":"ids:PXP", 
		"ids:interfaceDescription":{
			"@value":"https://example.com/ids/PXP/id/anonymize", 
			"@type":"xsd:anyURI"
		}, 
		"ids:endpointURI":{
			"@value":"https://consumer.org/PXP/ep/anonymize", 
			"@type":"xsd:anyURI"
		} 
	}
  }] 
}
```

17. Modify Data in Transit

This policy demands to intercept the data flow and modify the data in transit.

```
"ids:action": [{
	"@id":"idsc:USE"
}], 
"ids:preDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:REPLACE",     
		"ids:refinement": [{    
			"@type":"ids:Constraint",  
			"ids:leftOperand": { "@id":"idsc:REPLACE_WITH"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value": "xxxx", 
				"@type": "xsd:string"}
		},{    
			"@type":"ids:Constraint",  
			"ids:leftOperand":{"@id":"idsc:JSON_PATH"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value":"$.name",
				"@type": "xsd:string"}
		}] , 
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/replace", 
				"@type":"xsd:anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://consumer.org/ep/replace", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

18. Pass a Policy to the Third-party

This policy demands to pass a policy to a third-party before distributing the data to them.

```
"ids:permission": [{   
	"ids:target": {
		"@id":"http://example.com/ids-data/production-plan"
	}, 
	"ids:action": [{
		"@id":"idsc:DISTRIBUTE"
	}], 
	"ids:preDuty": [{    
		"@type":"ids:Duty",  
		"ids:action": [{
			"@id":"idsc:NEXT_POLICY",     
			"ids:refinement": [{    
				"@type":"ids:Constraint",  
				"ids:leftOperand": { "@id":"idsc:TARGET_POLICY"},  
				"ids:operator": { "@id": "idsc:DEFINES_AS"},  
				"ids:rightOperand": {
					"@value": "?policyURI", 
					"@type": "xsd:anyURI"}
			}],
			"ids:pxpEndpoint":{
				"@type":"ids:PXP",
				"ids:interfaceDescription":{
					"@value":"https://example.com/ids/PXP/id/attachPolicy", 
					"@type":"xsd:anyURI"
				}, 
				"ids:endpointURI":{
					"@value":"https://consumer.org/ep/attachPolicy", 
					"@type":"xsd:anyURI"
				} 
			}
		}]
	}]
}]
```

**Execute one or more actions after data usage:**

19. Log Data Usage Information

This policy demands to log the Data Usage information either locally or on the Clearing House.

```
"ids:action": [{
	"@id":"idsc:USE"
}], 
"ids:postDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:LOG",     
		"ids:refinement": [{    
			"@type":"ids:Constraint",  
			"ids:leftOperand": { "@id":"idsc:LOG_LEVEL"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value": "idsc:ON_ALLOW", 
				"@type": "xsd:string"}
		},{    
			"@type":"ids:Constraint",  
			"ids:leftOperand":{"@id":"idsc:SYSTEM_DEVICE"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value":"http://example.com/ids/system/local-log-server",
				"@type": "xsd:anyURI"}
		}] ,
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/log", 
				"@type":"xsd:anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://provider.org/ep/log", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

Note: A Policy Enforcement Point (PEP) intercepts the data flow and waits for an authorization decision from the Policy Decision Point (PDP). A Policy Execution Point (PXP) which is responsible to log the information could return a boolean value to the PDP and eventually influence the PEP. In a case that the Clearing House is not available, the PXP cannot execute the duty action and therefore it would return a False value to the PDP. Eventually, the PEP can block the data flow after receiving a usage deny from the PDP. A policy would specify such a case slightly different than what is shown above.

20. Inform a participant about the Data Usage

This policy demands to inform a participant (e.g. person, organization), or even the Clearing House about the Data Usage. For example, inform a party via Email or a notification system.

```
"ids:action": [{
	"@id":"idsc:USE"
}], 
"ids:postDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:NOTIFY",     
		"ids:refinement": [{    
			"@type":"ids:Constraint",  
			"ids:leftOperand": { "@id":"idsc:NOTIFICATION_LEVEL"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value": "idsc:ON_ALLOW", 
				"@type": "xsd:string"}
		},{    
			"@type":"ids:Constraint",  
			"ids:leftOperand":{"@id":"idsc:RECIPIENT"},  
			"ids:operator": { "@id": "idsc:DEFINES_AS"},  
			"ids:rightOperand": {
				"@value":"http://example.com/ids/party/my-party",
				"@type": "xsd:anyURI"}
		}] , 
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/notify", 
				"@type":"xsd:anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://provider.org/ep/notify", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

21. Delete Data

This policy demands to delete data either immediately after it is used, or after a delay period or before a defined deadline.

```
"ids:action": [{
	"@id":"idsc:USE"
}], 
"ids:postDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:DELETE",     
		"ids:refinement": [{    
			"@type":"ids:Constraint",  
			"ids:leftOperand": { "@id": "idsc:DELAY"},  
			"ids:operator": { "@id": "idsc:DURATION_EQ"},  
			"ids:rightOperand": { 
				"@type": "ids:durationEntity", 
				"ids:hasDuration": {
					"@value": "P6M",
					"@type": "xsd:duration"
				} 
			}, 
			"ids:pipEndpoint":{
				"ids:interfaceDescription":{
					"@value":"https://example.com/ids/PIP/id/delay", 
					"@type":"xsd:anyURI"
				}, 
				"ids:endpointURI":{
					"@value":"https://consumer.org/ep/delay", 
					"@type":"xsd:anyURI"
				} 
			} 
		}] , 
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/delete", 
				"@type":"xsd:anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://consumer.org/ep/delete", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

```
"ids:action": [{
	"@id":"idsc:USE"
}], 
"ids:postDuty": [{    
	"@type":"ids:Duty",  
	"ids:action": [{
		"@id":"idsc:DELETE",     
		"ids:refinement": [{    
			"@type":"ids:Constraint",  
			"ids:leftOperand": { "@id": "idsc:DATE_TIME"},  
			"ids:operator": { "@id": "idsc:BEFORE"},  
			"ids:rightOperand": { 
				"@type": "ids:Instant", 
				"ids:dateTime": {
					"@value": "2022-10-09T15:03"
					"@type": "xsd:dateTimeStamp",
				} 
			}, 
			"ids:pipEndpoint":{
				"ids:interfaceDescription":{
					"@value":"https://example.com/ids/PIP/id/date_time", 
					"@type":"xsd:anyURI"
				}, 
				"ids:endpointURI":{
					"@value":"https://consumer.org/ep/date_time", 
					"@type":"xsd:anyURI"
				} 
			} 
		}] , 
		"ids:pxpEndpoint":{
			"@type":"ids:PXP",
			"ids:interfaceDescription":{
				"@value":"https://example.com/ids/PXP/id/delete", 
				"@type":"xsd:anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://consumer.org/ep/delete", 
				"@type":"xsd:anyURI"
			} 
		}
	}]
}]
```

**Usage Control Policy**
It is an identified policy that is a combination of one or more instances of the policy classes.
Example: "The Data Consumer X shall use my data not later than 30th of December 2022 and only for defect analysis purpose."

### Policy Editor
A policy editor or in XACML terminology a Policy Administration Point (PAP) supports data owners and data providers in specifying their usage restrictions. 
The [IDS Policy editor](https://odrl-pap.mydata-control.de/) comprises a Graphical User Interface and offers a Policy Specification guidance to the user.

### Policy Negotiation
A negotiation process takes care of the potential bargaining between a Data Provider and a Data Consumer regarding the usage conditions. An *Offer Contract* and a *Request Contract* are created by a Data Provider and a Data Consumer, respectively, and reflect their Data Usage requirements and demands. 
As a result of a Policy Negotiation process, it is expected that the involved parties reach an agreement. The corresponding *Agreement Contract* is specified in *IDS Usage Policy Language* and shall be enforced to their systems. 

More information: [Data Sovereignty: Updated Position Paper on Data Usage Control in the IDS](https://internationaldataspaces.org/data-sovereignty-updated-position-paper-on-data-usage-control-in-the-ids/)
### Policy Transformation
The technically enforceable rules shall be transformed to a technology dependent policy to facilitate the Usage Control enforcement of data sovereignty.
In order to support the Data Usage Control technologies, the policy transformation service is additionally added to the IDS Policy Editor. Currently, it supports the transformation to enforceable policies for the MY DATA Control Technologies. The support for other technologies and further extension will follow.
