# Troubleshooting Guide - Pulse Pharmaceuticals Complaint Management System

## Common Issues and Solutions

---

## Flow 1: Acknowledgment & Assignment

### Issue: Complaint number not generated

**Symptoms:**
- ComplaintNo field is empty
- Error in flow run history

**Possible Causes:**
1. Expression syntax error
2. Item ID not available
3. Update action failed

**Solutions:**
1. Check expression in Compose action:
   ```
   concat('CCF-',formatDateTime(utcNow(),'yyyy'),'-',substring(concat('00000',string(triggerOutputs()?['body/ID'])),sub(length(concat('00000',string(triggerOutputs()?['body/ID']))),5),5))
   ```
2. Verify trigger output contains ID
3. Check SharePoint permissions for update

### Issue: Emails not sent

**Symptoms:**
- Flow completes but no emails received
- Email action shows error

**Possible Causes:**
1. Invalid email address
2. Email permissions not granted
3. Mailbox full
4. Spam filter blocking

**Solutions:**
1. Verify email addresses are correct
2. Check "Send As" permissions in Exchange
3. Check recipient mailbox capacity
4. Add sender to safe senders list
5. Check email action settings in flow

### Issue: Wrong department assigned

**Symptoms:**
- Complaint routed to incorrect department
- DepartmentResponsible field incorrect

**Possible Causes:**
1. Type field value not matching Compose logic
2. Choice field not extracting Value correctly

**Solutions:**
1. Use `?['Value']` to extract Choice field text:
   ```
   triggerOutputs()?['body/Type']?['Value']
   ```
2. Verify Type options match exactly (case-sensitive)
3. Check Compose action logic for all 10 types

### Issue: Adverse event alert not sent

**Symptoms:**
- Type = "Adverse Event" but no urgent alert
- Condition not triggering

**Possible Causes:**
1. Condition comparison incorrect
2. Type value not matching exactly

**Solutions:**
1. Verify condition uses exact match: `Adverse Event`
2. Check for extra spaces or different case
3. Use `?['Value']` to extract Choice field

---

## Flow 2: Email Intake

### Issue: Flow not triggering on new email

**Symptoms:**
- Email received but flow doesn't run
- No run history entry

**Possible Causes:**
1. Wrong mailbox address
2. Mailbox permissions not granted
3. Trigger not configured correctly
4. Flow is turned off

**Solutions:**
1. Verify mailbox address: `complaints@tpg.co.zw`
2. Grant mailbox access to flow owner
3. Use "When a new email arrives in a shared mailbox (V2)"
4. Turn flow On
5. Check connection authentication

### Issue: SharePoint item not created

**Symptoms:**
- Flow runs but no item in SharePoint
- HTTP request fails

**Possible Causes:**
1. Incorrect list name
2. Incorrect site URL
3. Invalid metadata type
4. Missing required fields
5. Permissions issue

**Solutions:**
1. Verify list name exactly: `Customer Feedback and Complaints Final`
2. Verify site URL: `https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final`
3. Verify metadata type: `SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem`
4. Check all required fields are in body
5. Grant flow owner Edit permissions on list

### Issue: Attachments not uploading

**Symptoms:**
- Email has attachments but not in SharePoint
- Apply to each fails

**Possible Causes:**
1. Attachment too large (>10MB)
2. Invalid file type
3. HTTP request syntax error
4. Permissions issue

**Solutions:**
1. Check file size limit in SharePoint
2. Verify file type is allowed
3. Verify HTTP request URI:
   ```
   _api/lists/getByTitle('Customer Feedback and Complaints Final')/items(@{body('Parse_-_Created_Item')?['d']?['Id']})/AttachmentFiles/add(FileName='@{items('Apply_to_each')?['name']}')
   ```
4. Use `contentBytes` not `contentUrl`

### Issue: Keyword detection not working

**Symptoms:**
- Type always defaults to "Query - General"
- Wrong type detected

**Possible Causes:**
1. Keywords not in email subject or body
2. toLower() not applied
3. Keywords too specific

**Solutions:**
1. Add more keywords to detection logic
2. Check both subject and bodyPreview
3. Use `toLower()` for case-insensitive matching
4. Broaden keyword list

### Issue: Parse JSON fails

**Symptoms:**
- Parse JSON action shows error
- Schema mismatch

**Possible Causes:**
1. HTTP response format changed
2. Schema doesn't match response
3. Item ID not in expected location

**Solutions:**
1. Copy actual HTTP response from run history
2. Use "Generate from sample" to create schema
3. Verify response structure matches schema

---

## Flow 3: Daily Reminders

### Issue: Flow not running at scheduled time

