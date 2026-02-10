# Flow 3: Daily Complaint Reminder and Escalation

## Flow Name
`Customer Complaints - Daily Reminders and Escalation`

## Trigger
**Recurrence**
- Frequency: Day
- Interval: 1
- Time Zone: (UTC+02:00) Harare, Pretoria
- At these hours: 8
- At these minutes: 0
- On these days: Monday, Tuesday, Wednesday, Thursday, Friday

---

## Step 1: Send HTTP Request - Get Open Complaints

**Action:** Send an HTTP request to SharePoint
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **Method:** `GET`
- **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items?$filter=(Status ne 'Closed') and (Status ne 'Resolved')&$select=Id,ComplaintNo,DateReceived,CustomerName,CustomerEmail,Type,DepartmentResponsible,ResponsiblePerson/EMail,Status,Priority,Description&$expand=ResponsiblePerson`
- **Headers:**
  ```json
  {
    "Accept": "application/json;odata=verbose"
  }
  ```

**Rename to:** `HTTP - Get Open Complaints`

---

## Step 2: Parse JSON - Open Complaints

**Action:** Parse JSON
- **Content:** `body('HTTP_-_Get_Open_Complaints')`
- **Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "d": {
        "type": "object",
        "properties": {
          "results": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "Id": {
                  "type": "integer"
                },
                "ComplaintNo": {
                  "type": "string"
                },
                "DateReceived": {
                  "type": "string"
                },
                "CustomerName": {
                  "type": "string"
                },
                "CustomerEmail": {
                  "type": "string"
                },
                "Type": {
                  "type": "string"
                },
                "DepartmentResponsible": {
                  "type": "string"
                },
                "Status": {
                  "type": "string"
                },
                "Priority": {
                  "type": "string"
                },
                "Description": {
                  "type": "string"
                },
                "ResponsiblePerson": {
                  "type": "object",
                  "properties": {
                    "EMail": {
                      "type": "string"
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
  ```

**Rename to:** `Parse - Open Complaints`

---

## Step 3: Apply to Each - Process Each Complaint

**Action:** Apply to each
- **Select output from previous steps:** `body('Parse_-_Open_Complaints')?['d']?['results']`

**Rename to:** `Apply to each - Process Complaint`

### Inside Apply to Each:

---

### Step 3.1: Compose - Calculate Days Open

**Action:** Compose

**Inputs Expression:**
```
div(sub(ticks(utcNow()),ticks(items('Apply_to_each_-_Process_Complaint')?['DateReceived'])),864000000000)
```

**Rename to:** `Compose - Days Open`

**Note:** This calculates the number of days between DateReceived and now.
- 1 day = 864000000000 ticks

---

### Step 3.2: Condition - Check if 3+ Days Open

**Action:** Condition
- **Condition:** `outputs('Compose_-_Days_Open')`
- **Operator:** `is greater than or equal to`
- **Value:** `3`

**Rename to:** `Condition - 3+ Days?`

---

#### If Yes Branch (3+ Days):

##### Step 3.2.1: Condition - Check if 5+ Days (Escalation)

**Action:** Condition
- **Condition:** `outputs('Compose_-_Days_Open')`
- **Operator:** `is greater than or equal to`
- **Value:** `5`

**Rename to:** `Condition - 5+ Days Escalation?`

---

###### If Yes Branch (5+ Days - ESCALATION):

####### Send Email - Escalation to MD/HR

