# Flow 2: Email Intake - Customer Complaints

## Flow Name
`Customer Complaints - Email Intake`

## Trigger
**When a new email arrives in a shared mailbox (V2)**
- Original Mailbox Address: `complaints@tpg.co.zw`
- Folder: Inbox
- Include Attachments: Yes
- Only with Attachments: No

---

## Step 1: Initialize Variables

### Variable 1: DetectedType
- **Name:** `DetectedType`
- **Type:** String
- **Value:** (leave empty)

### Variable 2: DetectedDepartment
- **Name:** `DetectedDepartment`
- **Type:** String
- **Value:** (leave empty)

### Variable 3: ResponsibleEmail
- **Name:** `ResponsibleEmail`
- **Type:** String
- **Value:** (leave empty)

---

## Step 2: Compose - Detect Type from Email

**Action:** Compose

**Inputs Expression:**
```
if(or(contains(toLower(triggerBody()?['subject']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'defect')),'Product Quality',
if(or(contains(toLower(triggerBody()?['subject']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'side effect')),'Adverse Event',
if(or(contains(toLower(triggerBody()?['subject']),'product portfolio'),contains(toLower(triggerBody()?['bodyPreview']),'product range')),'Product Portfolio',
if(or(contains(toLower(triggerBody()?['subject']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'invoice')),'Account Services',
if(or(contains(toLower(triggerBody()?['subject']),'delivery'),contains(toLower(triggerBody()?['subject']),'harare'),contains(toLower(triggerBody()?['bodyPreview']),'delivery')),'Delivery - Local',
if(or(contains(toLower(triggerBody()?['subject']),'out of harare'),contains(toLower(triggerBody()?['bodyPreview']),'out of harare')),'Delivery - Out of Harare',
if(or(contains(toLower(triggerBody()?['subject']),'sales'),contains(toLower(triggerBody()?['bodyPreview']),'sales rep')),'Query - Sales',
if(or(contains(toLower(triggerBody()?['subject']),'marketing'),contains(toLower(triggerBody()?['bodyPreview']),'marketing')),'Query - Marketing',
if(or(contains(toLower(triggerBody()?['subject']),'product query'),contains(toLower(triggerBody()?['bodyPreview']),'product information')),'Query - Product',
'Query - General')))))))))
```

**Rename to:** `Compose - Detect Type`

---

## Step 3: Set Variable - DetectedType

**Action:** Set variable
- **Name:** `DetectedType`
- **Value:** `outputs('Compose_-_Detect_Type')`

---

## Step 4: Compose - Route to Department

**Action:** Compose

**Inputs Expression:**
```
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

**Rename to:** `Compose - Route to Department`

---

## Step 5: Set Variable - DetectedDepartment

**Action:** Set variable
- **Name:** `DetectedDepartment`
- **Value:** `outputs('Compose_-_Route_to_Department')`

---

## Step 6: Compose - Get Responsible Email

**Action:** Compose

**Inputs Expression:**
```
if(equals(variables('DetectedType'),'Product Quality'),'qa.manager@tpg.co.zw',
if(equals(variables('DetectedType'),'Adverse Event'),'pharmacovigilance@tpg.co.zw',
if(equals(variables('DetectedType'),'Product Portfolio'),'product.manager@tpg.co.zw',
if(equals(variables('DetectedType'),'Account Services'),'cs.manager@tpg.co.zw',
if(equals(variables('DetectedType'),'Delivery - Local'),'logistics.local@tpg.co.zw',
if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'logistics.regional@tpg.co.zw',
if(equals(variables('DetectedType'),'Query - Sales'),'sales.manager@tpg.co.zw',
if(equals(variables('DetectedType'),'Query - Marketing'),'marketing.manager@tpg.co.zw',
if(equals(variables('DetectedType'),'Query - Product'),'product.manager@tpg.co.zw',
'jkaseke@tpg.co.zw')))))))))
```

**Rename to:** `Compose - Get Responsible Email`

---

## Step 7: Set Variable - ResponsibleEmail

**Action:** Set variable
- **Name:** `ResponsibleEmail`
- **Value:** `outputs('Compose_-_Get_Responsible_Email')`

---

## Step 8: Compose - Determine Priority

**Action:** Compose

**Inputs Expression:**
```
if(equals(variables('DetectedType'),'Adverse Event'),'Critical',
if(equals(variables('DetectedType'),'Product Quality'),'High',
if(or(contains(variables('DetectedType'),'Query'),equals(variables('DetectedType'),'Query - General')),'Low',
'Medium')))
```

**Rename to:** `Compose - Determine Priority`

---

## Step 9: Compose - Determine Category

**Action:** Compose

**Inputs Expression:**
```
if(contains(variables('DetectedType'),'Query'),'Query','Complaint')
```

**Rename to:** `Compose - Determine Category`

---

## Step 10: Send HTTP Request to SharePoint - Create Item

**Action:** Send an HTTP request to SharePoint
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **Method:** `POST`
- **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items`
- **Headers:**
  ```json
  {
    "Accept": "application/json;odata=verbose",
    "Content-Type": "application/json;odata=verbose"
  }
  ```
