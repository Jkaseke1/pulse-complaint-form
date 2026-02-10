# Google Forms Alternative Solution

## Overview
Google Forms is a free, reliable alternative for collecting customer complaints when Power Pages and Microsoft Forms have limitations.

## Advantages
✅ **Free** - No cost  
✅ **Public sharing** - Works with file uploads  
✅ **Easy to use** - Familiar interface  
✅ **Mobile friendly** - Responsive design  
✅ **File uploads** - Up to 10GB per file  
✅ **Integration** - Can connect to Power Automate via Google Sheets  

## Setup Instructions

### Step 1: Create Google Form

1. Go to https://forms.google.com
2. Click **+ Blank** to create new form
3. Title: `Customer Feedback & Complaints - Pulse Pharmaceuticals`
4. Description: `We value your feedback. Please complete this form to submit a complaint or query. We aim to respond within 2 working days.`

### Step 2: Add Form Fields

Add the following questions in order:

#### 1. Category
- **Type:** Multiple choice
- **Question:** Category *
- **Options:**
  - Complaint
  - Query
- **Required:** Yes

#### 2. Type
- **Type:** Dropdown
- **Question:** Type *
- **Options:**
  - Product Quality
  - Adverse Event
  - Product Portfolio
  - Account Services
  - Delivery - Local
  - Delivery - Out of Harare
  - Query - Sales
  - Query - Marketing
  - Query - Product
  - Query - General
- **Required:** Yes

#### 3. Customer Name
- **Type:** Short answer
- **Question:** Your Name *
- **Required:** Yes

#### 4. Customer Email
- **Type:** Short answer
- **Question:** Email Address *
- **Validation:** Response validation → Text → Email
- **Required:** Yes

#### 5. Customer Phone
- **Type:** Short answer
- **Question:** Phone Number
- **Required:** No

#### 6. Product Name
- **Type:** Short answer
- **Question:** Product Name
- **Description:** Name of the product involved (if applicable)
- **Required:** No

#### 7. Batch Number
- **Type:** Short answer
- **Question:** Batch Number
- **Description:** Product batch/lot number (if applicable)
- **Required:** No

#### 8. Expiry Date
- **Type:** Date
- **Question:** Product Expiry Date
- **Required:** No

#### 9. Description
- **Type:** Paragraph
- **Question:** Description *
- **Description:** Please provide detailed information about your complaint or query
- **Required:** Yes

#### 10. Evidence
- **Type:** File upload
- **Question:** Attachments (Photos/Documents)
- **Description:** Upload any supporting documents, photos, or evidence
- **Settings:**
  - Allow only specific file types: Images, PDFs, Word documents
  - Maximum number of files: 5
  - Maximum file size: 10 MB
- **Required:** No

### Step 3: Configure Form Settings

1. Click **Settings** (gear icon)
2. **General tab:**
   - ✅ Limit to 1 response (optional)
   - ✅ Collect email addresses (optional - for confirmation)
3. **Presentation tab:**
   - ✅ Show progress bar
   - Confirmation message: `Thank you! Your complaint has been submitted. You will receive a confirmation email shortly with your reference number.`
4. Click **Save**

### Step 4: Customize Theme

1. Click **Customize theme** (palette icon)
2. Choose header color: `#0066cc` (Pulse Pharmaceuticals blue)
3. Upload header image (optional): Company logo
4. Font style: Basic or Modern

### Step 5: Link to Google Sheets

1. Click **Responses** tab
2. Click **Link to Sheets** (green icon)
3. Create new spreadsheet: `Pulse Pharma - Customer Complaints`
4. Click **Create**

### Step 6: Get Public Link

1. Click **Send** button
2. Click **Link** icon (chain)
3. ✅ Check **Shorten URL**
4. Copy the link
5. Share this link on your website, email signature, etc.

---

## Integration with Power Automate

### Option A: Using Power Automate Connector (Recommended)

1. Go to https://make.powerautomate.com
2. Create new **Automated cloud flow**
3. Name: `Google Forms - Complaint Intake`
4. Trigger: **When a new response is submitted** (Google Forms)
5. Sign in to your Google account
6. Select your form: `Customer Feedback & Complaints - Pulse Pharmaceuticals`
7. Add action: **Get response details** (Google Forms)
   - Form Id: (automatically filled)
   - Response Id: (from trigger)
8. Continue with same logic as Flow 2 to create SharePoint item

### Option B: Using Google Sheets Connector

1. Go to https://make.powerautomate.com
2. Create new **Automated cloud flow**
3. Name: `Google Sheets - Complaint Intake`
4. Trigger: **When a row is added** (Excel Online - OneDrive) or use Google Sheets connector
5. Add actions to:
   - Read row data
   - Create SharePoint item
   - Send acknowledgment emails
   - Route to department

---

## Power Automate Flow Configuration

### Trigger: When a new response is submitted (Google Forms)

### Action 1: Get response details
- Form Id: (auto-populated)
- Response Id: (from trigger)

### Action 2: Initialize Variables
Same as Flow 2 (DetectedType, DetectedDepartment, ResponsibleEmail)

### Action 3-8: Compose Actions
Same routing logic as Flow 2

