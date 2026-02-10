# Testing Checklist - Pulse Pharmaceuticals Complaint Management System

## Pre-Deployment Testing

### SharePoint List Verification
- [ ] List "Customer Feedback and Complaints Final" exists
- [ ] All 21 columns are present and configured correctly
- [ ] Choice fields have correct options
- [ ] Attachments are enabled
- [ ] Calculated column (DaysOpen) formula is correct
- [ ] List permissions are set correctly
- [ ] Can create items manually
- [ ] Can edit items
- [ ] Can attach files

---

## Flow 1: Acknowledgment & Assignment (Already Working ✅)

### Basic Functionality
- [ ] Flow is turned **On**
- [ ] Trigger: When item is created in SharePoint
- [ ] Complaint number generated correctly (CCF-2026-00001 format)
- [ ] Complaint number updates SharePoint item
- [ ] Department routing works for all 10 types
- [ ] Responsible email assigned correctly
- [ ] Priority set correctly based on type

### Email Testing
- [ ] Customer acknowledgment email sent
- [ ] Customer email is HTML formatted
- [ ] Customer email contains complaint number
- [ ] Department assignment email sent
- [ ] Department email is HTML formatted
- [ ] Department email contains all details

### Adverse Event Testing
- [ ] Type = "Adverse Event" sets Priority = Critical
- [ ] Urgent alert sent to MD
- [ ] Urgent alert sent to HR
- [ ] Urgent alert sent to Regulatory
- [ ] Urgent alert sent to Pharmacovigilance
- [ ] Alert email is HTML formatted with high importance

### Test Cases
- [ ] Test Case 1: Product Quality complaint
  - Expected: Route to Quality Assurance, Priority = High
- [ ] Test Case 2: Adverse Event complaint
  - Expected: Route to Pharmacovigilance, Priority = Critical, urgent alerts sent
- [ ] Test Case 3: Query - General
  - Expected: Route to Customer Service, Priority = Low
- [ ] Test Case 4: Delivery - Local
  - Expected: Route to Logistics - Local, Priority = Medium

---

## Flow 2: Email Intake

### Trigger Configuration
- [ ] Trigger: When new email arrives in shared mailbox (V2)
- [ ] Mailbox: complaints@tpg.co.zw
- [ ] Include attachments: Yes
- [ ] Flow is turned **On**

### Variable Initialization
- [ ] Variable: DetectedType (String)
- [ ] Variable: DetectedDepartment (String)
- [ ] Variable: ResponsibleEmail (String)

### Keyword Detection
- [ ] "quality" → Product Quality
- [ ] "adverse" or "side effect" → Adverse Event
- [ ] "delivery" → Delivery - Local
- [ ] "account" or "invoice" → Account Services
- [ ] Generic text → Query - General

### SharePoint Item Creation
- [ ] HTTP Request creates item successfully
- [ ] All fields populated correctly
- [ ] DateReceived set to current time
- [ ] Category determined correctly
- [ ] Type detected from email content
- [ ] Customer name extracted from email sender
- [ ] Customer email extracted correctly
- [ ] Description contains email body
- [ ] DepartmentResponsible assigned
- [ ] Status set to "Open"
- [ ] Priority assigned correctly

### Complaint Number Generation
- [ ] Item ID retrieved from HTTP response
- [ ] Complaint number generated (CCF-2026-00001)
- [ ] SharePoint item updated with complaint number

### Attachment Handling
- [ ] Condition checks for attachments
- [ ] Apply to each loops through attachments
- [ ] Files uploaded to SharePoint successfully
- [ ] Multiple attachments handled correctly
- [ ] Large files handled (up to 10MB)

### Email Notifications
- [ ] Customer acknowledgment sent
- [ ] Department assignment sent
- [ ] Adverse event alert sent (if applicable)
- [ ] All emails HTML formatted
- [ ] All emails contain correct information

### Test Cases
- [ ] Test Case 1: Email with "quality issue" in subject
  - Expected: Type = Product Quality, route to QA
- [ ] Test Case 2: Email with "adverse reaction" in body
  - Expected: Type = Adverse Event, Priority = Critical, urgent alerts
- [ ] Test Case 3: Email with attachment
  - Expected: Attachment uploaded to SharePoint