**Action:** Send an email (V2)
- **To:** `md@tpg.co.zw;hr@tpg.co.zw`
- **Cc:** `@{items('Apply_to_each_-_Process_Complaint')?['ResponsiblePerson']?['EMail']};jkaseke@tpg.co.zw`
- **Subject:** `⚠️ ESCALATION: Complaint Overdue - @{items('Apply_to_each_-_Process_Complaint')?['ComplaintNo']} (Day @{outputs('Compose_-_Days_Open')})`
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
  .warning-box { background: #fff3cd; border-left: 4px solid #ffc107; padding: 15px; margin: 20px 0; }
  .escalation-box { background: #f8d7da; border-left: 4px solid #dc3545; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .button { background: #dc3545; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  .days-overdue { color: #dc3545; font-size: 24px; font-weight: bold; text-align: center; margin: 20px 0; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <div class="alert-icon">🚨</div>
      <h1 style="margin: 0;">COMPLAINT ESCALATION</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">SLA Breach - Management Attention Required</p>
    </div>
    <div class="content">
      <div class="days-overdue">
        ⏰ DAY @{outputs('Compose_-_Days_Open')} - SLA EXCEEDED
      </div>
      
      <div class="escalation-box">
        <strong>⚠️ This complaint has exceeded the 5-day resolution SLA per SOP MK-03-02.01</strong>
        <p style="margin: 10px 0 0 0;">Immediate management intervention is required to resolve this matter and prevent customer dissatisfaction.</p>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['ComplaintNo']}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(items('Apply_to_each_-_Process_Complaint')?['DateReceived'],'dd MMM yyyy')}</span></p>
        <p><span class="label">Days Open:</span><span class="value" style="color: #dc3545; font-weight: bold;">@{outputs('Compose_-_Days_Open')} days</span></p>
        <p><span class="label">Customer:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['CustomerName']}</span></p>
        <p><span class="label">Type:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Type']}</span></p>
        <p><span class="label">Priority:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Priority']}</span></p>
        <p><span class="label">Department:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['DepartmentResponsible']}</span></p>
        <p><span class="label">Current Status:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Status']}</span></p>
      </div>
      
      <div style="margin: 20px 0;">
        <p class="label">Complaint Details:</p>
        <div style="background: #f8f9fa; padding: 15px; border-radius: 5px; margin-top: 10px;">
          @{items('Apply_to_each_-_Process_Complaint')?['Description']}
        </div>
      </div>
      
      <div class="warning-box">
        <strong>Impact:</strong>
        <ul style="margin: 10px 0;">
          <li>Customer satisfaction at risk</li>
          <li>Potential regulatory implications</li>
          <li>Brand reputation impact</li>
          <li>SLA compliance breach</li>
        </ul>
      </div>
      
      <div style="text-align: center; margin: 30px 0;">
        <a href="https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final/Lists/Customer%20Feedback%20and%20Complaints%20Final/DispForm.aspx?ID=@{items('Apply_to_each_-_Process_Complaint')?['Id']}" class="button">
          View Complaint Details
        </a>
      </div>
      
      <div class="escalation-box">
        <strong>Required Actions:</strong>
        <ol style="margin: 10px 0;">
          <li>Review complaint status and progress immediately</li>
          <li>Engage with responsible department manager</li>
          <li>Identify and remove blockers to resolution</li>
          <li>Provide direct customer communication if needed</li>
          <li>Update SharePoint with escalation notes</li>
        </ol>
      </div>
      
      <p style="margin-top: 30px;"><strong>This is an automated escalation notification.</strong> The responsible department has been copied on this email.</p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>Automated Complaint Management System | SOP MK-03-02.01</p>
      <p>Daily Escalation Report - @{formatDateTime(utcNow(),'dd MMM yyyy')}</p>
    </div>
  </div>
  </body>
  </html>
  ```
- **Importance:** High

---

###### If No Branch (3-4 Days - REMINDER):

####### Send Email - Reminder to Department

**Action:** Send an email (V2)
- **To:** `@{items('Apply_to_each_-_Process_Complaint')?['ResponsiblePerson']?['EMail']}`
- **Cc:** `jkaseke@tpg.co.zw`
- **Subject:** `⏰ Reminder: Complaint Pending - @{items('Apply_to_each_-_Process_Complaint')?['ComplaintNo']} (Day @{outputs('Compose_-_Days_Open')})`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #ffc107 0%, #ff9800 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .content { background: #fff; padding: 30px; border: 1px solid #ffc107; border-top: none; border-radius: 0 0 10px 10px; }
  .reminder-box { background: #fff3cd; border-left: 4px solid #ffc107; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .button { background: #ffc107; color: #212529; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  .progress-bar { background: #e9ecef; height: 30px; border-radius: 5px; margin: 20px 0; position: relative; }
  .progress-fill { background: linear-gradient(90deg, #28a745 0%, #ffc107 60%, #dc3545 100%); height: 100%; border-radius: 5px; width: @{mul(div(outputs('Compose_-_Days_Open'),5),100)}%; }
  .progress-text { position: absolute; width: 100%; text-align: center; line-height: 30px; font-weight: bold; color: #212529; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <h1 style="margin: 0;">⏰ Complaint Reminder</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">Action Required</p>
    </div>
    <div class="content">
      <p>Dear @{items('Apply_to_each_-_Process_Complaint')?['DepartmentResponsible']} Team,</p>
      
      <p>This is a friendly reminder that the following complaint is still pending resolution.</p>
      
      <div class="reminder-box">
        <strong>📅 Day @{outputs('Compose_-_Days_Open')} of 5-day SLA</strong>
        <p style="margin: 10px 0 0 0;">Please prioritize this complaint to ensure timely resolution per SOP MK-03-02.01.</p>
      </div>
      
      <div class="progress-bar">
        <div class="progress-fill"></div>
        <div class="progress-text">Day @{outputs('Compose_-_Days_Open')} / 5</div>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['ComplaintNo']}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(items('Apply_to_each_-_Process_Complaint')?['DateReceived'],'dd MMM yyyy')}</span></p>
        <p><span class="label">Days Open:</span><span class="value" style="color: #ffc107; font-weight: bold;">@{outputs('Compose_-_Days_Open')} days</span></p>
        <p><span class="label">Customer:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['CustomerName']}</span></p>
        <p><span class="label">Type:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Type']}</span></p>
        <p><span class="label">Priority:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Priority']}</span></p>
        <p><span class="label">Current Status:</span><span class="value">@{items('Apply_to_each_-_Process_Complaint')?['Status']}</span></p>
      </div>
      
      <div style="margin: 20px 0;">
        <p class="label">Complaint Details:</p>
        <div style="background: #f8f9fa; padding: 15px; border-radius: 5px; margin-top: 10px;">
          @{items('Apply_to_each_-_Process_Complaint')?['Description']}
        </div>
      </div>
      
      <div style="text-align: center; margin: 30px 0;">
        <a href="https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final/Lists/Customer%20Feedback%20and%20Complaints%20Final/DispForm.aspx?ID=@{items('Apply_to_each_-_Process_Complaint')?['Id']}" class="button">
          Update Complaint Status
        </a>
      </div>
      
      <div class="reminder-box">
        <strong>⚠️ Important:</strong>
        <ul style="margin: 10px 0;">
          <li>Day 5: Complaint will be escalated to MD/HR</li>
          <li>Please update the Status field as you progress</li>
          <li>Document your actions in SharePoint</li>
          <li>Contact customer if additional information is needed</li>
        </ul>
      </div>
      
      <p>If you need assistance or there are blockers preventing resolution, please escalate to your manager immediately.</p>
      
      <p>Thank you for your attention to this matter.</p>
      
      <p>Best regards,<br>
      <strong>Automated Complaint Management System</strong></p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>SOP MK-03-02.01 | Customer Complaint Management</p>
      <p>Daily Reminder - @{formatDateTime(utcNow(),'dd MMM yyyy')}</p>
    </div>
  </div>
  </body>
  </html>
  ```

---

## Summary

This flow runs every weekday at 8:00 AM and:
1. Gets all open complaints (Status ≠ Closed and Status ≠ Resolved)
2. Calculates days open for each complaint
3. Sends reminder emails for complaints open 3+ days
4. Sends escalation emails to MD/HR for complaints open 5+ days
5. Includes progress bar visualization showing SLA timeline

## Testing

To test this flow:
1. Create a test complaint with DateReceived set to 3 days ago
2. Run the flow manually (Test → Manually)
3. Verify reminder email is sent
4. Change DateReceived to 5 days ago
5. Run again and verify escalation email is sent

## Notes

- Flow only runs on weekdays (Monday-Friday)
- Excludes complaints with Status = "Closed" or "Resolved"
- Days calculation uses ticks (864000000000 ticks = 1 day)
- Progress bar shows visual representation of SLA timeline
- All emails are HTML formatted with professional styling
- Escalation emails have high importance flag