**Symptoms:**
- No run history at 8:00 AM
- Flow shows as On but doesn't run

**Possible Causes:**
1. Wrong time zone
2. Wrong days selected
3. Recurrence not configured
4. Flow turned off

**Solutions:**
1. Verify time zone: (UTC+02:00) Harare, Pretoria
2. Verify days: Monday-Friday only
3. Check recurrence settings
4. Turn flow On
5. Test manually first

### Issue: No complaints retrieved

**Symptoms:**
- Flow runs but no emails sent
- Apply to each has zero iterations

**Possible Causes:**
1. No open complaints in SharePoint
2. Filter too restrictive
3. HTTP request fails
4. Parse JSON fails

**Solutions:**
1. Check SharePoint for open complaints
2. Verify filter: `(Status ne 'Closed') and (Status ne 'Resolved')`
3. Check HTTP request URI and headers
4. Verify Parse JSON schema

### Issue: Days open calculated incorrectly

**Symptoms:**
- Wrong number of days shown in email
- Reminders sent at wrong time

**Possible Causes:**
1. Expression syntax error
2. DateReceived format incorrect
3. Time zone difference

**Solutions:**
1. Verify expression:
   ```
   div(sub(ticks(utcNow()),ticks(items('Apply_to_each')?['DateReceived'])),864000000000)
   ```
2. Ensure DateReceived is valid date/time
3. Use UTC for consistency

### Issue: Emails sent to wrong people

**Symptoms:**
- Reminder sent to wrong department
- Escalation sent to wrong managers

**Possible Causes:**
1. ResponsiblePerson field empty
2. Email address incorrect
3. Field not expanded in query

**Solutions:**
1. Verify ResponsiblePerson is populated in SharePoint
2. Check email addresses in flow
3. Add `$expand=ResponsiblePerson` to query
4. Use `?['ResponsiblePerson']?['EMail']` to extract email

### Issue: Escalation not sent at Day 5

**Symptoms:**
- Complaint 5+ days old but no escalation
- Only reminder sent

**Possible Causes:**
1. Condition logic incorrect
2. Days calculation wrong
3. Nested condition not configured

**Solutions:**
1. Verify condition: Days ≥ 5
2. Check nested condition structure
3. Ensure escalation is in "If yes" branch of 5+ days condition

---

## Flow 4: Resolution & Closure

### Issue: Flow triggers but no action taken

**Symptoms:**
- Flow runs on every modification
- No emails sent

**Possible Causes:**
1. Status not changed to Resolved or Closed
2. Conditions not matching
3. Get item fails

**Solutions:**
1. Verify Status field was actually changed
2. Check condition uses `?['Value']` for Choice fields
3. Verify Get item action succeeds

### Issue: FeedbackClosedDate not updated

**Symptoms:**
- Status = Closed but date field empty
- HTTP request fails

**Possible Causes:**
1. HTTP request syntax error
2. Permissions issue
3. Field name incorrect

**Solutions:**
1. Verify HTTP request method: POST with X-HTTP-Method: MERGE
2. Check IF-MATCH header: "*"
3. Verify field name: `FeedbackClosedDate`
4. Grant Edit permissions

### Issue: Satisfaction buttons don't work

**Symptoms:**
- Clicking button does nothing
- Email client doesn't open

**Possible Causes:**
1. mailto: link malformed
2. Email client not configured
3. Browser blocking mailto links

**Solutions:**
1. Verify mailto syntax:
   ```
   mailto:complaints@tpg.co.zw?subject=Satisfied - CCF-2026-00001&body=Yes, I am satisfied
   ```
2. Configure default email client
3. Test in different browser
4. Use URL encoding for special characters

### Issue: Resolution email not sent

**Symptoms:**
- Status changed to Resolved but no email
- Condition not triggering

**Possible Causes:**
1. Status value not matching exactly
2. Get item not retrieving current values
3. Email action fails

**Solutions:**
1. Check Status = "Resolved" (exact match, case-sensitive)
2. Ensure Get item action before conditions
3. Verify customer email address is valid
4. Check email action configuration

---

## Public Form Issues

### Issue: Form not loading

**Symptoms:**
- Blank page
- 404 error
- Loading spinner forever

**Possible Causes:**
1. Incorrect URL
2. Hosting server down
3. JavaScript error
4. CORS issue

**Solutions:**
1. Verify form URL is correct
2. Check web server status
3. Open browser console (F12) for errors
4. Check CORS headers in Power Automate

### Issue: Form submission fails

**Symptoms:**
- Error message after submit
- No item created in SharePoint
- "Unable to submit" message

**Possible Causes:**
1. Power Automate URL incorrect
2. HTTP trigger flow not running
3. CORS not configured
4. Network error