### Action 9: Send HTTP Request - Create SharePoint Item
```json
{
  "__metadata": {
    "type": "SP.Data.Customer_x0020_Feedback_x0020_and_x0020_Complaints_x0020_FinalListItem"
  },
  "DateReceived": "@{utcNow()}",
  "Category": "@{outputs('Get_response_details')?['body/r4c7d9e8f1234567']}",
  "Type": "@{outputs('Get_response_details')?['body/r5e8f9a0b2345678']}",
  "CustomerName": "@{outputs('Get_response_details')?['body/r6f9a0b1c3456789']}",
  "CustomerEmail": "@{outputs('Get_response_details')?['body/r7a0b1c2d4567890']}",
  "CustomerPhone": "@{outputs('Get_response_details')?['body/r8b1c2d3e5678901']}",
  "ProductName": "@{outputs('Get_response_details')?['body/r9c2d3e4f6789012']}",
  "BatchNumber": "@{outputs('Get_response_details')?['body/r0d3e4f5a7890123']}",
  "ExpiryDate": "@{outputs('Get_response_details')?['body/r1e4f5a6b8901234']}",
  "Description": "@{outputs('Get_response_details')?['body/r2f5a6b7c9012345']}",
  "DepartmentResponsible": "@{variables('DetectedDepartment')}",
  "Status": "Open",
  "Priority": "@{outputs('Compose_-_Determine_Priority')}"
}
```

**Note:** The `r4c7d9e8f1234567` format are Google Forms question IDs. You'll need to map these after creating the flow.

### Action 10-15: Same as Flow 2
- Generate complaint number
- Update SharePoint with complaint number
- Handle file attachments (if any)
- Send adverse event alert (if applicable)
- Send customer acknowledgment
- Send department assignment

---

## File Attachments Handling

Google Forms stores uploaded files in Google Drive. To transfer to SharePoint:

1. In Power Automate, after "Get response details"
2. Add **Apply to each** for file uploads
3. Inside loop:
   - **Get file content** (Google Drive)
   - **Send HTTP request** to SharePoint to upload attachment

---

## Embedding on Website

### Option 1: Direct Link
```html
<a href="YOUR_GOOGLE_FORM_URL" target="_blank" class="btn btn-primary">
  Submit Complaint or Feedback
</a>
```

### Option 2: Embedded iFrame
```html
<iframe 
  src="YOUR_GOOGLE_FORM_URL?embedded=true" 
  width="100%" 
  height="1200" 
  frameborder="0" 
  marginheight="0" 
  marginwidth="0">
  Loading…
</iframe>
```

### Option 3: QR Code
1. In Google Forms, click **Send**
2. Click **Link** icon
3. Click **Shorten URL**
4. Use a QR code generator (e.g., qr-code-generator.com)
5. Generate QR code from shortened URL
6. Print on marketing materials, posters, etc.

---

## Advantages vs Disadvantages

### ✅ Advantages
- Free and unlimited responses
- File uploads work without restrictions
- Public sharing enabled by default
- Mobile-friendly responsive design
- Automatic data collection in Google Sheets
- Easy integration with Power Automate
- Can embed on website
- QR code support
- Email notifications available
- Data export to Excel/CSV

### ❌ Disadvantages
- Requires Google account for admin
- Not directly integrated with SharePoint (needs Power Automate)
- Less control over branding compared to custom HTML
- File attachments stored in Google Drive (not SharePoint)
- Question IDs are cryptic (need mapping)
- External dependency on Google services

---

## Cost Comparison

| Solution | Cost | File Upload | Public Sharing | SharePoint Integration |
|----------|------|-------------|----------------|------------------------|
| **Google Forms** | Free | ✅ Yes | ✅ Yes | Via Power Automate |
| **Microsoft Forms** | Free | ❌ Breaks sharing | ❌ Not with files | Direct |
| **Power Pages** | $5/user/month | ✅ Yes | ✅ Yes | Direct |
| **HTML Form** | Free (hosting) | ✅ Yes | ✅ Yes | Via Power Automate |
| **Typeform** | $25/month | ✅ Yes | ✅ Yes | Via Power Automate |

---

## Recommendation

**Google Forms is the best free alternative** when:
- Power Pages licensing is not available
- Microsoft Forms file upload breaks public sharing
- You need a quick, reliable solution
- You're comfortable with Power Automate integration

**Use HTML Form** if:
- You have web hosting available
- You want full branding control
- You need custom validation logic
- You prefer self-hosted solution

---

## Testing Checklist

- [ ] Form loads correctly on desktop
- [ ] Form loads correctly on mobile
- [ ] All required fields enforce validation
- [ ] Email field validates email format
- [ ] File upload accepts images/PDFs
- [ ] File upload rejects invalid file types
- [ ] Confirmation message displays after submission
- [ ] Response appears in Google Sheets
- [ ] Power Automate flow triggers on submission
- [ ] SharePoint item created successfully
- [ ] Complaint number generated correctly
- [ ] Customer receives acknowledgment email
- [ ] Department receives assignment email
- [ ] Adverse events trigger urgent alert

---

## Support

For Google Forms help:
- https://support.google.com/docs/topic/9054603

For Power Automate Google Forms connector:
- https://learn.microsoft.com/en-us/connectors/googleforms/