- **Body:**
  ```json
  {
    "__metadata": {
      "type": "SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem"
    },
    "DateReceived": "@{utcNow()}",
    "Category": "@{outputs('Compose_-_Determine_Category')}",
    "Type": "@{variables('DetectedType')}",
    "CustomerName": "@{triggerBody()?['from']}",
    "CustomerEmail": "@{triggerBody()?['from']}",
    "Description": "@{triggerBody()?['bodyPreview']}",
    "DepartmentResponsible": "@{variables('DetectedDepartment')}",
    "Status": "Open",
    "Priority": "@{outputs('Compose_-_Determine_Priority')}"
  }
  ```

**Rename to:** `HTTP - Create Complaint Item`

---

## Step 11: Parse JSON - Get Created Item ID

**Action:** Parse JSON
- **Content:** `body('HTTP_-_Create_Complaint_Item')`
- **Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "d": {
        "type": "object",
        "properties": {
          "Id": {
            "type": "integer"
          },
          "__metadata": {
            "type": "object",
            "properties": {
              "id": {
                "type": "string"
              },
              "uri": {
                "type": "string"
              },
              "etag": {
                "type": "string"
              },
              "type": {
                "type": "string"
              }
            }
          }
        }
      }
    }
  }
  ```

**Rename to:** `Parse - Created Item`

---

## Step 12: Compose - Generate Complaint Number

**Action:** Compose

**Inputs Expression:**
```
concat('CCF-',formatDateTime(utcNow(),'yyyy'),'-',substring(concat('00000',string(body('Parse_-_Created_Item')?['d']?['Id'])),sub(length(concat('00000',string(body('Parse_-_Created_Item')?['d']?['Id']))),5),5))
```

**Rename to:** `Compose - Complaint Number`

---

## Step 13: Send HTTP Request - Update Complaint Number

**Action:** Send an HTTP request to SharePoint
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **Method:** `POST`
- **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items(@{body('Parse_-_Created_Item')?['d']?['Id']})`
- **Headers:**
  ```json
  {
    "Accept": "application/json;odata=verbose",
    "Content-Type": "application/json;odata=verbose",
    "IF-MATCH": "*",
    "X-HTTP-Method": "MERGE"
  }
  ```
- **Body:**
  ```json
  {
    "__metadata": {
      "type": "SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem"
    },
    "ComplaintNo": "@{outputs('Compose_-_Complaint_Number')}"
  }
  ```

**Rename to:** `HTTP - Update Complaint Number`

---

## Step 14: Condition - Check if Has Attachments

**Action:** Condition
- **Condition:** `triggerBody()?['hasAttachments']`
- **Operator:** `is equal to`
- **Value:** `true`

### If Yes Branch:

#### Apply to Each - Process Attachments
**Action:** Apply to each
- **Select output from previous steps:** `triggerBody()?['attachments']`

##### Inside Apply to Each:

###### Send HTTP Request - Upload Attachment
**Action:** Send an HTTP request to SharePoint
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **Method:** `POST`
- **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items(@{body('Parse_-_Created_Item')?['d']?['Id']})/AttachmentFiles/add(FileName='@{items('Apply_to_each_-_Process_Attachments')?['name']}')`
- **Headers:**
  ```json
  {
    "Accept": "application/json;odata=verbose",
    "Content-Type": "application/json;odata=verbose"
  }
  ```
- **Body:** `@{items('Apply_to_each_-_Process_Attachments')?['contentBytes']}`

---

## Step 15: Condition - Check if Adverse Event

**Action:** Condition
- **Condition:** `variables('DetectedType')`
- **Operator:** `is equal to`
- **Value:** `Adverse Event`

### If Yes Branch:

#### Send Email - Adverse Event Alert
**Action:** Send an email (V2)
- **To:** `md@tpg.co.zw;hr@tpg.co.zw;regulatory@tpg.co.zw;pharmacovigilance@tpg.co.zw`
- **Subject:** `🚨 URGENT: Adverse Event Reported - @{outputs('Compose_-_Complaint_Number')}`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #dc3545 0%, #c82333 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .alert-icon { font-size: 48px; margin-bottom: 10px; }
  .content { background: #fff; padding: 30px; border: 2px solid #dc3545; border-top: none; border-radius: 0 0 10px 10px; }
  .info-box { background: #fff3cd; border-left: 4px solid #ffc107; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  .urgent { color: #dc3545; font-weight: bold; font-size: 18px; text-align: center; margin: 20px 0; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <div class="alert-icon">⚠️</div>
      <h1 style="margin: 0;">ADVERSE EVENT ALERT</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">Immediate Action Required</p>
    </div>
    <div class="content">
      <div class="urgent">
        ⏰ SLA: Acknowledgment within 1 hour | Assessment within 4 hours
      </div>
      
      <div class="info-box">
        <strong>⚡ This is an adverse event report requiring immediate attention per SOP MK-03-02.01</strong>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{outputs('Compose_-_Complaint_Number')}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy HH:mm')}</span></p>
        <p><span class="label">Customer:</span><span class="value">@{triggerBody()?['from']}</span></p>
        <p><span class="label">Priority:</span><span class="value" style="color: #dc3545; font-weight: bold;">CRITICAL</span></p>
        <p><span class="label">Department:</span><span class="value">Pharmacovigilance</span></p>
      </div>
      
      <div style="margin: 20px 0;">
        <p class="label">Complaint Details:</p>
        <div style="background: #f8f9fa; padding: 15px; border-radius: 5px; margin-top: 10px;">
          @{triggerBody()?['bodyPreview']}
        </div>
      </div>
      
      <div style="text-align: center; margin: 30px 0;">
        <a href="https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final/Lists/Customer%20Feedback%20and%20Complaints%20Final/AllItems.aspx" 
           style="background: #dc3545; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold;">
          View in SharePoint
        </a>
      </div>
      
      <div class="info-box">
        <strong>Required Actions:</strong>
        <ul style="margin: 10px 0;">
          <li>Acknowledge receipt within 1 hour</li>
          <li>Complete initial assessment within 4 hours</li>
          <li>Notify relevant regulatory authorities if required</li>
          <li>Document all findings in SharePoint</li>
        </ul>
      </div>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>Automated Complaint Management System | SOP MK-03-02.01</p>
    </div>
  </div>
  </body>
  </html>
  ```
- **Importance:** High

---

## Step 16: Send Email - Customer Acknowledgment