**Solutions:**
1. Verify Power Automate HTTP POST URL in JavaScript
2. Check flow is turned On
3. Add CORS headers to Response action
4. Test network connectivity
5. Check browser console for errors

### Issue: File upload not working

**Symptoms:**
- Can't select files
- Files not uploading
- Attachment not in SharePoint

**Possible Causes:**
1. File too large (>10MB)
2. Invalid file type
3. Base64 conversion fails
4. HTTP request fails

**Solutions:**
1. Check file size limit
2. Verify allowed file types in input accept attribute
3. Verify base64 conversion in JavaScript
4. Check attachment upload logic in Power Automate

### Issue: Success message not showing

**Symptoms:**
- Form submits but no confirmation
- Page doesn't reset

**Possible Causes:**
1. Response not received from Power Automate
2. JavaScript error
3. Success message display logic

**Solutions:**
1. Check Power Automate returns 200 status
2. Verify Response action in flow
3. Check JavaScript success handler
4. Open browser console for errors

---

## Email Issues

### Issue: Emails going to spam

**Symptoms:**
- Emails not received
- Found in spam/junk folder

**Possible Causes:**
1. SPF/DKIM not configured
2. Sender reputation low
3. Email content triggers spam filters

**Solutions:**
1. Configure SPF record for domain
2. Configure DKIM signing
3. Avoid spam trigger words
4. Add sender to safe senders list
5. Use authenticated SMTP

### Issue: HTML formatting broken

**Symptoms:**
- Email shows HTML code
- Styling not applied
- Layout broken

**Possible Causes:**
1. Email client doesn't support HTML
2. CSS not inline
3. HTML syntax error

**Solutions:**
1. Use inline CSS (not external stylesheets)
2. Test in multiple email clients
3. Validate HTML syntax
4. Use email-safe HTML (tables for layout)

### Issue: Email not sent to all recipients

**Symptoms:**
- Some recipients receive, others don't
- CC/BCC not working

**Possible Causes:**
1. Invalid email address
2. Mailbox full
3. Email limit reached
4. Syntax error in recipient list

**Solutions:**
1. Verify all email addresses
2. Check recipient mailbox capacity
3. Check daily email send limit (10,000 for Office 365)
4. Use semicolon (;) to separate multiple recipients

---

## SharePoint Issues

### Issue: Can't access SharePoint list

**Symptoms:**
- Access denied error
- List not found
- 403 Forbidden

**Possible Causes:**
1. Insufficient permissions
2. List deleted or renamed
3. Site URL incorrect

**Solutions:**
1. Grant Edit permissions to flow owner
2. Verify list exists
3. Check site URL is correct
4. Re-authenticate SharePoint connection

### Issue: Choice field values not saving

**Symptoms:**
- Field shows empty after save
- Invalid value error

**Possible Causes:**
1. Value not in Choice options
2. Extra spaces or different case
3. Multiple values when single expected

**Solutions:**
1. Verify value exactly matches Choice option
2. Trim spaces: `trim(variables('DetectedType'))`
3. Check field allows single value only

### Issue: Calculated column not calculating

**Symptoms:**
- DaysOpen shows #VALUE! or blank
- Formula error

**Possible Causes:**
1. Formula syntax error
2. Referenced field empty
3. Date format incorrect

**Solutions:**
1. Verify formula:
   ```
   =IF(ISBLANK([FeedbackClosedDate]),INT(NOW()-[DateReceived]),INT([FeedbackClosedDate]-[DateReceived]))
   ```
2. Ensure DateReceived has value
3. Check date format is Date/Time type

---

## Power Automate General Issues

### Issue: Flow runs slowly

**Symptoms:**
- Flow takes minutes to complete
- Timeout errors

**Possible Causes:**
1. Too many actions
2. Large attachments
3. Network latency
4. Apply to each with many items

**Solutions:**
1. Optimize flow logic
2. Remove unnecessary actions
3. Use parallel branches where possible
4. Limit Apply to each iterations
5. Increase timeout settings

### Issue: Flow fails intermittently

**Symptoms:**
- Sometimes works, sometimes fails
- Inconsistent errors

**Possible Causes:**
1. Network issues
2. Service throttling
3. Concurrent executions
4. Resource contention

**Solutions:**
1. Add retry policy to actions
2. Add delay between actions
3. Configure concurrency control
4. Check service health status

### Issue: Connection expired

**Symptoms:**
- Authentication error
- Connection not valid
- 401 Unauthorized

**Possible Causes:**
1. Password changed
2. Token expired
3. Permissions revoked

**Solutions:**
1. Re-authenticate connection
2. Update credentials
3. Check account permissions
4. Recreate connection if needed

