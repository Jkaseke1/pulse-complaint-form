# Flow 4: Complaint Resolution and Closure

## Flow Name
`Customer Complaints - Resolution and Closure`

## Trigger
**When an item is modified**
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **List Name:** `Customer Feedback and Complaints Final`

---

## Step 1: Get Item Details

**Action:** Get item
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **List Name:** `Customer Feedback and Complaints Final`
- **Id:** `triggerOutputs()?['body/ID']`

**Rename to:** `Get - Complaint Details`

**Note:** This ensures we have all current field values including Choice fields with proper Value extraction.

---

## Step 2: Condition - Check if Status Changed to Resolved

**Action:** Condition
- **Condition:** `outputs('Get_-_Complaint_Details')?['body/Status']?['Value']`
- **Operator:** `is equal to`
- **Value:** `Resolved`

**Rename to:** `Condition - Status = Resolved?`

---

### If Yes Branch (Status = Resolved):

#### Step 2.1: Send Email - Resolution Notification with Satisfaction Buttons

**Action:** Send an email (V2)
- **To:** `@{outputs('Get_-_Complaint_Details')?['body/CustomerEmail']}`
- **Subject:** `Your Complaint Has Been Resolved - @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #28a745 0%, #20c997 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .success-icon { font-size: 48px; margin-bottom: 10px; }
  .content { background: #fff; padding: 30px; border: 1px solid #28a745; border-top: none; border-radius: 0 0 10px 10px; }
  .success-box { background: #d4edda; border-left: 4px solid #28a745; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .resolution { background: #e7f3ff; padding: 20px; border-radius: 5px; margin: 20px 0; border-left: 4px solid #0066cc; }
  .feedback-section { background: #fff3cd; padding: 20px; border-radius: 5px; margin: 30px 0; text-align: center; }
  .feedback-buttons { margin: 20px 0; }
  .button-yes { background: #28a745; color: white; padding: 15px 40px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold; margin: 0 10px; font-size: 16px; }
  .button-no { background: #dc3545; color: white; padding: 15px 40px; text-decoration: none; border-radius: 5px; display: inline-block; font-weight: bold; margin: 0 10px; font-size: 16px; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <div class="success-icon">✅</div>
      <h1 style="margin: 0;">Complaint Resolved</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">We've addressed your concern</p>
    </div>
    <div class="content">
      <p>Dear @{outputs('Get_-_Complaint_Details')?['body/CustomerName']},</p>
      
      <div class="success-box">
        <strong>✓ Good news! Your complaint has been resolved.</strong>
        <p style="margin: 10px 0 0 0;">Our team has completed the investigation and taken appropriate action to address your concern.</p>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(outputs('Get_-_Complaint_Details')?['body/DateReceived'],'dd MMM yyyy')}</span></p>
        <p><span class="label">Date Resolved:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy')}</span></p>
        <p><span class="label">Type:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/Type']?['Value']}</span></p>
        <p><span class="label">Handled By:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/DepartmentResponsible']?['Value']}</span></p>
      </div>
      
      <div class="resolution">
        <h3 style="margin-top: 0; color: #0066cc;">Resolution Summary</h3>
        <p>@{outputs('Get_-_Complaint_Details')?['body/ResolutionSummary']}</p>
      </div>
      
      <div class="feedback-section">
        <h3 style="margin-top: 0; color: #856404;">📋 We Value Your Feedback</h3>
        <p style="margin: 15px 0;"><strong>Are you satisfied with how we resolved your complaint?</strong></p>
        <p style="font-size: 14px; color: #856404; margin-bottom: 20px;">Your feedback helps us improve our service quality.</p>
        
        <div class="feedback-buttons">
          <a href="mailto:complaints@tpg.co.zw?subject=Satisfied - @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}&body=Yes, I am satisfied with the resolution. Thank you!" class="button-yes">
            👍 Yes, I'm Satisfied
          </a>
          <a href="mailto:complaints@tpg.co.zw?subject=Not Satisfied - @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}&body=No, I am not satisfied. Here's why:%0D%0A%0D%0A[Please explain your concerns]" class="button-no">
            👎 No, I'm Not Satisfied
          </a>
        </div>
        
        <p style="font-size: 12px; color: #856404; margin-top: 15px;">Click a button above to send your feedback via email, or reply directly to this message.</p>
      </div>
      
      <div class="success-box">
        <strong>What happens next?</strong>
        <ul style="margin: 10px 0;">
          <li>If you're satisfied, we'll close this complaint</li>
          <li>If you're not satisfied, we'll reopen and reassign for further review</li>
          <li>If we don't hear from you within 3 days, we'll assume you're satisfied and close the complaint</li>
        </ul>
      </div>
      
      <p>If you have any additional questions or concerns, please don't hesitate to contact us:</p>
      <p>📧 complaints@tpg.co.zw<br>
      📞 [Your phone number]</p>
      
      <p>Thank you for bringing this matter to our attention and giving us the opportunity to make things right.</p>
      
      <p>Best regards,<br>
      <strong>Customer Service Team</strong><br>
      Pulse Pharmaceuticals (Private) Limited</p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>Committed to Quality Healthcare Solutions</p>
      <p>Reference: @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}</p>
    </div>
  </div>
  </body>
  </html>
  ```