**Action:** Send an email (V2)
- **To:** `@{triggerBody()?['from']}`
- **Subject:** `Complaint Received - @{outputs('Compose_-_Complaint_Number')}`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #0066cc 0%, #004999 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .logo { font-size: 36px; font-weight: bold; margin-bottom: 10px; }
  .content { background: #fff; padding: 30px; border: 1px solid #dee2e6; border-top: none; border-radius: 0 0 10px 10px; }
  .info-box { background: #e7f3ff; border-left: 4px solid #0066cc; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <div class="logo">Pulse Pharmaceuticals</div>
      <p style="margin: 0; font-size: 14px;">Customer Care Excellence</p>
    </div>
    <div class="content">
      <h2 style="color: #0066cc; margin-top: 0;">Thank You for Contacting Us</h2>
      
      <p>Dear Valued Customer,</p>
      
      <p>We have received your complaint/feedback and want to assure you that we take your concerns seriously.</p>
      
      <div class="info-box">
        <strong>Your complaint has been registered with the following details:</strong>
      </div>
      
      <div class="details">
        <p><span class="label">Reference Number:</span><span class="value">@{outputs('Compose_-_Complaint_Number')}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy HH:mm')}</span></p>
        <p><span class="label">Type:</span><span class="value">@{variables('DetectedType')}</span></p>
        <p><span class="label">Department:</span><span class="value">@{variables('DetectedDepartment')}</span></p>
        <p><span class="label">Priority:</span><span class="value">@{outputs('Compose_-_Determine_Priority')}</span></p>
      </div>
      
      <div class="info-box">
        <strong>What happens next?</strong>
        <ul style="margin: 10px 0;">
          <li>Our @{variables('DetectedDepartment')} team will review your complaint</li>
          <li>We aim to provide an initial response within 2 working days</li>
          <li>Full resolution is targeted within 5 working days</li>
          <li>You will receive updates on the progress</li>
        </ul>
      </div>
      
      <p><strong>Please keep your reference number (@{outputs('Compose_-_Complaint_Number')}) for future correspondence.</strong></p>
      
      <p>If you have any additional information or questions, please reply to this email or contact us at:</p>
      <p>📧 complaints@tpg.co.zw<br>
      📞 [Your phone number]</p>
      
      <p>Thank you for your patience and for giving us the opportunity to address your concerns.</p>
      
      <p>Best regards,<br>
      <strong>Customer Service Team</strong><br>
      Pulse Pharmaceuticals (Private) Limited</p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>Committed to Quality Healthcare Solutions</p>
    </div>
  </div>
  </body>
  </html>
  ```

---

## Step 17: Send Email - Department Assignment

**Action:** Send an email (V2)
- **To:** `@{variables('ResponsibleEmail')}`
- **Cc:** `jkaseke@tpg.co.zw`
- **Subject:** `New Complaint Assigned - @{outputs('Compose_-_Complaint_Number')} [@{outputs('Compose_-_Determine_Priority')}]`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #28a745 0%, #218838 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .content { background: #fff; padding: 30px; border: 1px solid #dee2e6; border-top: none; border-radius: 0 0 10px 10px; }
  .priority-high { background: #fff3cd; border-left: 4px solid #ffc107; padding: 15px; margin: 20px 0; }
  .priority-critical { background: #f8d7da; border-left: 4px solid #dc3545; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .button { background: #28a745; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <h1 style="margin: 0;">New Complaint Assigned</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">Action Required</p>
    </div>
    <div class="content">
      <p>Dear @{variables('DetectedDepartment')} Team,</p>
      
      <p>A new customer complaint has been assigned to your department for resolution.</p>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{outputs('Compose_-_Complaint_Number')}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy HH:mm')}</span></p>
        <p><span class="label">Customer:</span><span class="value">@{triggerBody()?['from']}</span></p>
        <p><span class="label">Type:</span><span class="value">@{variables('DetectedType')}</span></p>
        <p><span class="label">Priority:</span><span class="value">@{outputs('Compose_-_Determine_Priority')}</span></p>
        <p><span class="label">Department:</span><span class="value">@{variables('DetectedDepartment')}</span></p>
      </div>
      
      <div style="margin: 20px 0;">
        <p class="label">Complaint Details:</p>
        <div style="background: #f8f9fa; padding: 15px; border-radius: 5px; margin-top: 10px;">
          @{triggerBody()?['bodyPreview']}
        </div>
      </div>
      
      <div class="priority-@{toLower(outputs('Compose_-_Determine_Priority'))}">
        <strong>SLA Requirements (SOP MK-03-02.01):</strong>
        <ul style="margin: 10px 0;">
          <li>Initial response: 2 working days</li>
          <li>Full resolution: 5 working days</li>
          <li>Customer has been notified and is awaiting your response</li>
        </ul>
      </div>
      
      <div style="text-align: center; margin: 30px 0;">
        <a href="https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final/Lists/Customer%20Feedback%20and%20Complaints%20Final/AllItems.aspx" class="button">
          Open in SharePoint
        </a>
      </div>
      
      <p><strong>Next Steps:</strong></p>
      <ol>
        <li>Review the complaint details in SharePoint</li>
        <li>Assign to a team member using the "AssignedTo" field</li>
        <li>Update the Status as you progress</li>
        <li>Document your resolution in the "ResolutionSummary" field</li>
        <li>Change Status to "Resolved" when complete</li>
      </ol>
      
      <p>Thank you for your prompt attention to this matter.</p>
      
      <p>Best regards,<br>
      <strong>Automated Complaint Management System</strong></p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>SOP MK-03-02.01 | Customer Complaint Management</p>
    </div>
  </div>
  </body>
  </html>
  ```

---

## Notes

### Why Send HTTP Request Instead of Create Item?
The standard "Create item" action does NOT show SharePoint columns when the trigger is email-based. Using "Send an HTTP request to SharePoint" bypasses this limitation and has built-in authentication.

### List Item Type Name
The `__metadata.type` value must match your SharePoint list's internal name:
- Format: `SP.Data.[ListName]ListItem`
- Spaces become `_x0020_`
- Example: `Customer Feedback and Complaints Final` → `SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem`

### Testing Keywords
Test the keyword detection with emails containing:
- "quality issue" → Product Quality
- "adverse reaction" → Adverse Event
- "delivery delay" → Delivery - Local
- "account statement" → Account Services
- Generic text → Query - General

### Troubleshooting
- If attachments fail: Check file size limits (10MB default)
- If HTTP request fails: Verify list name and site URL
- If emails not triggering: Check shared mailbox permissions
- If type detection wrong: Add more keywords to Compose expression