- [ ] Test Case 4: Email with multiple attachments
  - Expected: All attachments uploaded
- [ ] Test Case 5: Generic email
  - Expected: Type = Query - General, route to Customer Service

---

## Flow 3: Daily Reminders and Escalation

### Trigger Configuration
- [ ] Trigger: Recurrence
- [ ] Frequency: Daily
- [ ] Time: 8:00 AM
- [ ] Time zone: (UTC+02:00) Harare, Pretoria
- [ ] Days: Monday-Friday only
- [ ] Flow is turned **On**

### Get Open Complaints
- [ ] HTTP Request retrieves items
- [ ] Filter: Status ≠ Closed AND Status ≠ Resolved
- [ ] All required fields selected
- [ ] ResponsiblePerson expanded correctly

### Parse JSON
- [ ] Schema matches response structure
- [ ] Results array parsed correctly
- [ ] All fields accessible

### Days Open Calculation
- [ ] Formula calculates correctly
- [ ] Day 0: Received today
- [ ] Day 1: Received yesterday
- [ ] Day 3: Received 3 days ago
- [ ] Day 5: Received 5 days ago

### Reminder Logic (Day 3-4)
- [ ] Condition: Days ≥ 3
- [ ] Reminder email sent to department
- [ ] Email contains days open
- [ ] Email contains progress bar
- [ ] Email HTML formatted
- [ ] CC to jkaseke@tpg.co.zw

### Escalation Logic (Day 5+)
- [ ] Condition: Days ≥ 5
- [ ] Escalation email sent to MD
- [ ] Escalation email sent to HR
- [ ] Department manager copied
- [ ] Email marked as high importance
- [ ] Email HTML formatted
- [ ] Email contains all complaint details

### Test Cases
- [ ] Test Case 1: Complaint 2 days old
  - Expected: No email sent
- [ ] Test Case 2: Complaint 3 days old
  - Expected: Reminder email to department
- [ ] Test Case 3: Complaint 4 days old
  - Expected: Reminder email to department
- [ ] Test Case 4: Complaint 5 days old
  - Expected: Escalation email to MD/HR
- [ ] Test Case 5: Complaint 7 days old
  - Expected: Escalation email to MD/HR
- [ ] Test Case 6: Complaint with Status = Closed
  - Expected: No email sent (filtered out)
- [ ] Test Case 7: Multiple open complaints
  - Expected: Correct email for each based on days open

### Manual Test Run
- [ ] Flow can be run manually
- [ ] Test button works
- [ ] Run history shows results
- [ ] No errors in execution

---

## Flow 4: Resolution and Closure

### Trigger Configuration
- [ ] Trigger: When item is modified (SharePoint)
- [ ] List: Customer Feedback and Complaints Final
- [ ] Flow is turned **On**

### Get Item Details
- [ ] Item retrieved successfully
- [ ] All fields accessible
- [ ] Choice fields extract Value correctly

### Resolution Workflow (Status = Resolved)
- [ ] Condition checks Status = "Resolved"
- [ ] Resolution email sent to customer
- [ ] Email contains ResolutionSummary
- [ ] Email contains satisfaction buttons
- [ ] "Yes" button opens email with positive feedback
- [ ] "No" button opens email with negative feedback
- [ ] Email HTML formatted
- [ ] Email contains all complaint details

### Closure Workflow (Status = Closed)
- [ ] Condition checks Status = "Closed"
- [ ] HTTP Request updates FeedbackClosedDate
- [ ] FeedbackClosedDate set to current time
- [ ] Closure email sent to customer
- [ ] Internal notification sent to department
- [ ] Emails contain days to resolution
- [ ] Emails contain SLA compliance status
- [ ] Emails HTML formatted

### Test Cases
- [ ] Test Case 1: Change Status to "Resolved"
  - Expected: Resolution email sent with satisfaction buttons
- [ ] Test Case 2: Click "Yes, I'm Satisfied" button
  - Expected: Email client opens with positive feedback
- [ ] Test Case 3: Click "No, I'm Not Satisfied" button
  - Expected: Email client opens with negative feedback form
