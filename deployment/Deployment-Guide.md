# Deployment Guide - Pulse Pharmaceuticals Complaint Management System

## Overview
This guide walks you through deploying all four Power Automate flows and the public complaint form for the complete complaint management system.

---

## Prerequisites

### Required Access
- ✅ SharePoint site access: `Pulse-Intranet-Final`
- ✅ SharePoint list: `Customer Feedback and Complaints Final` (already created)
- ✅ Power Automate license (included with Office 365)
- ✅ Shared mailbox access: `complaints@tpg.co.zw`
- ✅ Email permissions to send as system

### Required Information
- SharePoint site URL: `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- Department email addresses (see Department Routing Map)
- Escalation contacts: MD, HR, Regulatory emails

---

## Phase 1: Verify SharePoint List Structure

### Step 1.1: Check List Exists
1. Navigate to SharePoint site
2. Go to **Site Contents**
3. Verify list: `Customer Feedback and Complaints Final`

### Step 1.2: Verify Columns
Open the list and check **List settings** → **Columns**:

**Required columns:**
- ComplaintNo (Single line of text)
- DateReceived (Date and time)
- Category (Choice: Complaint, Query)
- Type (Choice: 10 options - see SharePoint-List-Structure.md)
- CustomerName (Single line of text)
- CustomerEmail (Single line of text)
- CustomerPhone (Single line of text)
- ProductName (Single line of text)
- BatchNumber (Single line of text)
- ExpiryDate (Date and time)
- Description (Multiple lines of text)
- DepartmentResponsible (Choice: 8 departments)
- ResponsiblePerson (Person)
- AssignedTo (Person)
- Status (Choice: Open, Under Review, Awaiting Response, Resolved, Closed)
- Priority (Choice: Low, Medium, High, Critical)
- ResolutionSummary (Multiple lines of text)
- FeedbackClosedDate (Date and time)
- Evidence (Attachments - enabled)
- DaysOpen (Calculated)
- CustomerSatisfied (Choice: Yes, No, Pending)

### Step 1.3: Enable Attachments
1. List settings → Advanced settings
2. Attachments: **Enabled**
3. Save

---

## Phase 2: Deploy Flow 1 (Already Complete ✅)

**Status:** This flow is already working according to your notes.

**Verification:**
1. Go to https://make.powerautomate.com
2. Find flow: `Customer Complaint - Acknowledgment & Assignment`
3. Verify status: **On**
4. Test by creating a manual SharePoint item

---

## Phase 3: Deploy Flow 2 - Email Intake

### Step 3.1: Create New Flow
1. Go to https://make.powerautomate.com
2. Click **+ Create** → **Automated cloud flow**
3. Name: `Customer Complaints - Email Intake`
4. Skip trigger selection
5. Click **Create**

### Step 3.2: Configure Trigger
1. Search: **When a new email arrives in a shared mailbox (V2)**
2. Select it
3. Configure:
   - **Original Mailbox Address:** `complaints@tpg.co.zw`
   - **Folder:** Inbox
   - **Include Attachments:** Yes
   - **Only with Attachments:** No
   - **Importance:** All

### Step 3.3: Add Actions
Follow the complete configuration in: `flows/Flow2-Email-Intake-Configuration.md`

**Key actions:**
1. Initialize 3 variables (DetectedType, DetectedDepartment, ResponsibleEmail)
2. Compose - Detect Type from Email (keyword detection)
3. Compose - Route to Department
4. Compose - Get Responsible Email
5. Compose - Determine Priority
6. Compose - Determine Category
7. **Send HTTP Request** - Create SharePoint item (not "Create item")
8. Parse JSON - Get created item ID
9. Compose - Generate complaint number
10. Send HTTP Request - Update complaint number
11. Condition - Process attachments if present
12. Condition - Send adverse event alert if applicable
13. Send Email - Customer acknowledgment
14. Send Email - Department assignment

### Step 3.4: Critical Configuration Notes

**HTTP Request for Creating Item:**
- Site Address: `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
- Method: `POST`
- Uri: `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items`
- Headers:
  ```json
  {
    "Accept": "application/json;odata=verbose",
    "Content-Type": "application/json;odata=verbose"
  }
  ```

**List Item Type:**
The `__metadata.type` must be: `SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem`