---

## Step 3: Condition - Check if Status Changed to Closed

**Action:** Condition
- **Condition:** `outputs('Get_-_Complaint_Details')?['body/Status']?['Value']`
- **Operator:** `is equal to`
- **Value:** `Closed`

**Rename to:** `Condition - Status = Closed?`

---

### If Yes Branch (Status = Closed):

#### Step 3.1: Send HTTP Request - Update Closed Date

**Action:** Send an HTTP request to SharePoint
- **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- **Method:** `POST`
- **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items(@{triggerOutputs()?['body/ID']})`
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
    "FeedbackClosedDate": "@{utcNow()}"
  }
  ```

**Rename to:** `HTTP - Update Closed Date`

---

#### Step 3.2: Send Email - Closure Confirmation

**Action:** Send an email (V2)
- **To:** `@{outputs('Get_-_Complaint_Details')?['body/CustomerEmail']}`
- **Cc:** `@{outputs('Get_-_Complaint_Details')?['body/ResponsiblePerson/EMail']};jkaseke@tpg.co.zw`
- **Subject:** `Complaint Closed - @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']} - Thank You`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #6f42c1 0%, #5a32a3 100%); color: white; padding: 30px; text-align: center; border-radius: 10px 10px 0 0; }
  .complete-icon { font-size: 48px; margin-bottom: 10px; }
  .content { background: #fff; padding: 30px; border: 1px solid #6f42c1; border-top: none; border-radius: 0 0 10px 10px; }
  .closure-box { background: #e7e3f5; border-left: 4px solid #6f42c1; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .thank-you { background: linear-gradient(135deg, #e7e3f5 0%, #d4edda 100%); padding: 20px; border-radius: 5px; margin: 20px 0; text-align: center; }
  .stats { display: table; width: 100%; margin: 20px 0; }
  .stat-item { display: table-cell; text-align: center; padding: 15px; background: #f8f9fa; border-radius: 5px; }
  .stat-number { font-size: 32px; font-weight: bold; color: #6f42c1; }
  .stat-label { font-size: 12px; color: #6c757d; margin-top: 5px; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <div class="complete-icon">🎉</div>
      <h1 style="margin: 0;">Complaint Closed</h1>
      <p style="margin: 10px 0 0 0; font-size: 14px;">Case Successfully Resolved</p>
    </div>
    <div class="content">
      <p>Dear @{outputs('Get_-_Complaint_Details')?['body/CustomerName']},</p>
      
      <div class="closure-box">
        <strong>✓ Your complaint has been officially closed.</strong>
        <p style="margin: 10px 0 0 0;">Thank you for your patience and for giving us the opportunity to address your concern.</p>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(outputs('Get_-_Complaint_Details')?['body/DateReceived'],'dd MMM yyyy')}</span></p>
        <p><span class="label">Date Closed:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy')}</span></p>
        <p><span class="label">Type:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/Type']?['Value']}</span></p>
        <p><span class="label">Final Status:</span><span class="value" style="color: #28a745; font-weight: bold;">Closed</span></p>
      </div>
      
      <div class="stats">
        <div class="stat-item" style="margin-right: 10px;">
          <div class="stat-number">@{div(sub(ticks(utcNow()),ticks(outputs('Get_-_Complaint_Details')?['body/DateReceived'])),864000000000)}</div>
          <div class="stat-label">Days to Resolution</div>
        </div>
        <div class="stat-item" style="margin-left: 10px;">
          <div class="stat-number">@{if(equals(outputs('Get_-_Complaint_Details')?['body/CustomerSatisfied']?['Value'],'Yes'),'✓','—')}</div>
          <div class="stat-label">Customer Satisfied</div>
        </div>
      </div>
      
      <div class="thank-you">
        <h3 style="margin-top: 0; color: #6f42c1;">🙏 Thank You for Your Feedback</h3>
        <p style="margin: 10px 0;">Your input helps us continuously improve our products and services. We truly appreciate your business and the trust you place in Pulse Pharmaceuticals.</p>
      </div>
      
      <div class="closure-box">
        <strong>Case Summary:</strong>
        <p style="margin: 10px 0 0 0;">@{outputs('Get_-_Complaint_Details')?['body/ResolutionSummary']}</p>
      </div>
      
      <p><strong>This complaint is now closed.</strong> If you experience any further issues or have additional concerns, please don't hesitate to contact us again:</p>
      
      <p>📧 complaints@tpg.co.zw<br>
      📞 [Your phone number]<br>
      🌐 [Your website]</p>
      
      <p>We look forward to continuing to serve you with excellence.</p>
      
      <p>Warm regards,<br>
      <strong>Customer Service Team</strong><br>
      Pulse Pharmaceuticals (Private) Limited</p>
    </div>
    <div class="footer">
      <p><strong>Pulse Pharmaceuticals (Private) Limited</strong></p>
      <p>Committed to Quality Healthcare Solutions</p>
      <p>Reference: @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']} | Closed: @{formatDateTime(utcNow(),'dd MMM yyyy')}</p>
    </div>
  </div>
  </body>
  </html>
  ```

---

#### Step 3.3: Send Email - Internal Closure Notification

**Action:** Send an email (V2)
- **To:** `@{outputs('Get_-_Complaint_Details')?['body/ResponsiblePerson/EMail']}`
- **Cc:** `jkaseke@tpg.co.zw`
- **Subject:** `✓ Complaint Closed - @{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}`
- **Body:**
  ```html
  <html>
  <head>
  <style>
  body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; }
  .container { max-width: 600px; margin: 0 auto; padding: 20px; }
  .header { background: linear-gradient(135deg, #28a745 0%, #20c997 100%); color: white; padding: 20px; text-align: center; border-radius: 10px 10px 0 0; }
  .content { background: #fff; padding: 30px; border: 1px solid #28a745; border-top: none; border-radius: 0 0 10px 10px; }
  .success-box { background: #d4edda; border-left: 4px solid #28a745; padding: 15px; margin: 20px 0; }
  .details { background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 15px 0; }
  .label { font-weight: bold; color: #495057; }
  .value { color: #212529; margin-left: 10px; }
  .metrics { background: #e7f3ff; padding: 15px; border-radius: 5px; margin: 20px 0; }
  .footer { text-align: center; margin-top: 20px; padding: 20px; color: #6c757d; font-size: 12px; }
  </style>
  </head>
  <body>
  <div class="container">
    <div class="header">
      <h2 style="margin: 0;">✓ Complaint Successfully Closed</h2>
    </div>
    <div class="content">
      <p>Dear @{outputs('Get_-_Complaint_Details')?['body/DepartmentResponsible']?['Value']} Team,</p>
      
      <div class="success-box">
        <strong>✓ Well done! The following complaint has been successfully closed.</strong>
      </div>
      
      <div class="details">
        <p><span class="label">Complaint Number:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/ComplaintNo']}</span></p>
        <p><span class="label">Customer:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/CustomerName']}</span></p>
        <p><span class="label">Type:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/Type']?['Value']}</span></p>
        <p><span class="label">Date Received:</span><span class="value">@{formatDateTime(outputs('Get_-_Complaint_Details')?['body/DateReceived'],'dd MMM yyyy')}</span></p>
        <p><span class="label">Date Closed:</span><span class="value">@{formatDateTime(utcNow(),'dd MMM yyyy')}</span></p>
        <p><span class="label">Customer Satisfaction:</span><span class="value">@{outputs('Get_-_Complaint_Details')?['body/CustomerSatisfied']?['Value']}</span></p>
      </div>
      
      <div class="metrics">
        <p class="label">Performance Metrics:</p>
        <ul style="margin: 10px 0;">
          <li><strong>Resolution Time:</strong> @{div(sub(ticks(utcNow()),ticks(outputs('Get_-_Complaint_Details')?['body/DateReceived'])),864000000000)} days</li>
          <li><strong>SLA Compliance:</strong> @{if(lessOrEquals(div(sub(ticks(utcNow()),ticks(outputs('Get_-_Complaint_Details')?['body/DateReceived'])),864000000000),5),'✓ Within SLA (5 days)','✗ SLA Exceeded')}</li>
          <li><strong>Priority Level:</strong> @{outputs('Get_-_Complaint_Details')?['body/Priority']?['Value']}</li>
        </ul>
      </div>
      
      <p><strong>Resolution Summary:</strong></p>
      <div style="background: #f8f9fa; padding: 15px; border-radius: 5px; margin: 10px 0;">
        @{outputs('Get_-_Complaint_Details')?['body/ResolutionSummary']}
      </div>
      
      <p>The customer has been notified of the closure. This case is now complete and archived in SharePoint.</p>
      
      <p>Thank you for your diligent work in resolving this complaint and maintaining our commitment to customer satisfaction.</p>
      
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

## Summary

This flow triggers when any item is modified in the SharePoint list and:

1. **When Status = "Resolved":**
   - Sends resolution notification to customer
   - Includes satisfaction feedback buttons (Yes/No)
   - Buttons use mailto links to send feedback via email
   - Displays resolution summary

2. **When Status = "Closed":**
   - Updates FeedbackClosedDate field automatically
   - Sends closure confirmation to customer
   - Sends internal closure notification to department
   - Includes performance metrics (days to resolution, SLA compliance)
   - Displays customer satisfaction status

## Satisfaction Feedback Mechanism

The satisfaction buttons use **mailto links** that:
- Open customer's email client
- Pre-fill subject with complaint number
- Pre-fill body with satisfaction response
- Send to complaints@tpg.co.zw

When customer clicks:
- **"Yes, I'm Satisfied"** → Sends positive feedback email
- **"No, I'm Not Satisfied"** → Sends negative feedback email with space for explanation

You can then create a **Flow 5** (optional) to:
- Monitor complaints@tpg.co.zw for satisfaction emails
- Parse subject line for complaint number
- Update CustomerSatisfied field in SharePoint
- If "Not Satisfied", reopen complaint and reassign

## Testing

To test this flow:
1. Create a test complaint in SharePoint
2. Update Status to "Resolved" and add ResolutionSummary
3. Verify resolution email is sent to customer
4. Update Status to "Closed"
5. Verify FeedbackClosedDate is populated
6. Verify closure emails are sent

## Notes

- Flow uses "Get item" to ensure all field values are current
- Choice fields extracted using `?['Value']` syntax
- FeedbackClosedDate updated via HTTP request (more reliable than Update item)
- Days to resolution calculated using ticks
- SLA compliance automatically determined (5 days or less = compliant)
- All emails are HTML formatted with professional styling
- Internal notifications include performance metrics
