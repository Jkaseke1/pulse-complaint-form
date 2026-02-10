# Complete Deployment Roadmap - Start to Finish
## Pulse Pharmaceuticals Complaint Management System

---

## 📍 WHERE YOU ARE NOW

### ✅ Already Complete
1. **SharePoint List** - "Customer Feedback and Complaints Final" is created with all 21 columns
2. **Flow 1** - Acknowledgment & Assignment is deployed and working
3. **Documentation** - All guides and configuration files are ready

### 🔧 What Needs to Be Done
1. **Flow 2** - Email Intake (complaints@tpg.co.zw)
2. **Flow 3** - Daily Reminders & Escalation
3. **Flow 4** - Resolution & Closure
4. **Google Forms** - Public complaint form (PRIMARY)
5. **HTML Form** - Backup option (SECONDARY)

---

## 🎯 YOUR DEPLOYMENT PLAN

### Phase 1: Google Forms (PRIMARY) - Today
**Time:** 1-2 hours  
**Why First:** Quick to set up, test the full workflow

### Phase 2: Power Automate Flows - This Week
**Time:** 2-3 hours  
**Order:** Flow 2 → Flow 3 → Flow 4

### Phase 3: HTML Form (BACKUP) - Next Week
**Time:** 2 hours  
**Why Last:** More complex, but gives you full control

---

## 📋 COMPLETE STEP-BY-STEP GUIDE

---

## PHASE 1: GOOGLE FORMS SETUP (PRIMARY)

### ✅ GOOD NEWS: Google Forms DOES support file uploads with public sharing!

### Step 1.1: Create Google Form (15 minutes)

1. **Go to Google Forms**
   - Open browser: https://forms.google.com
   - Sign in with your Google account (use company account if available)

2. **Create New Form**
   - Click **+ Blank** (big plus icon)
   - You'll see a new untitled form

3. **Set Form Title and Description**
   - Click "Untitled form" at top
   - Change to: `Customer Feedback & Complaints - Pulse Pharmaceuticals`
   - Click "Form description"
   - Add:
     ```
     We value your feedback. Please complete this form to submit a complaint or query.
     
     We aim to:
     • Respond within 2 working days
     • Resolve issues within 5 working days
     
     You will receive a confirmation email with your reference number.
     ```

### Step 1.2: Add Form Questions (30 minutes)

**Add these questions in this exact order:**

#### Question 1: Category
1. Click **+ Add question** (right sidebar)
2. Select **Multiple choice**
3. Question: `Category`
4. Options:
   - `Complaint`
   - `Query`
5. Toggle **Required** ON (bottom of question)

#### Question 2: Type
1. Click **+ Add question**
2. Select **Dropdown**
3. Question: `Type`
4. Options (add all 10):
   - `Product Quality`
   - `Adverse Event`
   - `Product Portfolio`
   - `Account Services`
   - `Delivery - Local`
   - `Delivery - Out of Harare`
   - `Query - Sales`
   - `Query - Marketing`
   - `Query - Product`
   - `Query - General`
5. Toggle **Required** ON

#### Question 3: Your Name
1. Click **+ Add question**
2. Select **Short answer**
3. Question: `Your Name`
4. Toggle **Required** ON

#### Question 4: Email Address
1. Click **+ Add question**
2. Select **Short answer**
3. Question: `Email Address`
4. Click **⋮** (three dots) → **Response validation**
5. Select **Text** → **Email**
6. Toggle **Required** ON

#### Question 5: Phone Number
1. Click **+ Add question**
2. Select **Short answer**
3. Question: `Phone Number`
4. Description: `Optional - in case we need to reach you`
5. Leave **Required** OFF

#### Question 6: Product Name
1. Click **+ Add question**
2. Select **Short answer**
3. Question: `Product Name`
4. Description: `Name of the product involved (if applicable)`
5. Leave **Required** OFF

#### Question 7: Batch Number
1. Click **+ Add question**
2. Select **Short answer**
3. Question: `Batch Number`
4. Description: `Product batch/lot number (if applicable)`
5. Leave **Required** OFF

#### Question 8: Product Expiry Date
1. Click **+ Add question**
2. Select **Date**
3. Question: `Product Expiry Date`
4. Description: `If applicable`
5. Leave **Required** OFF