- [ ] Test Case 4: Change Status to "Closed"
  - Expected: FeedbackClosedDate populated, closure emails sent
- [ ] Test Case 5: Change Status to "Under Review"
  - Expected: No emails sent (not Resolved or Closed)
- [ ] Test Case 6: Modify other fields (not Status)
  - Expected: Flow runs but no emails sent

---

## Public Form Testing

### Form Accessibility
- [ ] Form loads on desktop browser
- [ ] Form loads on mobile browser
- [ ] Form loads on tablet
- [ ] Form is responsive (adapts to screen size)
- [ ] All fields visible and accessible
- [ ] Form styling displays correctly

### Field Validation
- [ ] Category field is required
- [ ] Type field is required
- [ ] Customer Name is required
- [ ] Customer Email is required
- [ ] Email field validates email format
- [ ] Description is required
- [ ] Optional fields can be left blank
- [ ] Form prevents submission if required fields empty

### File Upload
- [ ] File upload area is visible
- [ ] Click to upload works
- [ ] Drag and drop works (if supported)
- [ ] Image files accepted (JPG, PNG, GIF)
- [ ] PDF files accepted
- [ ] Word documents accepted
- [ ] Invalid file types rejected
- [ ] File size limit enforced (10MB)
- [ ] Multiple files can be uploaded
- [ ] File names displayed after selection

### Form Submission
- [ ] Submit button works
- [ ] Button shows "Submitting..." during submission
- [ ] Button disabled during submission
- [ ] Success message displays after submission
- [ ] Form resets after successful submission
- [ ] Error message displays if submission fails
- [ ] Error message is helpful and actionable

### Integration Testing
- [ ] Form data sent to Power Automate
- [ ] SharePoint item created
- [ ] All form fields mapped correctly
- [ ] File attachments transferred to SharePoint
- [ ] Complaint number generated
- [ ] Emails sent to customer and department

### Test Cases
- [ ] Test Case 1: Submit with all fields filled
  - Expected: Success, item created, emails sent
- [ ] Test Case 2: Submit with only required fields
  - Expected: Success, optional fields empty in SharePoint
- [ ] Test Case 3: Submit with invalid email
  - Expected: Validation error, form not submitted
- [ ] Test Case 4: Submit without required fields
  - Expected: Validation error, form not submitted
- [ ] Test Case 5: Submit with file attachment
  - Expected: File uploaded to SharePoint
- [ ] Test Case 6: Submit with multiple attachments
  - Expected: All files uploaded to SharePoint
- [ ] Test Case 7: Submit Adverse Event
  - Expected: Priority = Critical, urgent alerts sent

---

## Integration Testing

### End-to-End Flow 1: SharePoint Entry
1. [ ] Create item in SharePoint manually
2. [ ] Flow 1 triggers
3. [ ] Complaint number generated
4. [ ] Emails sent
5. [ ] Item updated in SharePoint

### End-to-End Flow 2: Email Submission
1. [ ] Send email to complaints@tpg.co.zw
2. [ ] Flow 2 triggers
3. [ ] Item created in SharePoint
4. [ ] Complaint number generated
5. [ ] Emails sent
6. [ ] Attachments uploaded (if any)

### End-to-End Flow 3: Public Form Submission
1. [ ] Submit form via public URL
2. [ ] Data sent to Power Automate
3. [ ] Item created in SharePoint
4. [ ] Complaint number generated
5. [ ] Emails sent
6. [ ] Attachments uploaded (if any)

### End-to-End Flow 4: Daily Reminder
1. [ ] Create complaint 3 days ago
2. [ ] Wait for 8:00 AM or run Flow 3 manually
3. [ ] Reminder email sent
4. [ ] Correct recipient
5. [ ] Correct content

### End-to-End Flow 5: Escalation
1. [ ] Create complaint 5 days ago
2. [ ] Wait for 8:00 AM or run Flow 3 manually
3. [ ] Escalation email sent to MD/HR
4. [ ] Department manager copied
5. [ ] Correct content

### End-to-End Flow 6: Resolution
1. [ ] Open complaint in SharePoint
2. [ ] Add ResolutionSummary
3. [ ] Change Status to "Resolved"
4. [ ] Flow 4 triggers
5. [ ] Resolution email sent
6. [ ] Satisfaction buttons work