---

## Monitoring and Diagnostics

### Check Flow Run History

1. Go to https://make.powerautomate.com
2. Click **My flows**
3. Click on flow name
4. Click **28-day run history**
5. Click on failed run
6. Expand each action to see inputs/outputs
7. Look for red X indicating failure
8. Read error message

### Enable Flow Analytics

1. Open flow
2. Click **Analytics** tab
3. View:
   - Run success rate
   - Average duration
   - Error trends
   - Most common errors

### Export Run History

1. In run history, click **Download CSV**
2. Analyze in Excel
3. Look for patterns in failures

### Test Individual Actions

1. Edit flow
2. Click **Test** button
3. Select **Manually**
4. Trigger the flow
5. Watch actions execute in real-time
6. See inputs/outputs for each action

---

## Performance Optimization

### Reduce Flow Execution Time

1. **Remove unnecessary actions**
   - Combine multiple Compose into one
   - Remove debug actions

2. **Use parallel branches**
   - Send multiple emails in parallel
   - Process independent actions simultaneously

3. **Optimize conditions**
   - Put most common conditions first
   - Use Switch instead of nested If/Else

4. **Limit data retrieved**
   - Use $select to get only needed fields
   - Use $top to limit results
   - Use $filter to reduce items

5. **Cache data**
   - Store frequently used data in variables
   - Avoid repeated HTTP requests

### Reduce Email Volume

1. **Batch notifications**
   - Send daily summary instead of per-item
   - Combine multiple complaints in one email

2. **Smart reminders**
   - Only send if no recent activity
   - Skip weekends and holidays

3. **Conditional emails**
   - Only send if priority is High or Critical
   - Skip emails for internal queries

---

## Emergency Procedures

### If System is Down

1. **Immediate Actions:**
   - Turn off all flows
   - Post notice on form
   - Notify staff

2. **Fallback Process:**
   - Monitor complaints@tpg.co.zw manually
   - Create SharePoint items manually
   - Send emails manually
   - Document all complaints

3. **Communication:**
   - Email customers about temporary issue
   - Provide alternative contact methods
   - Set auto-reply on shared mailbox

### If Data is Lost

1. **Check SharePoint Recycle Bin**
   - Site Contents → Recycle Bin
   - Restore deleted items

2. **Check Version History**
   - Open item → Version History
   - Restore previous version

3. **Check Flow Run History**
   - Export run history
   - Reconstruct data from inputs

4. **Contact IT Support**
   - Request backup restoration
   - Provide date/time of loss

---

## Getting Help

### Internal Support
- **System Administrator:** jkaseke@tpg.co.zw
- **IT Help Desk:** [Your IT contact]
- **SharePoint Admin:** [Your SharePoint admin]

### Microsoft Support
- **Power Automate Community:** https://powerusers.microsoft.com/t5/Power-Automate-Community/ct-p/MPACommunity
- **SharePoint Community:** https://techcommunity.microsoft.com/t5/sharepoint/ct-p/SharePoint
- **Microsoft Support:** https://support.microsoft.com

### Documentation
- **Power Automate Docs:** https://learn.microsoft.com/en-us/power-automate/
- **SharePoint Docs:** https://learn.microsoft.com/en-us/sharepoint/
- **This Project Docs:** `C:\Users\Joseph Kaseke\CascadeProjects\PulsePharma-ComplaintSystem\`

---

## Preventive Maintenance

### Weekly Tasks
- [ ] Review flow run history for errors
- [ ] Check SharePoint list for data quality
- [ ] Verify email addresses are current
- [ ] Test public form submission

### Monthly Tasks
- [ ] Review and optimize flow performance
- [ ] Update department email addresses
- [ ] Archive closed complaints (optional)
- [ ] Generate usage reports
- [ ] Review and update documentation

### Quarterly Tasks
- [ ] Full system test (all scenarios)
- [ ] User feedback survey
- [ ] Security review
- [ ] Backup configuration
- [ ] Update training materials

---

## Version Control

Keep track of changes to flows:

1. **Before making changes:**
   - Export flow as package
   - Save with version number
   - Document what you're changing

2. **After making changes:**
   - Test thoroughly
   - Update documentation
   - Notify users of changes

3. **If changes fail:**
   - Import previous version
   - Restore from backup
   - Document lessons learned

---

## Contact Information

For urgent issues or questions about this system:

- **Primary Contact:** jkaseke@tpg.co.zw
- **Backup Contact:** [Backup admin email]
- **After Hours:** [Emergency contact]

---

**Last Updated:** [Date]  
**Document Version:** 1.0  
**Maintained By:** [Your name]