### Step 3.5: Test Flow 2
1. Send test email to `complaints@tpg.co.zw`
2. Subject: "Product quality issue"
3. Body: "I received a defective product"
4. Verify:
   - Flow triggers
   - SharePoint item created
   - Complaint number generated
   - Emails sent

---

## Phase 4: Deploy Flow 3 - Daily Reminders

### Step 4.1: Create New Flow
1. Go to https://make.powerautomate.com
2. Click **+ Create** → **Scheduled cloud flow**
3. Name: `Customer Complaints - Daily Reminders and Escalation`
4. Configure recurrence:
   - **Starting:** Tomorrow at 8:00 AM
   - **Repeat every:** 1 Day
   - **Time zone:** (UTC+02:00) Harare, Pretoria
   - **At these hours:** 8
   - **At these minutes:** 0
   - **On these days:** Monday, Tuesday, Wednesday, Thursday, Friday
5. Click **Create**

### Step 4.2: Add Actions
Follow the complete configuration in: `flows/Flow3-Daily-Reminders-Configuration.md`

**Key actions:**
1. Send HTTP Request - Get open complaints (GET request with filter)
2. Parse JSON - Open complaints response
3. Apply to each - Process each complaint
   - Compose - Calculate days open
   - Condition - Check if 3+ days
     - If yes:
       - Condition - Check if 5+ days (escalation)
         - If yes: Send escalation email to MD/HR
         - If no: Send reminder email to department

### Step 4.3: HTTP Request for Getting Items

**Configuration:**
- Method: `GET`
- Uri: `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items?$filter=(Status ne 'Closed') and (Status ne 'Resolved')&$select=Id,ComplaintNo,DateReceived,CustomerName,CustomerEmail,Type,DepartmentResponsible,ResponsiblePerson/EMail,Status,Priority,Description&$expand=ResponsiblePerson`
- Headers:
  ```json
  {
    "Accept": "application/json;odata=verbose"
  }
  ```

### Step 4.4: Days Open Calculation

**Expression:**
```
div(sub(ticks(utcNow()),ticks(items('Apply_to_each_-_Process_Complaint')?['DateReceived'])),864000000000)
```

This calculates: (Now - DateReceived) / ticks per day

### Step 4.5: Test Flow 3
1. Create test complaint with DateReceived = 3 days ago
2. Run flow manually: **Test** → **Manually**
3. Verify:
   - Flow retrieves complaint
   - Days calculated correctly
   - Reminder email sent
4. Change DateReceived to 5 days ago
5. Run again
6. Verify escalation email sent to MD/HR

---

## Phase 5: Deploy Flow 4 - Resolution & Closure

### Step 5.1: Create New Flow
1. Go to https://make.powerautomate.com
2. Click **+ Create** → **Automated cloud flow**
3. Name: `Customer Complaints - Resolution and Closure`
4. Search trigger: **When an item is modified** (SharePoint)
5. Configure:
   - **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
   - **List Name:** `Customer Feedback and Complaints Final`
6. Click **Create**

### Step 5.2: Add Actions
Follow the complete configuration in: `flows/Flow4-Resolution-Closure-Configuration.md`

**Key actions:**
1. Get item - Retrieve full complaint details
2. Condition - Check if Status = "Resolved"
   - If yes: Send resolution email with satisfaction buttons
3. Condition - Check if Status = "Closed"
   - If yes:
     - Send HTTP Request - Update FeedbackClosedDate
     - Send closure email to customer
     - Send internal closure notification

### Step 5.3: Satisfaction Feedback Buttons

The resolution email includes mailto links:
- **Yes button:** `mailto:complaints@tpg.co.zw?subject=Satisfied - CCF-2026-00001&body=Yes, I am satisfied...`
- **No button:** `mailto:complaints@tpg.co.zw?subject=Not Satisfied - CCF-2026-00001&body=No, I am not satisfied...`

These open the customer's email client with pre-filled content.

### Step 5.4: Test Flow 4

**Test Resolution:**
1. Open existing complaint in SharePoint
2. Add text to **ResolutionSummary** field
3. Change **Status** to "Resolved"
4. Save
5. Verify:
   - Flow triggers
   - Customer receives resolution email
   - Satisfaction buttons work

**Test Closure:**
1. Open same complaint
2. Change **Status** to "Closed"
3. Save
4. Verify:
   - FeedbackClosedDate populated
   - Customer receives closure email
   - Department receives internal notification

---

## Phase 6: Deploy Public Complaint Form