### End-to-End Flow 7: Closure
1. [ ] Change Status to "Closed"
2. [ ] Flow 4 triggers
3. [ ] FeedbackClosedDate populated
4. [ ] Closure emails sent
5. [ ] Correct recipients

---

## Performance Testing

### Flow Execution Time
- [ ] Flow 1 completes in < 30 seconds
- [ ] Flow 2 completes in < 60 seconds (with attachments)
- [ ] Flow 3 completes in < 5 minutes (for 50 complaints)
- [ ] Flow 4 completes in < 30 seconds

### Concurrent Submissions
- [ ] Multiple emails received simultaneously
- [ ] Multiple form submissions simultaneously
- [ ] All processed correctly
- [ ] No data loss
- [ ] No duplicate complaint numbers

### Large Attachments
- [ ] 1MB file uploads successfully
- [ ] 5MB file uploads successfully
- [ ] 10MB file uploads successfully
- [ ] Multiple large files upload successfully

---

## Error Handling Testing

### Invalid Data
- [ ] Missing required fields handled gracefully
- [ ] Invalid email format rejected
- [ ] Invalid date format handled
- [ ] Empty description handled

### SharePoint Errors
- [ ] List not found error handled
- [ ] Permission denied error handled
- [ ] Network timeout handled
- [ ] Item creation failure handled

### Email Errors
- [ ] Invalid recipient email handled
- [ ] Email send failure handled
- [ ] Attachment too large handled

### Flow Errors
- [ ] Parse JSON error handled
- [ ] HTTP request error handled
- [ ] Variable not set error handled
- [ ] Timeout error handled

---

## Security Testing

### Access Control
- [ ] Only authorized users can access SharePoint list
- [ ] Only authorized users can modify flows
- [ ] Shared mailbox permissions correct
- [ ] Public form accessible to everyone

### Data Privacy
- [ ] Customer data encrypted in transit
- [ ] Customer data encrypted at rest
- [ ] No sensitive data in flow run history
- [ ] No passwords or keys in flow

### Email Security
- [ ] Emails sent from legitimate domain
- [ ] SPF/DKIM configured correctly
- [ ] No email spoofing possible
- [ ] Attachments scanned for malware

---

## User Acceptance Testing

### Department Managers
- [ ] Can access SharePoint list
- [ ] Can view assigned complaints
- [ ] Can update Status field
- [ ] Can add ResolutionSummary
- [ ] Can close complaints
- [ ] Receive assignment emails
- [ ] Receive reminder emails
- [ ] Understand workflow

### Customers
- [ ] Can access public form
- [ ] Can submit complaints easily
- [ ] Receive acknowledgment emails
- [ ] Receive resolution emails
- [ ] Receive closure emails
- [ ] Satisfaction buttons work
- [ ] Understand process

### System Administrators
- [ ] Can monitor flow run history
- [ ] Can troubleshoot errors
- [ ] Can modify flows
- [ ] Can update email addresses
- [ ] Can generate reports
- [ ] Understand system architecture

---

## Regression Testing (After Changes)

### After Flow Modification
- [ ] Re-run all test cases for modified flow
- [ ] Verify no impact on other flows
- [ ] Check flow run history for errors
- [ ] Test end-to-end scenarios

### After SharePoint Changes
- [ ] Verify all flows still work
- [ ] Verify column references correct
- [ ] Test item creation
- [ ] Test item modification

### After Email Address Changes
- [ ] Verify emails sent to correct recipients
- [ ] Test all email notifications
- [ ] Verify CC/BCC recipients correct

---

## Sign-Off

### Testing Completed By
- **Name:** _________________
- **Date:** _________________
- **Signature:** _________________

### Issues Found
- **Critical:** _____ (must fix before go-live)
- **High:** _____ (should fix before go-live)
- **Medium:** _____ (can fix after go-live)
- **Low:** _____ (nice to have)

### Approval for Go-Live
- [ ] All critical issues resolved
- [ ] All high issues resolved or accepted
- [ ] System ready for production
- [ ] Staff trained
- [ ] Documentation complete

**Approved By:** _________________

**Date:** _________________

**Signature:** _________________
