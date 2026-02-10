# Department Routing Map

## Type to Department Assignment

| Complaint Type | Department | Responsible Email | Priority |
|----------------|------------|-------------------|----------|
| Product Quality | Quality Assurance | qa.manager@tpg.co.zw | High |
| Adverse Event | Pharmacovigilance | pharmacovigilance@tpg.co.zw | **Critical** |
| Product Portfolio | Product Management | product.manager@tpg.co.zw | Medium |
| Account Services | Customer Service | cs.manager@tpg.co.zw | Medium |
| Delivery - Local | Logistics - Local | logistics.local@tpg.co.zw | Medium |
| Delivery - Out of Harare | Logistics - Regional | logistics.regional@tpg.co.zw | Medium |
| Query - Sales | Sales | sales.manager@tpg.co.zw | Low |
| Query - Marketing | Marketing | marketing.manager@tpg.co.zw | Low |
| Query - Product | Product Management | product.manager@tpg.co.zw | Low |
| Query - General | Customer Service | jkaseke@tpg.co.zw | Low |

## Adverse Event Special Handling

When Type = "Adverse Event":
- Priority automatically set to **Critical**
- Immediate alert sent to:
  - MD: md@tpg.co.zw
  - HR: hr@tpg.co.zw
  - Regulatory: regulatory@tpg.co.zw
  - Pharmacovigilance: pharmacovigilance@tpg.co.zw
- SLA: Acknowledgment within 1 hour, assessment within 4 hours

## Escalation Contacts

### Day 5+ Escalation
- MD: md@tpg.co.zw
- HR: hr@tpg.co.zw
- Original Department Manager

### Default Contact
- General/Fallback: jkaseke@tpg.co.zw

## Power Automate Implementation

### Using Compose Actions (Recommended)
```
Compose: DetectedDepartment
Expression:
if(equals(variables('DetectedType'),'Product Quality'),'Quality Assurance',
if(equals(variables('DetectedType'),'Adverse Event'),'Pharmacovigilance',
if(equals(variables('DetectedType'),'Product Portfolio'),'Product Management',
if(equals(variables('DetectedType'),'Account Services'),'Customer Service',
if(equals(variables('DetectedType'),'Delivery - Local'),'Logistics - Local',
if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'Logistics - Regional',
if(equals(variables('DetectedType'),'Query - Sales'),'Sales',
if(equals(variables('DetectedType'),'Query - Marketing'),'Marketing',
if(equals(variables('DetectedType'),'Query - Product'),'Product Management',
'Customer Service')))))))))
```

### Extracting Choice Field Values
When reading from SharePoint trigger:
```
triggerOutputs()?['body/Type']?['Value']
```

Note: Must use `?['Value']` suffix to extract text from Choice fields.