You have **4 options** for the public form. Choose based on your requirements:

### Option 1: HTML Form + Power Automate HTTP Trigger (RECOMMENDED)

**Best for:** Full control, zero cost, direct integration

**Steps:**
1. Follow guide: `forms/Power-Automate-HTTP-Form.md`
2. Create Power Automate flow with HTTP trigger
3. Copy HTTP POST URL
4. Update `HTML-Public-Form.html` with URL
5. Host on Azure Static Web Apps (free) or company web server
6. Test submission

**Advantages:**
- ✅ Free
- ✅ Full branding control
- ✅ Direct SharePoint integration
- ✅ No external dependencies

### Option 2: Google Forms (FREE ALTERNATIVE)

**Best for:** Quick setup, familiar interface, zero cost

**Steps:**
1. Follow guide: `forms/Google-Forms-Setup.md`
2. Create form at https://forms.google.com
3. Add all fields
4. Link to Google Sheets
5. Create Power Automate flow with Google Forms connector
6. Share public link

**Advantages:**
- ✅ Free
- ✅ Easy to set up (30 minutes)
- ✅ File uploads work
- ✅ Mobile friendly

### Option 3: Typeform (PREMIUM)

**Best for:** Best user experience, professional appearance

**Steps:**
1. Follow guide: `forms/Typeform-Setup.md`
2. Sign up at https://www.typeform.com ($25/month)
3. Create conversational form
4. Customize branding
5. Connect to Power Automate
6. Share public link

**Advantages:**
- ✅ Beautiful design
- ✅ Conversational flow
- ✅ Advanced analytics
- ❌ Costs $25/month

### Option 4: Power Pages (MICROSOFT NATIVE)

**Best for:** Full Microsoft integration, enterprise features

**Steps:**
1. Go to https://make.powerpages.microsoft.com
2. Create new site
3. Add form connected to SharePoint list
4. Customize design
5. Publish as public site

**Advantages:**
- ✅ Native Microsoft integration
- ✅ Direct SharePoint connection
- ❌ Costs $5/user/month

---

## Phase 7: Configure Email Addresses

### Step 7.1: Update Department Emails

In **all flows**, update these email addresses to match your actual department contacts:

| Variable/Field | Current Value | Update To |
|----------------|---------------|-----------|
| qa.manager@tpg.co.zw | Quality Assurance Manager | [Actual email] |
| pharmacovigilance@tpg.co.zw | Pharmacovigilance Manager | [Actual email] |
| product.manager@tpg.co.zw | Product Manager | [Actual email] |
| cs.manager@tpg.co.zw | Customer Service Manager | [Actual email] |
| logistics.local@tpg.co.zw | Local Logistics Manager | [Actual email] |
| logistics.regional@tpg.co.zw | Regional Logistics Manager | [Actual email] |
| sales.manager@tpg.co.zw | Sales Manager | [Actual email] |
| marketing.manager@tpg.co.zw | Marketing Manager | [Actual email] |
| md@tpg.co.zw | Managing Director | [Actual email] |
| hr@tpg.co.zw | HR Manager | [Actual email] |
| regulatory@tpg.co.zw | Regulatory Affairs | [Actual email] |

### Step 7.2: Update Default Contact

Replace `jkaseke@tpg.co.zw` with appropriate default contact in:
- Flow 2: Customer acknowledgment CC
- Flow 2: Department assignment CC
- Flow 3: Reminder email CC
- Flow 3: Escalation email CC
- Flow 4: Closure email CC

---

## Phase 8: Configure Shared Mailbox

### Step 8.1: Verify Mailbox Access
1. Go to https://outlook.office.com
2. Click profile icon → **Open another mailbox**
3. Enter: `complaints@tpg.co.zw`
4. Verify you can access it

### Step 8.2: Grant Power Automate Access
1. Admin must grant "Send As" permissions
2. Exchange Admin Center → Recipients → Mailboxes
3. Select `complaints@tpg.co.zw`
4. Mailbox delegation → Send As
5. Add your account

### Step 8.3: Test Email Trigger
1. Send test email to `complaints@tpg.co.zw`
2. Verify Flow 2 triggers
3. Check flow run history

---

## Phase 9: Testing & Validation

### Test Scenario 1: Manual SharePoint Entry
1. Create new item in SharePoint list
2. Fill required fields
3. Save
4. **Expected:** Flow 1 triggers, emails sent, complaint number generated