#### Question 9: Description
1. Click **+ Add question**
2. Select **Paragraph**
3. Question: `Please describe your complaint or query in detail`
4. Description: `The more details you provide, the better we can help you`
5. Toggle **Required** ON

#### Question 10: Attachments (FILE UPLOAD - THIS WORKS!)
1. Click **+ Add question**
2. Select **File upload**
3. Question: `Attachments (Photos/Documents)`
4. Description: `Upload any supporting documents, photos, or evidence (optional)`
5. Click **Continue** when prompted about file upload
6. Configure file upload settings:
   - **Allow only specific file types:** Check this
   - Select: **Images**, **PDFs**, **Documents**
   - **Maximum number of files:** `5`
   - **Maximum file size:** `10 MB`
7. Leave **Required** OFF

### Step 1.3: Configure Form Settings (10 minutes)

1. **Click Settings** (gear icon at top)

2. **General Tab:**
   - ✅ Check **Limit to 1 response** (optional - prevents spam)
   - ✅ Check **Collect email addresses** (for confirmation)
   - ❌ Uncheck **Restrict to users in Pulse Pharmaceuticals** (if you want public access)

3. **Presentation Tab:**
   - ✅ Check **Show progress bar**
   - **Confirmation message:** 
     ```
     Thank you! Your complaint has been submitted successfully.
     
     You will receive a confirmation email shortly with your reference number.
     
     If you have urgent concerns, please contact us at:
     📧 complaints@tpg.co.zw
     ```

4. Click **Save**

### Step 1.4: Customize Theme (5 minutes)

1. **Click Customize theme** (palette icon at top)
2. **Header:**
   - Choose color: `#0066cc` (Pulse Pharmaceuticals blue)
   - Or click **Choose image** to upload company logo
3. **Font style:** Choose **Basic** or **Modern**
4. Click **X** to close

### Step 1.5: Link to Google Sheets (5 minutes)

1. Click **Responses** tab (at top)
2. Click **Link to Sheets** icon (green spreadsheet icon)
3. Select **Create a new spreadsheet**
4. Name it: `Pulse Pharma - Customer Complaints`
5. Click **Create**
6. A new Google Sheet will open - keep this tab open

### Step 1.6: Get Public Link (2 minutes)

1. Go back to Google Form tab
2. Click **Send** button (top right)
3. Click **Link** icon (chain symbol)
4. ✅ Check **Shorten URL**
5. **Copy the link** - it will look like: `https://forms.gle/abc123xyz`
6. **Save this link** - you'll need it later

**🎉 Google Form is now complete and ready to receive submissions!**

---

## PHASE 2: POWER AUTOMATE FLOWS

### IMPORTANT: Do these in order (Flow 2 → Flow 3 → Flow 4)

---

## FLOW 2: EMAIL INTAKE (1 hour)

### Step 2.1: Create the Flow (5 minutes)

1. **Go to Power Automate**
   - Open: https://make.powerautomate.com
   - Sign in with your Office 365 account

