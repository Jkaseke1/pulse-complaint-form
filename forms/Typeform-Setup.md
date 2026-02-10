# Typeform Alternative Solution

## Overview
Typeform is a premium form builder with beautiful UI/UX, ideal for customer-facing complaint forms. It offers a conversational, one-question-at-a-time interface.

## Advantages
✅ **Beautiful design** - Modern, engaging interface  
✅ **Conversational flow** - One question at a time  
✅ **File uploads** - Works with public sharing  
✅ **Logic jumps** - Conditional questions  
✅ **Branding** - Custom colors, logos, backgrounds  
✅ **Mobile optimized** - Excellent mobile experience  
✅ **Analytics** - Built-in response analytics  
✅ **Integrations** - Direct Power Automate connector  

## Pricing
- **Free Plan:** 10 responses/month (not suitable)
- **Basic Plan:** $25/month - 100 responses/month
- **Plus Plan:** $50/month - 1,000 responses/month
- **Business Plan:** $83/month - 10,000 responses/month

**Recommendation:** Basic Plan ($25/month) for most small-medium businesses

---

## Setup Instructions

### Step 1: Create Typeform Account

1. Go to https://www.typeform.com
2. Sign up for account (use company email)
3. Choose **Basic Plan** or start with free trial
4. Verify email address

### Step 2: Create New Typeform

1. Click **+ New typeform**
2. Choose **Start from scratch**
3. Title: `Customer Feedback & Complaints`
4. Click **Create**

### Step 3: Add Welcome Screen

1. Click **+ Add field** → **Welcome screen**
2. Title: `We Value Your Feedback`
3. Description:
   ```
   Thank you for contacting Pulse Pharmaceuticals.
   
   Your complaint or feedback is important to us. We aim to:
   • Respond within 2 working days
   • Resolve issues within 5 working days
   
   This form takes approximately 3-5 minutes to complete.
   ```
4. Button text: `Get Started`
5. Add company logo (click image icon)

### Step 4: Add Form Questions

#### Question 1: Category
- **Type:** Multiple choice
- **Question:** `What type of submission is this?`
- **Choices:**
  - 🔴 Complaint
  - 💬 Query
- **Required:** Yes

#### Question 2: Type
- **Type:** Dropdown
- **Question:** `Please select the specific type`
- **Choices:**
  - Product Quality
  - Adverse Event ⚠️
  - Product Portfolio
  - Account Services
  - Delivery - Local
  - Delivery - Out of Harare
  - Query - Sales
  - Query - Marketing
  - Query - Product
  - Query - General
- **Required:** Yes

#### Question 3: Customer Name
- **Type:** Short text
- **Question:** `What is your name?`
- **Required:** Yes

#### Question 4: Customer Email
- **Type:** Email
- **Question:** `What is your email address?`
- **Description:** `We'll send you a confirmation and updates here`
- **Required:** Yes
- **Validation:** Email format (automatic)

#### Question 5: Customer Phone
- **Type:** Phone number
- **Question:** `What is your phone number?`
- **Description:** `Optional - in case we need to reach you`
- **Required:** No
- **Default country:** Zimbabwe (+263)

#### Question 6: Product Name
- **Type:** Short text
- **Question:** `Which product is this about?`
- **Description:** `Product name (if applicable)`
- **Required:** No

#### Question 7: Batch Number
- **Type:** Short text
- **Question:** `What is the batch number?`
- **Description:** `Found on the product packaging (if applicable)`
- **Required:** No

#### Question 8: Expiry Date
- **Type:** Date
- **Question:** `What is the product expiry date?`
- **Description:** `Found on the product packaging (if applicable)`
- **Required:** No

#### Question 9: Description
- **Type:** Long text
- **Question:** `Please describe your complaint or query in detail`
- **Description:** `The more details you provide, the better we can help you`
- **Required:** Yes
- **Min characters:** 20

#### Question 10: Evidence
- **Type:** File upload
- **Question:** `Do you have any photos or documents to share?`
- **Description:** `Upload images, PDFs, or documents (optional)`
- **Required:** No
- **Max files:** 5
- **Max file size:** 10 MB
- **Allowed types:** Images, PDF, Word

### Step 5: Add Thank You Screen

1. Click **+ Add field** → **Ending**
2. Title: `Thank You! 🎉`
3. Description:
   ```
   Your complaint has been submitted successfully.
   
   ✓ You will receive a confirmation email shortly with your reference number
   ✓ We aim to respond within 2 working days
   ✓ Full resolution within 5 working days
   
   If you have urgent concerns, please contact us at:
   📧 complaints@tpg.co.zw
   📞 [Your phone number]
   ```
