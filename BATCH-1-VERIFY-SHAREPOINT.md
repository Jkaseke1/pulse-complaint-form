# BATCH 1: Verify SharePoint List
## ✅ Starting Point: SharePoint List Created

---

## 🎯 GOAL
Verify your SharePoint list is properly configured before building flows.

**Time Required:** 10 minutes

---

## STEP 1: Access Your SharePoint List

1. **Open your browser**
2. **Go to:** https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final
3. **Click:** Site Contents (left sidebar)
4. **Find and click:** Customer Feedback and Complaints Final

---

## STEP 2: Verify All 21 Columns Exist

Click **+ Add column** (top right) to see all columns, or click on any item and check the form.

**Required columns checklist:**

### Basic Information
- [ ] **Title** (default SharePoint column)
- [ ] **ComplaintNo** (Single line of text)
- [ ] **DateReceived** (Date and time)
- [ ] **Category** (Choice: Complaint, Query)
- [ ] **Type** (Choice: 10 options)

### Customer Information
- [ ] **CustomerName** (Single line of text)
- [ ] **CustomerEmail** (Single line of text)
- [ ] **CustomerPhone** (Single line of text)

### Product Information
- [ ] **ProductName** (Single line of text)
- [ ] **BatchNumber** (Single line of text)
- [ ] **ExpiryDate** (Date and time)

### Complaint Details
- [ ] **Description** (Multiple lines of text)
- [ ] **Evidence** (Attachments - must be enabled)

### Assignment & Tracking
- [ ] **DepartmentResponsible** (Choice: 8 departments)
- [ ] **ResponsiblePerson** (Person)
- [ ] **AssignedTo** (Person)
- [ ] **Status** (Choice: Open, Under Review, Awaiting Response, Resolved, Closed)
- [ ] **Priority** (Choice: Low, Medium, High, Critical)

### Resolution
- [ ] **ResolutionSummary** (Multiple lines of text)
- [ ] **FeedbackClosedDate** (Date and time)
- [ ] **CustomerSatisfied** (Choice: Yes, No, Pending)

### Calculated
- [ ] **DaysOpen** (Calculated column)

---

## STEP 3: Verify Choice Field Options

### Check "Type" field has these 10 options:
1. Product Quality
2. Adverse Event
3. Product Portfolio
4. Account Services
5. Delivery - Local
6. Delivery - Out of Harare
7. Query - Sales
8. Query - Marketing
9. Query - Product
10. Query - General

**How to check:**
- Click **List settings** (gear icon → List settings)
- Under "Columns" section, click **Type**
- Verify all 10 choices are listed

### Check "DepartmentResponsible" field has these 8 options:
1. Quality Assurance
2. Pharmacovigilance
3. Product Management
4. Customer Service
5. Sales
6. Marketing
7. Logistics - Local
8. Logistics - Regional

### Check "Status" field has these 5 options:
1. Open
2. Under Review
3. Awaiting Response
4. Resolved
5. Closed

### Check "Priority" field has these 4 options:
1. Low
2. Medium
3. High
4. Critical

---

## STEP 4: Verify Attachments Are Enabled

1. **Go to:** List settings (gear icon → List settings)
2. **Click:** Advanced settings
3. **Check:** Attachments section shows **"Enabled"**
4. If disabled, change to **Enabled** and click **OK**

---

## STEP 5: Test Creating a Manual Item

1. **Click:** + New (top left of list)
2. **Fill in required fields:**
   - Category: Complaint
   - Type: Product Quality
   - CustomerName: Test User
   - CustomerEmail: test@example.com
   - Description: This is a test complaint
   - Status: Open
3. **Click:** Save

**Expected result:** Item should save successfully

4. **Verify the item appears in the list**
5. **Delete the test item** (click on it → Delete)

---

## STEP 6: Check Your Permissions

**You need Edit permissions to run Power Automate flows.**

1. **Click:** Share (top right)
2. **Verify:** Your account has "Edit" or "Full Control" permissions
3. If not, ask your SharePoint admin to grant Edit permissions

---

## ✅ VERIFICATION COMPLETE

If all checkboxes above are checked, your SharePoint list is ready!

---

## 🚨 TROUBLESHOOTING

### Issue: Can't find the list
**Solution:** 
- Verify the site URL is correct
- Check you have access to the site
- Ask SharePoint admin for permissions

### Issue: Missing columns
**Solution:**
- Add missing columns manually
- Use the column types specified in `documentation/SharePoint-List-Structure.md`

### Issue: Wrong column type
**Solution:**
- You may need to delete and recreate the column with correct type
- Or create a new column with correct name

### Issue: Can't enable attachments
**Solution:**
- You need site owner or admin permissions
- Ask your SharePoint admin to enable attachments

---

## 📋 WHAT'S NEXT?

Once SharePoint is verified, you have two options:

### **Option A: Deploy Flow 2 (Email Intake)** ⭐ RECOMMENDED
- Allows email submissions to complaints@tpg.co.zw
- Creates SharePoint items automatically
- **Next file:** `BATCH-2-FLOW-2-EMAIL-INTAKE.md`

### **Option B: Test Flow 1 (Already Working)**
- Verify the existing flow works
- Create a manual SharePoint item
- Check if emails are sent
- **Next file:** `BATCH-2-TEST-FLOW-1.md`

---

## 🎯 READY FOR NEXT BATCH?

**Tell me which option you want:**
1. **"Deploy Flow 2"** - Set up email intake
2. **"Test Flow 1"** - Verify what's already working
3. **"I found issues"** - Let's troubleshoot

---

**Current Progress:** SharePoint ✅ → Next: Flow 2 or Test Flow 1
