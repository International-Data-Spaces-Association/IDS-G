# Concepts for Data Sharing

In general, a comprehensive concept for data sharing comprises variety of concepts such as: sharing data among the applications, connectors and data storages inside the IDS infrastructure as well as data warehouses and cloud systems outside of IDS world. The complexity of this subject affects both aspects of Data Usage Control, the policy specification and the technical enforcement.

The image below roughly illustrates the cases that address the data sharing issue. In this document, we investigate each of the cases separately.

![image](https://user-images.githubusercontent.com/69632955/159927689-cfe55ace-85c3-4a92-8813-acba1dcea0f4.png)

Please, note that the icons in green refer to the devices that is controlled by IDS and the ones in blue refer to the devices that are not in the control of IDS infrastructure.

## Operate variety of actions on the data (Display, print, etc.)

There might one or more Data App(s) running inside the Connectors. In order to cover the Usage Control concepts inside the applications, we need to know about the Actions that they operate on data. For example, processing data, analyzing data, storing data, visualizing data, printing data, etc. (as shown in the image). If possible, then we can implement the Policy Enforcement Point (PEPs) inside the applications and intercept the data flow and enforce the policies. This, of course, demands some implementation efforts.

![image](https://user-images.githubusercontent.com/69632955/159927861-04ba76e1-df59-4b3f-a285-4bf9e4a61b3b.png)

Moreover, we need to know which of the actions that are operated by the Data Apps require sharing data. This can be storing data in a data repository for processing the data or transerring the data to a cloud platform to receive a service or simply showing the data on a screen in order to display it to a group of users. In the table below, we have a list of actions that may be operated by the Data Apps, and may lead to share data with other systems or users. 
  
   | **IDS Action** | **IDS Info Model Definition**  | **ODRL Definition** |
|--|--|--|
  | Delete | To remove a resource or inhibit any further access with reasonable measures. This action is evaluated at the consumer side if used in a duty clause. A provider cannot be forced to delete its data resources. If used in a permission clause, it is effecting the data provider as it allows the consumer to delete the provider's resource remotely. | To permanently remove all copies of the Asset after it has been used. Use a constraint to define under which conditions the Asset must be deleted. |
  | Read | To obtain data from the resource. This action is always evaluated at the provider side. It corresponds to 'give access to a resource'. | To obtain data from the Asset. For example, the ability to read a record from a database (the Asset). |
  | Anonymize | To anonymize all, parts or certain attributes of the resource. This action is always evaluated at the provider side. | To anonymize all or parts of the Asset. For example, to remove identifying particulars for statistical or for other comparable purposes, or to use the Asset without stating the author/source. |
  | Compensate | To pay a certain amount of money in order to use a resource. This action must be evaluated both at the consumer and provider side. A compensation might be required before access is granted (provider-side), or each time the usage action is performed (consumer-side). | To compensate by transfer of some amount of value, if defined, for using or selling the Asset. |
  | Distribute | To forward or supply a resource to a third-party. | To supply the Asset to third-parties. It is recommended to use nextPolicy to express the allowable usages by third-parties. |
  | Print(?) | -- | To create a tangible and permanent rendition of an Asset. For example, creating a permanent, fixed (static), and directly perceivable representation of the Asset, such as printing onto paper. |
  | Display(?) | -- | To create a static and transient rendition of an Asset. For example, displaying an image on a screen. If the action is to be performed to a wider audience than just the Assignees, then the Recipient constraint is recommended to be used. |
  | Derive(?) | -- | To create a new derivative Asset from this Asset and to edit or modify the derivative. A new asset is created and may have significant overlaps with the original Asset. (Note that the notion of whether or not the change is significant enough to qualify as a new asset is subjective). To the derived Asset a next policy may be applied. |
  | Execute(?) | -- | To run the computer program Asset. For example, machine executable code or Java such as a game or application. |
  | Sharing (?) |--| Permits commercial derivatives, but only non-commercial distribution. |
  | ? |--|--|

## Transfer data outside of the Connector

### Distribute data to another IDS Participant

In the table below, there are defnitions for three actions, Use, Distribute and Next Policy. These actions address the policy rules about distributing data to another IDS connectors.

 | **IDS Action** | **Definition**  | **Comments** |
|--|--|--|
| Use | To use a resource in any possible way. It also includes transfering the Asset to other connectors of the same party to be used by them (if permitted by the policy). | Another Connetor, same Participant, same Policy |
| Distribute | To forward or supply a resource to a third-party. This action is always evaluated at the consumer side and allows it to become a data provider of this resource. The corresponding Offer Contract shall be refered via Next Policy preduty. | Another Connetor, another Participant, an Offer Policy (based on the template given by the Data Owner- if exists any) |
| Next Policy | To grant the specified Policy to a third party for their use of the Asset. A refinement with idsc:TARGET_POLICY Left Operand must be set. It shall refer to an Offer Contract. | |

As illustrated in the image below, we differentiate between transferring data to another Connector of the same Participant and another Connector of anothe Participant. 

When a policy simply allow the Data Consumer A to "use" the data without any other restriction on the "distribute" action, the Data Consumer shall be allowed to actually use the data by all it's Connectors. Technically speaking, an IDS USE PEP (Policy Enforcement Point) shall let the data flow to the other Connector of the Data Consumer A.

However, a policy may explicitly prohibit distributing data to another Participant. In this case, an IDS DISTRIBUTE PEP shall be realized to intercept the data flow and technically inhibit the action of distributing data to the Data Consumer B.

![image](https://user-images.githubusercontent.com/69632955/159928970-a48fe06f-67df-4440-ab9c-8a7e46d56a69.png)

As well, a policy may allow distributing data to another Participant but demanding to grant a Usage policy (i.e. Offer template) to the third-party. An IDS NEXT_POLICY PXP (Policy Execution Point) can be realized to execute the demanded duty. An example IDS Usage Policy for this case is as follows:

  
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
		"ids:pipEndpoint":{
			"@type":"ids:PIP", 
			"ids:interfaceDescription":{
				"@value":"https://example.com/PIP/interfaceDescription/artifact_state", 
				"@type":"anyURI"
			}, 
			"ids:endpointURI":{
				"@value":"https://example.com/PXPendpoint/artifact_state", 
				"@type":"anyURI"
			} 
		} 
	}],
	"ids:preDuty": [{    
		"@type":"ids:Duty",  
		"ids:action": [{
			"@id":"idsc:NEXT_POLICY",     
			"ids:refinement": [{    
				"@type":"ids:Constraint",  
				"ids:leftOperand": { "@id": "idsc:TARGET_POLICY"},  
				"ids:operator": { "@id": "idsc:DEFINES_AS"}, 
				"ids:rightOperand": [{
					"@value": "http://example.com/ids/policy/offer123", 
					"@type": "xsd:anyURI"
				}] 
			}], 
			"ids:pxpEndpoint":{
				"@type":"ids:PXP", 
				"ids:interfaceDescription":{
					"@value":"https://example.com/PXP/interfaceDescription/next_policy", 
					"@type":"anyURI"
				}, 
				"ids:endpointURI":{
					"@value":"https://example.com/PXPendpoint/next_policy", 
					"@type":"anyURI"
				} 
			}
		}]
	}] 
}] 
} 
  ```
### Grant the use of the asset to a third-party

For more information: https://github.com/International-Data-Spaces-Association/IDS-G-pre/blob/usage-control-terms/UsageControl/Specification/Concepts/T7_data_reseller.md

## Store data outside of the Connector

### IDS controlled data storage

In order to store the data in an IDS controlled data repository outside of the Connector, a System Adapter App can be used. Using a [System Adapter App](https://github.com/International-Data-Spaces-Association/IDS-G-pre/blob/usage-control-terms/UsageControl/Enforcement/SystemAdapter.md), an IDS Connector has mechanisms of how to store and retrieve the data securely. In order to achieve this security, a System Adapter App applies three principles: authentication, authorization and encryption. Beside providing this main functionality, a System Adapter App also serves as an interface for other parts of a Connector to interact with each other.

![image](https://user-images.githubusercontent.com/69632955/159927983-3d24e537-1b6b-478f-b03f-3b9c713b46c6.png)

For more information: https://github.com/International-Data-Spaces-Association/IDS-G-pre/blob/usage-control-terms/UsageControl/Enforcement/SystemAdapter.md

### None-IDS controlled data storage

Here, the main question is how it could be possible to still control a storage (e.g. CloudServices AWS, AZURE) when it doesn't blong to the IDS infrastructure. Obviously, it is very system specific. However, we may realize two general solutions to address this problem:

- Extend System Adapter in lazy mode
- Keep IDS informations (metadata like targetID)

![image](https://user-images.githubusercontent.com/69632955/159928726-7753f360-e724-4b3a-aaa4-40856873b385.png)