4. Button text: `Submit Another` (optional)
5. **Share icons:** Add social media sharing (optional)

---

## Step 6: Customize Design

### Theme Settings
1. Click **Design** tab (paintbrush icon)
2. **Colors:**
   - Primary color: `#0066cc` (Pulse Pharmaceuticals blue)
   - Background: White or light gradient
   - Text: Dark gray `#333333`
3. **Font:**
   - Choose professional font (e.g., Inter, Roboto, Open Sans)
4. **Background:**
   - Upload company background image, or
   - Use solid color, or
   - Use gradient
5. **Logo:**
   - Upload company logo (top-left corner)

### Layout Settings
1. **Question layout:** One question at a time (default)
2. **Progress bar:** Show progress bar ✅
3. **Navigation:** Show keyboard shortcuts ✅
4. **Branding:** Hide Typeform branding (requires Plus plan)

---

## Step 7: Configure Logic (Optional)

Add conditional logic for better user experience:

### Logic Example 1: Adverse Event Alert
- **If** Type = "Adverse Event"
- **Then** Show additional question:
  - Type: Statement
  - Text: `⚠️ IMPORTANT: Adverse events are treated as urgent. You will receive a response within 1 hour.`

### Logic Example 2: Product-Related Questions
- **If** Type contains "Product" or "Quality" or "Adverse"
- **Then** Make Product Name, Batch Number, Expiry Date **required**

To add logic:
1. Click on question
2. Click **Logic** tab
3. Add conditions and actions

---

## Step 8: Configure Settings

1. Click **Settings** (gear icon)
2. **Responses:**
   - ✅ Allow multiple submissions
   - ✅ Show progress bar
   - ✅ Show question numbers
3. **Notifications:**
   - ✅ Email me when someone submits
   - Email: `jkaseke@tpg.co.zw`
4. **Messages:**
   - Closed message: (if you need to temporarily close form)
   - Limit reached: (if you set response limit)

---

## Step 9: Publish and Share

1. Click **Publish** button (top-right)
2. Click **Share**
3. **Share options:**

### Option A: Direct Link
- Copy the link
- Share via email, SMS, social media
- Example: `https://form.typeform.com/to/abc123xyz`

### Option B: Embed on Website
```html
<!-- Full page embed -->
<div data-tf-widget="abc123xyz" style="width:100%;height:500px;"></div>
<script src="//embed.typeform.com/next/embed.js"></script>

<!-- Popup button -->
<button data-tf-popup="abc123xyz" data-tf-size="70">Submit Complaint</button>
<script src="//embed.typeform.com/next/embed.js"></script>

<!-- Slide-in panel -->
<button data-tf-slider="abc123xyz" data-tf-position="right" data-tf-medium="snippet">
  Contact Us
</button>
<script src="//embed.typeform.com/next/embed.js"></script>
```

### Option C: QR Code
1. In Share menu, click **QR code**
2. Download QR code image
3. Print on marketing materials

### Option D: Social Media
- Share directly to Facebook, Twitter, LinkedIn
- Typeform generates preview cards automatically

---

## Integration with Power Automate

### Step 1: Connect Typeform to Power Automate

1. Go to https://make.powerautomate.com
2. Create new **Automated cloud flow**
3. Name: `Typeform - Complaint Intake`
4. Search for trigger: **When a response is submitted** (Typeform)
5. Sign in to Typeform account
6. Select your form: `Customer Feedback & Complaints`

### Step 2: Configure Flow

The flow structure is similar to Flow 2, but uses Typeform response data:

```
Trigger: When a response is submitted (Typeform)
↓
Action 1: Initialize Variables (DetectedType, DetectedDepartment, ResponsibleEmail)
↓
Action 2-7: Compose Actions (routing logic)
↓
Action 8: Send HTTP Request - Create SharePoint Item
Body:
{
  "__metadata": { "type": "SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem" },
  "DateReceived": "@{utcNow()}",
  "Category": "@{triggerBody()?['answers']?[0]?['choice']?['label']}",
  "Type": "@{triggerBody()?['answers']?[1]?['choice']?['label']}",
  "CustomerName": "@{triggerBody()?['answers']?[2]?['text']}",
  "CustomerEmail": "@{triggerBody()?['answers']?[3]?['email']}",
  "CustomerPhone": "@{triggerBody()?['answers']?[4]?['phone_number']}",
  "ProductName": "@{triggerBody()?['answers']?[5]?['text']}",
  "BatchNumber": "@{triggerBody()?['answers']?[6]?['text']}",
  "ExpiryDate": "@{triggerBody()?['answers']?[7]?['date']}",
  "Description": "@{triggerBody()?['answers']?[8]?['text']}",
  "DepartmentResponsible": "@{variables('DetectedDepartment')}",
  "Status": "Open",
  "Priority": "@{outputs('Compose_-_Determine_Priority')}"
}
↓
Action 9-15: Generate complaint number, update SharePoint, send emails (same as Flow 2)
```