### Test Scenario 2: Email Submission
1. Send email to `complaints@tpg.co.zw`
2. Include keywords like "quality issue"
3. **Expected:** Flow 2 triggers, item created, routed correctly

### Test Scenario 3: Public Form Submission
1. Open public form
2. Fill all fields
3. Upload attachment
4. Submit
5. **Expected:** Item created in SharePoint, emails sent

### Test Scenario 4: Adverse Event
1. Create complaint with Type = "Adverse Event"
2. **Expected:** Priority = Critical, urgent alert to MD/HR/Regulatory

### Test Scenario 5: Daily Reminder
1. Create complaint with DateReceived = 3 days ago
2. Run Flow 3 manually
3. **Expected:** Reminder email sent to department

### Test Scenario 6: Escalation
1. Create complaint with DateReceived = 5 days ago
2. Run Flow 3 manually
3. **Expected:** Escalation email sent to MD/HR

### Test Scenario 7: Resolution
1. Open complaint
2. Add ResolutionSummary
3. Change Status to "Resolved"
4. **Expected:** Resolution email with satisfaction buttons

### Test Scenario 8: Closure
1. Change Status to "Closed"
2. **Expected:** FeedbackClosedDate populated, closure emails sent

---

## Phase 10: Go Live

### Step 10.1: Enable All Flows
1. Verify all 4 flows are **On**
2. Check flow run history for errors
3. Fix any issues

### Step 10.2: Publish Form
1. Share public form link
2. Add to company website
3. Add to email signatures
4. Print QR codes for physical locations

### Step 10.3: Train Staff
1. Train department managers on:
   - Checking SharePoint for new complaints
   - Updating Status field
   - Adding ResolutionSummary
   - Closing complaints
2. Provide access to SharePoint list
3. Share this documentation

### Step 10.4: Communicate to Customers
1. Announce new complaint system
2. Share form link on:
   - Company website
   - Social media
   - Email newsletters
   - Product packaging
3. Update contact information

---

## Monitoring & Maintenance

### Daily Monitoring
- Check Flow 3 runs successfully each morning
- Review new complaints in SharePoint
- Monitor shared mailbox for incoming emails

### Weekly Review
- Review open complaints (Status ≠ Closed)
- Check SLA compliance (DaysOpen ≤ 5)
- Follow up on escalated complaints

### Monthly Reporting
- Total complaints received
- Average resolution time
- Complaints by type
- Complaints by department
- Customer satisfaction rate
- SLA compliance percentage

### Troubleshooting
See: `deployment/Troubleshooting.md`

---

## Rollback Plan

If issues occur:

1. **Turn off problematic flow**
2. **Revert to manual process:**
   - Monitor complaints@tpg.co.zw manually
   - Create SharePoint items manually
   - Send emails manually
3. **Fix issue in flow**
4. **Test thoroughly**
5. **Re-enable flow**

---

## Success Criteria

System is successfully deployed when:

- ✅ All 4 flows are running without errors
- ✅ Public form is accessible and submitting correctly
- ✅ Complaints are created in SharePoint automatically
- ✅ Complaint numbers are generated correctly
- ✅ Emails are sent to customers and departments
- ✅ Reminders and escalations are sent daily
- ✅ Resolution and closure workflows work
- ✅ Staff are trained and using the system
- ✅ Customers are aware of the new system

---

## Support Contacts

- **System Administrator:** jkaseke@tpg.co.zw
- **SharePoint Support:** [IT Department]
- **Power Automate Support:** [IT Department]
- **User Training:** [HR/Training Department]

---

## Next Steps After Deployment

1. Monitor system for first week closely
2. Gather feedback from staff
3. Optimize flows based on usage patterns
4. Consider additional features:
   - Power BI dashboard for analytics
   - SMS notifications for urgent complaints
   - Customer satisfaction survey automation
   - Integration with CRM system
   - Automated monthly reports

---

## Documentation Files

All documentation is in: `C:\Users\Joseph Kaseke\CascadeProjects\PulsePharma-ComplaintSystem\`

- `README.md` - Project overview
- `documentation/` - SharePoint structure, routing, SLA requirements
- `flows/` - Flow configuration guides
- `forms/` - Public form options and setup
- `deployment/` - This guide, testing checklist, troubleshooting

---

**Deployment Date:** _________________

**Deployed By:** _________________

**Sign-off:** _________________
