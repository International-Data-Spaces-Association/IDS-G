# Concepts for Participant-restricted policies and reselling data

## Participant-restricted policy

Question: how to refine the restriction on the Participants? For example, how can we specify a policy that restricts the usage of the data to those Participants that their location is set to be in EU. 

ODRL Language provide the possibility to specify [properties for a Party](https://www.w3.org/TR/odrl-model/#party-partof). Is it possible to refer to these properties inside a policy and restrict the data usage to specific values for these properties?


```
{
   "@context":{
      "ids":"https://w3id.org/idsa/core/",
      "idsc":"https://w3id.org/idsa/code/"
   },
   "@type":"ids:ContractAgreement",
   "@id":"https://w3id.org/idsa/autogen/contract/complex-policy-access",
   "profile":"http://example.com/ids-profile",
   "ids:provider":"http://example.com/party/my-party",
   "ids:consumer":"http://example.com/ids/party/consumer-party",
   "ids:permission":[
      {
         "ids:target":{
            "@id":"http://example.com/ids/data/production-plan"
         },
         "ids:action":[
            {
               "@id":"idsc:USE"
            }
         ]
      }
   ]
}
```

## Policy for reselling data

ODRL language differentiates between [selling](https://www.w3.org/TR/odrl-vocab/#term-sell) data and [giving](https://www.w3.org/TR/odrl-vocab/#term-give) data as shown in the table below. The difference is that when the data is sold, an obligation (preduty) of paying an agreed amount of money (i.e, [compensate](https://www.w3.org/TR/odrl-vocab/#term-compensate)) is demanded while giving data to another Party doesn't require any compensation. However, in the definitions of both actions, the original data is deleted after it is transferred to another party. Therefore, by default, the data is deleted and cannot be resold! Of course, the reason is that the ODRL language addresses Data Assets which are already materialized.


 | **IDS Action** | **IDS Info Model Definition**  | **ODRL Definition** |
|--|--|--|
  | Read | To obtain data from the resource. This action is always evaluated at the provider side. It corresponds to 'give access to a resource'. | To obtain data from the Asset. For example, the ability to read a record from a database (the Asset). |
  | Use | To use a resource in any possible way. Includes all other actions.  | To use the Asset |
  | Compensate | To pay a certain amount of money in order to use a resource. This action must be evaluated both at the consumer and provider side. A compensation might be required before access is granted (provider-side), or each time the usage action is performed (consumer-side). | To compensate by transfer of some amount of value, if defined, for using or selling the Asset. |
  | Distribute | To forward or supply a resource to a third-party. | To supply the Asset to third-parties. It is recommended to use nextPolicy to express the allowable usages by third-parties. |
  | Next Policy |--| To grant the specified Policy to a third party for their use of the Asset. |
  | Derive | -- | To create a new derivative Asset from this Asset and to edit or modify the derivative. A new asset is created and may have significant overlaps with the original Asset. (Note that the notion of whether or not the change is significant enough to qualify as a new asset is subjective). To the derived Asset a next policy may be applied. |
  | Share Alike |--| Derivative works be licensed under the same terms or compatible terms as the original work. |
  | Grant use |--| To grant the use of the Asset to third parties. This action enables the assignee to create policies for the use of the Asset for third parties. The nextPolicy is recommended to be agreed with the third party. Use of temporal constraints is recommended. |
  | Sharing |--| Permits commercial derivatives, but only non-commercial distribution. |
  | Give |--| To transfer the ownership of the Asset to a third party without compensation and while deleting the original asset. |
  | Sell |--| To transfer the ownership of the Asset to a third party with compensation and while deleting the original asset. |
  
There are two other actions introduced by ODRL language that address transferring data to another party; [Distribute](https://www.w3.org/TR/odrl-vocab/#term-distribute) and [Sharing](https://www.w3.org/TR/odrl-vocab/#term-Sharing). Distribute data means supplying a resource to a third-party and sharing data means distributing data only for non-commercial purposes. In addition, the action sharing permits commercial derivatives. The differences of the explained actions are summerized in the table below:

| ODRL Action | Commercial distribution | Compensate Duty | Next Policy Duty | Delete original Asset |
|--|--|--|--|--|
| Distribute | Y | - | R* | N |
| Sharing | N | - | - | N |
| Give | - | N | - | Y |
| Sell | - | Y | - | Y |

R* = Recommended

### Solution 1: Using the "Distribute" action

We shall use the "Distribute" action together with the "Grant use" and "Compensate" duties in order to deletegate authorities to the third party and demand a payment. 

A sample Usage policy:

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
"ids:consumer": "http://example.com/party/consumer-party-A",    
"ids:permission": [{    
  "ids:target": {
  	"@id":"http://example.com/ids/target/data1234"
  },    
  "ids:action": [{
  	"@id":"idsc:USE"
  }]
}, 
{    
  "ids:target": {
  	"@id":"http://example.com/ids/target/data1234"
  },    
  "ids:action": [{
  	"@id":"idsc:DISTRIBUTE"
  }], 
  	"ids:constraint": [{    
  	"@type":"ids:Constraint",  
  	"ids:leftOperand": { "@id": "idsc:ARTIFACT_STATE"},  
  	"ids:operator": { "@id": "idsc:EQUALS"}, 
  	"ids:rightOperand": [{
  		"@value": "idsc:ENCRYPTED", 
  		"@type": "xsd:string"
  	}]
  }],
  "ids:preDuty": [{    
  	"@type":"ids:Duty",  
  	"ids:action": [{
  		"@id":"idsc:COMPENSATE",     
  		"ids:refinement": [{    
  			"@type":"ids:Constraint",  
  			"ids:leftOperand": { "@id": "idsc:PAY_AMOUNT"},  
  			"ids:operator": { "@id": "idsc:EQ"}, 
  			"ids:rightOperand": {
  				"@value": "500.00", 
  				"@type": "xsd:decimal"
  			},
			"unit": "http://dbpedia.org/resource/Euro" 
  		}]
  	}]
  },{    
  	"@type":"ids:Duty",  
  	"ids:action": [{
  		"@id":"idsc:GRANT_USE",     
  		"ids:refinement": [{    
  			"@type":"ids:Constraint",  
  			"ids:leftOperand": { "@id": "idsc:RECIPIENT"},  
  			"ids:operator": { "@id": "idsc:EQUALS"}, 
  			"ids:rightOperand": [{
  				"@value": "http://example.com/party/consumer-party-A", 
  				"@type": "xsd:anyURI"
  			}]
  		}]
  	}]
  }] 
}] 
} 
```

### Solution 2: Redefining the "Sell" action or introducing a new action

We may introduce a new action in the IDS language which refers to selling data while not deleting the original asset or overwrite the definition of the ODRL Sell action.

## Challenges and Open Points

- Delegation of Authority
- Data Ownership

## Future Work
- Update policies with respect to the changes on the policy language (ODRL and EDC)
- Policy for restricting the usage of the data to the location of a Participant (it is different from the location where the data is used)
- Policy for obtaining consent before the data usage
- Concept for policy for metadata (restricting the availability of the offer policies)
- Policy for transfering data to a third party which is not an IDS Participant
- Policy for reselling data to another IDS Participant. This requires concepts of data ownership and delegation of authority.
- Concept and Policy for allowing creating a new derivative Asset.
- Concept and Policy for restricting the usage of derivative Assets. (e.g, policy for distributing derivative works)
- Policy for how offen the data can be shared. (i.e, refinements on the distribute action)