### Step 3: Handle File Attachments

Typeform stores files temporarily. To transfer to SharePoint:

```
Apply to each: triggerBody()?['answers']?[9]?['file_url']
  ↓
  HTTP Request: GET file from Typeform URL
  ↓
  Send HTTP Request: Upload to SharePoint attachments
```

---

## Advanced Features

### 1. Webhooks (Alternative to Power Automate)
- Typeform can send data directly to custom endpoint
- Settings → Webhooks → Add webhook URL
- Useful if you have custom API

### 2. Hidden Fields
- Pass data via URL parameters
- Example: `https://form.typeform.com/to/abc123?source=website&campaign=q1`
- Access in Power Automate: `triggerBody()?['hidden']?['source']`

### 3. Calculations
- Add calculated fields
- Example: Auto-calculate priority based on type

### 4. Integrations
- Direct integrations available:
  - Slack (instant notifications)
  - Google Sheets (automatic logging)
  - Mailchimp (add to mailing list)
  - Zapier (connect to 5000+ apps)

---

## Analytics & Reporting

Typeform provides built-in analytics:

1. **Responses tab:**
   - View all submissions
   - Filter by date, question, answer
   - Export to Excel/CSV/Google Sheets

2. **Results tab:**
   - Completion rate
   - Average time to complete
   - Drop-off points
   - Question-by-question analysis

3. **Insights:**
   - Most common answers
   - Response trends over time
   - Device breakdown (mobile vs desktop)

---

## Comparison: Typeform vs Other Solutions

| Feature | Typeform | Google Forms | Microsoft Forms | HTML Form |
|---------|----------|--------------|-----------------|-----------|
| **Cost** | $25/month | Free | Free | Free (hosting) |
| **Design** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ (custom) |
| **User Experience** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ (custom) |
| **File Upload** | ✅ Yes | ✅ Yes | ❌ Breaks sharing | ✅ Yes |
| **Logic Jumps** | ✅ Yes | ✅ Yes | ✅ Yes | ⚠️ Manual |
| **Branding** | ✅ Full | ⚠️ Limited | ⚠️ Limited | ✅ Full |
| **Analytics** | ✅ Advanced | ⚠️ Basic | ⚠️ Basic | ❌ None |
| **Mobile** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ (if coded) |
| **Integration** | ✅ Native | ✅ Native | ✅ Native | ⚠️ Custom |

---

## Recommendation

**Use Typeform if:**
- Budget allows ($25/month)
- Customer experience is priority
- You want professional, branded forms
- You need advanced analytics
- You want logic jumps and conditional questions
- Mobile experience is critical

**Don't use Typeform if:**
- Budget is very tight (use Google Forms)
- You need 100% free solution
- You have very high response volume (>1000/month on Basic plan)

---

## Testing Checklist

- [ ] Form loads correctly on desktop
- [ ] Form loads correctly on mobile
- [ ] All required fields enforce validation
- [ ] Email field validates email format
- [ ] Phone field validates phone format
- [ ] File upload accepts images/PDFs
- [ ] File upload rejects invalid file types
- [ ] Logic jumps work correctly
- [ ] Progress bar displays correctly
- [ ] Thank you screen shows after submission
- [ ] Power Automate flow triggers on submission
- [ ] SharePoint item created successfully
- [ ] Complaint number generated correctly
- [ ] Customer receives acknowledgment email
- [ ] Department receives assignment email
- [ ] Adverse events trigger urgent alert
- [ ] File attachments transfer to SharePoint

---

## Support

- **Typeform Help Center:** https://help.typeform.com
- **Power Automate Typeform Connector:** https://learn.microsoft.com/en-us/connectors/typeform/
- **Typeform Community:** https://community.typeform.com
- **Typeform Templates:** https://typeform.com/templates

---

## Free Trial

Typeform offers a 14-day free trial of the Plus plan:
- Test all features before committing
- No credit card required for trial
- Downgrade to Basic after trial if needed