2. **Create New Flow**
   - Click **+ Create** (left sidebar)
   - Click **Automated cloud flow**
   - Name: `Customer Complaints - Email Intake`
   - Click **Skip** (we'll add trigger manually)
   - Click **Create**

### Step 2.2: Add Email Trigger (5 minutes)

1. **Search for trigger**
   - In the search box, type: `shared mailbox`
   - Select: **When a new email arrives in a shared mailbox (V2)**

2. **Configure trigger**
   - **Original Mailbox Address:** `complaints@tpg.co.zw`
   - **Folder:** `Inbox`
   - **Include Attachments:** `Yes`
   - **Only with Attachments:** `No`
   - Leave other settings as default

3. Click **Save** (top right)

### Step 2.3: Initialize Variables (5 minutes)

1. **Add action**
   - Click **+ New step**
   - Search: `initialize variable`
   - Select: **Initialize variable**

2. **Configure Variable 1: DetectedType**
   - **Name:** `DetectedType`
   - **Type:** `String`
   - **Value:** (leave empty)

3. **Add two more variables** (click + New step after each)

4. **Variable 2: DetectedDepartment**
   - **Name:** `DetectedDepartment`
   - **Type:** `String`
   - **Value:** (leave empty)

5. **Variable 3: ResponsibleEmail**
   - **Name:** `ResponsibleEmail`
   - **Type:** `String`
   - **Value:** (leave empty)

### Step 2.4: Detect Type from Email (10 minutes)

1. **Add Compose action**
   - Click **+ New step**
   - Search: `compose`
   - Select: **Compose**

2. **Rename action**
   - Click **⋮** (three dots on action) → **Rename**
   - Name: `Compose - Detect Type`

3. **Add expression** (this detects complaint type from keywords)
   - Click in **Inputs** field
   - Click **Expression** tab
   - Copy and paste this ENTIRE expression:

```
if(or(contains(toLower(triggerBody()?['subject']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'defect')),'Product Quality',if(or(contains(toLower(triggerBody()?['subject']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'side effect')),'Adverse Event',if(or(contains(toLower(triggerBody()?['subject']),'product portfolio'),contains(toLower(triggerBody()?['bodyPreview']),'product range')),'Product Portfolio',if(or(contains(toLower(triggerBody()?['subject']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'invoice')),'Account Services',if(or(contains(toLower(triggerBody()?['subject']),'delivery'),contains(toLower(triggerBody()?['subject']),'harare'),contains(toLower(triggerBody()?['bodyPreview']),'delivery')),'Delivery - Local',if(or(contains(toLower(triggerBody()?['subject']),'out of harare'),contains(toLower(triggerBody()?['bodyPreview']),'out of harare')),'Delivery - Out of Harare',if(or(contains(toLower(triggerBody()?['subject']),'sales'),contains(toLower(triggerBody()?['bodyPreview']),'sales rep')),'Query - Sales',if(or(contains(toLower(triggerBody()?['subject']),'marketing'),contains(toLower(triggerBody()?['bodyPreview']),'marketing')),'Query - Marketing',if(or(contains(toLower(triggerBody()?['subject']),'product query'),contains(toLower(triggerBody()?['bodyPreview']),'product information')),'Query - Product','Query - General')))))))))
```

4. Click **OK**

5. **Set the variable**
   - Click **+ New step**
   - Search: `set variable`
   - Select: **Set variable**
   - **Name:** Select `DetectedType` from dropdown
   - **Value:** Click in field → **Dynamic content** → Select `Outputs` (from Compose - Detect Type)

### Step 2.5: Route to Department (10 minutes)

1. **Add Compose action**
   - Click **+ New step**
   - Search: `compose`
   - Select: **Compose**
   - Rename to: `Compose - Route to Department`

2. **Add routing expression**
   - Click in **Inputs** field
   - Click **Expression** tab
   - Paste this expression:

```
if(equals(variables('DetectedType'),'Product Quality'),'Quality Assurance',if(equals(variables('DetectedType'),'Adverse Event'),'Pharmacovigilance',if(equals(variables('DetectedType'),'Product Portfolio'),'Product Management',if(equals(variables('DetectedType'),'Account Services'),'Customer Service',if(equals(variables('DetectedType'),'Delivery - Local'),'Logistics - Local',if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'Logistics - Regional',if(equals(variables('DetectedType'),'Query - Sales'),'Sales',if(equals(variables('DetectedType'),'Query - Marketing'),'Marketing',if(equals(variables('DetectedType'),'Query - Product'),'Product Management','Customer Service')))))))))
```

3. Click **OK**

4. **Set the variable**
   - Click **+ New step**
   - Search: `set variable`
   - **Name:** `DetectedDepartment`
   - **Value:** `Outputs` (from Compose - Route to Department)

### Step 2.6: Get Responsible Email (10 minutes)

1. **Add Compose action**
   - Click **+ New step**
   - Select: **Compose**
   - Rename to: `Compose - Get Responsible Email`

2. **Add email routing expression**
   - Click in **Inputs** field
   - Click **Expression** tab
   - Paste:

```
if(equals(variables('DetectedType'),'Product Quality'),'qa.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Adverse Event'),'pharmacovigilance@tpg.co.zw',if(equals(variables('DetectedType'),'Product Portfolio'),'product.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Account Services'),'cs.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Delivery - Local'),'logistics.local@tpg.co.zw',if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'logistics.regional@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Sales'),'sales.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Marketing'),'marketing.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Product'),'product.manager@tpg.co.zw','jkaseke@tpg.co.zw')))))))))
```

3. **IMPORTANT:** Replace these email addresses with your actual department emails!

4. Click **OK**

5. **Set the variable**
   - Click **+ New step**
   - Search: `set variable`
   - **Name:** `ResponsibleEmail`
   - **Value:** `Outputs` (from Compose - Get Responsible Email)

### Step 2.7: Determine Priority (5 minutes)

1. **Add Compose action**
   - Click **+ New step**
   - Select: **Compose**
   - Rename to: `Compose - Determine Priority`

2. **Add priority expression**
   - Click **Expression** tab
   - Paste:

```
if(equals(variables('DetectedType'),'Adverse Event'),'Critical',if(equals(variables('DetectedType'),'Product Quality'),'High',if(or(contains(variables('DetectedType'),'Query'),equals(variables('DetectedType'),'Query - General')),'Low','Medium')))
```

3. Click **OK**

### Step 2.8: Determine Category (5 minutes)

1. **Add Compose action**
   - Click **+ New step**
   - Select: **Compose**
   - Rename to: `Compose - Determine Category`

2. **Add category expression**
   - Click **Expression** tab
   - Paste:

```
if(contains(variables('DetectedType'),'Query'),'Query','Complaint')
```

3. Click **OK**

### Step 2.9: Create SharePoint Item (15 minutes) - CRITICAL STEP

1. **Add HTTP Request action**
   - Click **+ New step**
   - Search: `send an http request to sharepoint`
   - Select: **Send an HTTP request to SharePoint**

2. **Configure HTTP Request**
   - **Site Address:** `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
   - **Method:** `POST`
   - **Uri:** `_api/lists/getByTitle('Customer Feedback and Complaints Final')/items`
   - **Headers:** Click **Add new item** twice and add:
     - Key: `Accept` Value: `application/json;odata=verbose`
     - Key: `Content-Type` Value: `application/json;odata=verbose`

3. **Body:** Click in field, then click **Expression** tab, paste this, then click **OK**:

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

4. Rename action to: `HTTP - Create Complaint Item`

**⚠️ PAUSE HERE - Save your flow and test it before continuing!**

---

## 🛑 CHECKPOINT: Test Flow 2 So Far

1. **Save the flow** (top right)
2. **Send test email** to complaints@tpg.co.zw with subject: "Product quality issue"
3. **Check flow run history:**
   - Click **Flow checker** (top right)
   - Look for any errors
4. **Check SharePoint:**
   - Go to your SharePoint list
   - Verify a new item was created
5. **If successful, continue. If errors, check Troubleshooting section below**

---

### Continue Flow 2: Generate Complaint Number (remaining 10 minutes)

I'll provide the rest of Flow 2 in the next section. Should I continue with the complete step-by-step, or would you like to pause here and test what we have so far?

---

## 📝 QUICK REFERENCE

### What You're Building:
1. ✅ Google Form (with file uploads) - DONE
2. 🔧 Flow 2: Email Intake - IN PROGRESS (50% complete)
3. ⏳ Flow 3: Daily Reminders - NEXT
4. ⏳ Flow 4: Resolution & Closure - AFTER THAT
5. ⏳ Google Forms → Power Automate integration - FINAL STEP
6. ⏳ HTML Form - BACKUP OPTION

### Time Investment:
- Google Forms: ✅ 1 hour (DONE)
- Flow 2: 🔧 30 more minutes (50% done)
- Flow 3: ⏳ 30 minutes
- Flow 4: ⏳ 30 minutes
- Google Forms integration: ⏳ 30 minutes
- HTML Form: ⏳ 2 hours (optional backup)

**Total: 4-5 hours for complete system**

---

Would you like me to:
1. **Continue with the rest of Flow 2** (complaint number generation, emails, etc.)
2. **Pause here and test** what we have so far
3. **Jump to Google Forms integration** first (so you can test the form)

Let me know and I'll continue with detailed step-by-step instructions!
