# BATCH 2: Deploy Flow 2 - Email Intake
## 📧 Goal: Enable email submissions to complaints@tpg.co.zw

---

## 🎯 WHAT THIS FLOW DOES
- Receives emails sent to complaints@tpg.co.zw
- Detects complaint type from keywords
- Creates SharePoint item automatically
- Generates complaint number (CCF-2026-00001)
- Sends acknowledgment to customer
- Notifies department manager

**Time Required:** 1 hour

---

## PREREQUISITES

Before starting, ensure:
- [ ] SharePoint list verified (Batch 1 complete)
- [ ] You have access to complaints@tpg.co.zw mailbox
- [ ] You can access https://make.powerautomate.com
- [ ] You have department manager email addresses ready

---

## PART 1: CREATE THE FLOW (5 minutes)

### Step 1: Go to Power Automate
1. Open browser: **https://make.powerautomate.com**
2. Sign in with your Office 365 account

### Step 2: Create New Flow
1. Click **+ Create** (left sidebar)
2. Click **Automated cloud flow**
3. **Name:** `Customer Complaints - Email Intake`
4. Click **Skip** (we'll add trigger manually)
5. Click **Create**

---

## PART 2: ADD EMAIL TRIGGER (5 minutes)

### Step 3: Add Trigger
1. In the search box, type: **shared mailbox**
2. Select: **When a new email arrives in a shared mailbox (V2)**

### Step 4: Configure Trigger
Fill in these fields:
- **Original Mailbox Address:** `complaints@tpg.co.zw`
- **Folder:** `Inbox`
- **Include Attachments:** `Yes`
- **Only with Attachments:** `No`
- **Importance:** Leave as "All"

### Step 5: Save
Click **Save** (top right)

**✅ CHECKPOINT:** Flow should save without errors. If you get an error about mailbox access, you need permissions to the shared mailbox.

---

## PART 3: INITIALIZE VARIABLES (10 minutes)

We need 3 variables to store information.

### Step 6: Add Variable 1 - DetectedType
1. Click **+ New step**
2. Search: **initialize variable**
3. Select: **Initialize variable**
4. Fill in:
   - **Name:** `DetectedType`
   - **Type:** `String`
   - **Value:** (leave empty)

### Step 7: Add Variable 2 - DetectedDepartment
1. Click **+ New step**
2. Search: **initialize variable**
3. Select: **Initialize variable**
4. Fill in:
   - **Name:** `DetectedDepartment`
   - **Type:** `String`
   - **Value:** (leave empty)

### Step 8: Add Variable 3 - ResponsibleEmail
1. Click **+ New step**
2. Search: **initialize variable**
3. Select: **Initialize variable**
4. Fill in:
   - **Name:** `ResponsibleEmail`
   - **Type:** `String`
   - **Value:** (leave empty)

### Step 9: Save
Click **Save** (top right)

---

## PART 4: DETECT COMPLAINT TYPE (10 minutes)

This detects the type of complaint from email keywords.

### Step 10: Add Compose Action
1. Click **+ New step**
2. Search: **compose**
3. Select: **Compose**

### Step 11: Rename Action
1. Click **⋮** (three dots on the Compose action)
2. Click **Rename**
3. Change name to: `Compose - Detect Type`

### Step 12: Add Detection Expression
1. Click in the **Inputs** field
2. Click **Expression** tab (above the field)
3. **Copy this ENTIRE expression** (it's long):

```
if(or(contains(toLower(triggerBody()?['subject']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'quality'),contains(toLower(triggerBody()?['bodyPreview']),'defect')),'Product Quality',if(or(contains(toLower(triggerBody()?['subject']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'adverse'),contains(toLower(triggerBody()?['bodyPreview']),'side effect')),'Adverse Event',if(or(contains(toLower(triggerBody()?['subject']),'product portfolio'),contains(toLower(triggerBody()?['bodyPreview']),'product range')),'Product Portfolio',if(or(contains(toLower(triggerBody()?['subject']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'account'),contains(toLower(triggerBody()?['bodyPreview']),'invoice')),'Account Services',if(or(contains(toLower(triggerBody()?['subject']),'delivery'),contains(toLower(triggerBody()?['subject']),'harare'),contains(toLower(triggerBody()?['bodyPreview']),'delivery')),'Delivery - Local',if(or(contains(toLower(triggerBody()?['subject']),'out of harare'),contains(toLower(triggerBody()?['bodyPreview']),'out of harare')),'Delivery - Out of Harare',if(or(contains(toLower(triggerBody()?['subject']),'sales'),contains(toLower(triggerBody()?['bodyPreview']),'sales rep')),'Query - Sales',if(or(contains(toLower(triggerBody()?['subject']),'marketing'),contains(toLower(triggerBody()?['bodyPreview']),'marketing')),'Query - Marketing',if(or(contains(toLower(triggerBody()?['subject']),'product query'),contains(toLower(triggerBody()?['bodyPreview']),'product information')),'Query - Product','Query - General')))))))))
```

4. Click **OK**

### Step 13: Set the Variable
1. Click **+ New step**
2. Search: **set variable**
3. Select: **Set variable**
4. Fill in:
   - **Name:** Select `DetectedType` from dropdown
   - **Value:** Click in field → **Dynamic content** tab → Select **Outputs** (from Compose - Detect Type)

### Step 14: Save
Click **Save**

---

## PART 5: ROUTE TO DEPARTMENT (10 minutes)

### Step 15: Add Compose Action
1. Click **+ New step**
2. Search: **compose**
3. Select: **Compose**
4. Rename to: `Compose - Route to Department`

### Step 16: Add Routing Expression
1. Click in **Inputs** field
2. Click **Expression** tab
3. **Copy this expression:**

```
if(equals(variables('DetectedType'),'Product Quality'),'Quality Assurance',if(equals(variables('DetectedType'),'Adverse Event'),'Pharmacovigilance',if(equals(variables('DetectedType'),'Product Portfolio'),'Product Management',if(equals(variables('DetectedType'),'Account Services'),'Customer Service',if(equals(variables('DetectedType'),'Delivery - Local'),'Logistics - Local',if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'Logistics - Regional',if(equals(variables('DetectedType'),'Query - Sales'),'Sales',if(equals(variables('DetectedType'),'Query - Marketing'),'Marketing',if(equals(variables('DetectedType'),'Query - Product'),'Product Management','Customer Service')))))))))
```

4. Click **OK**

### Step 17: Set the Variable
1. Click **+ New step**
2. Search: **set variable**
3. Select: **Set variable**
4. **Name:** `DetectedDepartment`
5. **Value:** Select **Outputs** (from Compose - Route to Department)

### Step 18: Save
Click **Save**

---

## PART 6: GET RESPONSIBLE EMAIL (10 minutes)

### Step 19: Add Compose Action
1. Click **+ New step**
2. Select: **Compose**
3. Rename to: `Compose - Get Responsible Email`

### Step 20: Add Email Expression
1. Click in **Inputs** field
2. Click **Expression** tab
3. **Copy this expression:**

```
if(equals(variables('DetectedType'),'Product Quality'),'qa.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Adverse Event'),'pharmacovigilance@tpg.co.zw',if(equals(variables('DetectedType'),'Product Portfolio'),'product.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Account Services'),'cs.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Delivery - Local'),'logistics.local@tpg.co.zw',if(equals(variables('DetectedType'),'Delivery - Out of Harare'),'logistics.regional@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Sales'),'sales.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Marketing'),'marketing.manager@tpg.co.zw',if(equals(variables('DetectedType'),'Query - Product'),'product.manager@tpg.co.zw','jkaseke@tpg.co.zw')))))))))
```

4. **⚠️ IMPORTANT:** Replace these email addresses with your actual department emails:
   - qa.manager@tpg.co.zw → Your QA manager email
   - pharmacovigilance@tpg.co.zw → Your Pharmacovigilance email
   - product.manager@tpg.co.zw → Your Product Manager email
   - cs.manager@tpg.co.zw → Your Customer Service email
   - logistics.local@tpg.co.zw → Your Local Logistics email
   - logistics.regional@tpg.co.zw → Your Regional Logistics email
   - sales.manager@tpg.co.zw → Your Sales Manager email
   - marketing.manager@tpg.co.zw → Your Marketing Manager email
   - jkaseke@tpg.co.zw → Your default contact email

5. Click **OK**

### Step 21: Set the Variable
1. Click **+ New step**
2. Search: **set variable**
3. **Name:** `ResponsibleEmail`
4. **Value:** Select **Outputs** (from Compose - Get Responsible Email)

### Step 22: Save
Click **Save**

---

## 🛑 PAUSE HERE - TEST SO FAR

Before continuing, let's test what we have:

1. **Turn on the flow:** Toggle switch at top right to **On**
2. **Send test email:**
   - To: complaints@tpg.co.zw
   - Subject: "Product quality issue"
   - Body: "I received a defective product"
3. **Check flow run:**
   - Wait 1-2 minutes
   - Refresh the page
   - Look for a run in the history (bottom of page)
   - Click on it to see if it succeeded
4. **Check variables:**
   - In the run history, expand each action
   - Verify DetectedType = "Product Quality"
   - Verify DetectedDepartment = "Quality Assurance"
   - Verify ResponsibleEmail = your QA email

**✅ If successful, continue to Part 7**  
**❌ If failed, check the error message and let me know**

---

## 📋 WHAT'S NEXT?

**Part 7-12 will cover:**
- Creating SharePoint item via HTTP request
- Generating complaint number
- Uploading attachments
- Sending emails to customer and department
- Handling adverse events

**This is saved for Batch 3 to keep things manageable.**

---

## 🎯 CURRENT STATUS

✅ SharePoint verified  
✅ Flow 2 - Parts 1-6 complete (50% done)  
⏳ Flow 2 - Parts 7-12 remaining  
⏳ Flow 3 - Daily Reminders  
⏳ Flow 4 - Resolution & Closure  
⏳ Google Forms integration  

---

## 🚀 READY FOR BATCH 3?

**Tell me:**
1. **"Test passed"** - Continue to Batch 3 (complete Flow 2)
2. **"Test failed"** - Let's troubleshoot
3. **"Need a break"** - We'll continue later

**Current Progress:** SharePoint ✅ → Flow 2 (50%) ✅ → Next: Complete Flow 2
