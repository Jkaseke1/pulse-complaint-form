# HTML Form with Power Automate HTTP Trigger

## Overview
This solution uses the standalone HTML form with a Power Automate HTTP trigger to receive submissions directly. This is the most flexible and cost-effective solution.

## Architecture
```
HTML Form (hosted on web server)
    ↓ (HTTP POST with JSON)
Power Automate HTTP Trigger
    ↓
Create SharePoint Item
    ↓
Send Emails & Route to Department
```

## Advantages
✅ **Free** - No form service costs  
✅ **Full control** - Complete customization  
✅ **No external dependencies** - Direct to Power Automate  
✅ **Fast** - Direct integration  
✅ **Branding** - 100% custom design  
✅ **File uploads** - Supports attachments  

---

## Step 1: Create Power Automate HTTP Trigger Flow

### 1.1 Create New Flow

1. Go to https://make.powerautomate.com
2. Click **+ Create** → **Automated cloud flow**
3. Name: `HTTP Form - Complaint Intake`
4. Search for trigger: **When a HTTP request is received**
5. Click **Create**

### 1.2 Configure HTTP Trigger

1. Click on the trigger
2. **Request Body JSON Schema:**

```json
{
  "type": "object",
  "properties": {
    "category": {
      "type": "string"
    },
    "type": {
      "type": "string"
    },
    "customerName": {
      "type": "string"
    },
    "customerEmail": {
      "type": "string"
    },
    "customerPhone": {
      "type": "string"
    },
    "productName": {
      "type": "string"
    },
    "batchNumber": {
      "type": "string"
    },
    "expiryDate": {
      "type": "string"
    },
    "description": {
      "type": "string"
    },
    "dateReceived": {
      "type": "string"
    },
    "attachments": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string"
          },
          "contentBytes": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

3. **Method:** POST
4. Click **Save** (important!)
5. Copy the **HTTP POST URL** that appears (you'll need this for the HTML form)

### 1.3 Add Flow Actions

Add the same actions as Flow 2:

```
Step 1: Initialize Variables (DetectedType, DetectedDepartment, ResponsibleEmail)
Step 2: Set Variable - DetectedType = triggerBody()?['type']
Step 3: Compose - Route to Department
Step 4: Set Variable - DetectedDepartment
Step 5: Compose - Get Responsible Email
Step 6: Set Variable - ResponsibleEmail
Step 7: Compose - Determine Priority
Step 8: Compose - Determine Category
Step 9: Send HTTP Request - Create SharePoint Item
Step 10: Parse JSON - Get Created Item ID
Step 11: Compose - Generate Complaint Number
Step 12: Send HTTP Request - Update Complaint Number
Step 13: Condition - Check if Has Attachments
  If Yes:
    Apply to each - Process Attachments
      Send HTTP Request - Upload Attachment to SharePoint
Step 14: Condition - Check if Adverse Event
  If Yes:
    Send Email - Adverse Event Alert
Step 15: Send Email - Customer Acknowledgment
Step 16: Send Email - Department Assignment
Step 17: Response (HTTP) - Return success message
```

### 1.4 Add HTTP Response Action

**Important:** Add this at the end of the flow to respond to the HTML form:

**Action:** Response (Request connector)
- **Status Code:** `200`
- **Headers:**
  ```json
  {
    "Content-Type": "application/json",
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Methods": "POST, OPTIONS",
    "Access-Control-Allow-Headers": "Content-Type"
  }
  ```
- **Body:**
  ```json
  {
    "success": true,
    "message": "Complaint submitted successfully",
    "complaintNumber": "@{outputs('Compose_-_Complaint_Number')}"
  }
  ```

### 1.5 Handle CORS (Cross-Origin Requests)

Add a **Condition** at the start of the flow:

**Condition:** `triggerOutputs()?['headers']?['Origin']`  
**Operator:** `is not equal to`  
**Value:** (leave empty)

**If Yes branch:**
- Add **Response** action
- Status Code: `200`
- Headers: (same CORS headers as above)
- Body: (empty)
- **Terminate** action: Status = Succeeded

**If No branch:**
- Continue with rest of flow

This handles browser preflight OPTIONS requests.

---

## Step 2: Update HTML Form with Power Automate URL

1. Open `HTML-Public-Form.html`
2. Find this line (around line 267):
   ```javascript
   const powerAutomateUrl = 'YOUR_POWER_AUTOMATE_HTTP_TRIGGER_URL_HERE';
   ```
3. Replace with your actual HTTP POST URL from Power Automate:
   ```javascript
   const powerAutomateUrl = 'https://prod-12.eastus.logic.azure.com:443/workflows/abc123.../triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xyz789...';
   ```
4. Save the file

---

## Step 3: Host the HTML Form

You need to host the HTML file on a web server. Here are your options:

### Option A: Azure Static Web Apps (Recommended - Free)

1. Go to https://portal.azure.com
2. Create new **Static Web App**
3. Name: `pulse-pharma-complaints`
4. Region: Choose nearest
5. Deployment: Upload HTML file
6. URL will be: `https://pulse-pharma-complaints.azurestaticapps.net`

### Option B: GitHub Pages (Free)

1. Create GitHub repository: `pulse-pharma-complaints`
2. Upload `HTML-Public-Form.html`
3. Rename to `index.html`
4. Go to Settings → Pages
5. Enable GitHub Pages
6. URL will be: `https://yourusername.github.io/pulse-pharma-complaints`

### Option C: Company Web Server

1. Upload `HTML-Public-Form.html` to your web server
2. Place in public folder (e.g., `/complaints/index.html`)
3. URL will be: `https://yourcompany.com/complaints/`

### Option D: SharePoint (Limited)

1. Create SharePoint page
2. Add **Embed** web part
3. Paste HTML code
4. **Note:** JavaScript may be restricted by SharePoint security

### Option E: OneDrive/SharePoint Document Library (Not Recommended)

1. Upload HTML file to OneDrive
2. Share with "Anyone with the link"
3. **Note:** Users will need to download and open file (poor UX)

---

## Step 4: Test the Integration

### Test 1: Basic Submission

1. Open the hosted HTML form in browser
2. Fill in all required fields:
   - Category: Complaint
   - Type: Product Quality
   - Name: Test User
   - Email: test@example.com
   - Description: This is a test complaint
3. Click **Submit Complaint**
4. Verify:
   - ✅ Success message appears
   - ✅ Form resets
   - ✅ Power Automate flow runs
   - ✅ SharePoint item created
   - ✅ Emails sent

### Test 2: File Upload

1. Fill in form
2. Upload a test image (JPG/PNG)
3. Submit
4. Verify:
   - ✅ File appears in SharePoint attachments

### Test 3: Adverse Event

1. Fill in form
2. Select Type: **Adverse Event**
3. Submit
4. Verify:
   - ✅ Priority set to Critical
   - ✅ Urgent alert sent to MD/HR/Regulatory

### Test 4: Mobile Device

1. Open form on mobile phone
2. Test all fields and submission
3. Verify responsive design works

---

## Step 5: Security Considerations

### 5.1 Protect Power Automate URL

The HTTP POST URL contains a signature token that authorizes requests. Keep it secure:

- ❌ Don't commit to public GitHub repos
- ❌ Don't share publicly
- ✅ Use environment variables if possible
- ✅ Regenerate if compromised (in Power Automate trigger settings)

### 5.2 Add Rate Limiting (Optional)

To prevent abuse, add rate limiting in Power Automate:

1. Add **Compose** action at start
2. Expression: `triggerOutputs()?['headers']?['X-Forwarded-For']`
3. Store IP addresses in SharePoint list with timestamp
4. Check if same IP submitted > 5 times in last hour
5. If yes, terminate flow with error

### 5.3 Add CAPTCHA (Optional)

To prevent bots, add Google reCAPTCHA to HTML form:

1. Get reCAPTCHA keys from https://www.google.com/recaptcha
2. Add to HTML form:
   ```html
   <div class="g-recaptcha" data-sitekey="YOUR_SITE_KEY"></div>
   <script src="https://www.google.com/recaptcha/api.js"></script>
   ```
3. Verify token in Power Automate before processing

---

## Step 6: Monitoring and Troubleshooting

### View Flow Run History

1. Go to Power Automate
2. Click on flow: `HTTP Form - Complaint Intake`
3. Click **28-day run history**
4. View successful and failed runs
5. Click on any run to see detailed execution

### Common Errors

#### Error: 404 Not Found
- **Cause:** Power Automate URL is incorrect
- **Fix:** Copy URL again from trigger, update HTML form

#### Error: CORS Policy
- **Cause:** CORS headers not set correctly
- **Fix:** Add Response action with CORS headers (see Step 1.4)

#### Error: 400 Bad Request
- **Cause:** JSON schema mismatch
- **Fix:** Verify form data matches schema in trigger

#### Error: 500 Internal Server Error
- **Cause:** Error in Power Automate flow
- **Fix:** Check flow run history for specific error

#### Error: Timeout
- **Cause:** Flow takes too long (>120 seconds)
- **Fix:** Optimize flow, remove unnecessary actions

---

## Step 7: Embed on Company Website

### Full Page

```html
<iframe 
  src="https://yourcompany.com/complaints/" 
  width="100%" 
  height="1200px" 
  frameborder="0"
  title="Customer Complaints Form">
</iframe>
```

### Modal/Popup

```html
<button onclick="openComplaintForm()">Submit Complaint</button>

<div id="complaintModal" style="display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.5); z-index:9999;">
  <div style="position:relative; width:90%; max-width:800px; height:90%; margin:5% auto; background:white; border-radius:10px; overflow:hidden;">
    <button onclick="closeComplaintForm()" style="position:absolute; top:10px; right:10px; z-index:10000;">✕ Close</button>
    <iframe src="https://yourcompany.com/complaints/" width="100%" height="100%" frameborder="0"></iframe>
  </div>
</div>

<script>
function openComplaintForm() {
  document.getElementById('complaintModal').style.display = 'block';
}
function closeComplaintForm() {
  document.getElementById('complaintModal').style.display = 'none';
}
</script>
```

### Link Button

```html
<a href="https://yourcompany.com/complaints/" 
   target="_blank" 
   class="btn btn-primary"
   style="background:#0066cc; color:white; padding:15px 30px; text-decoration:none; border-radius:5px; display:inline-block; font-weight:bold;">
  📋 Submit Complaint or Feedback
</a>
```

---

## Step 8: Analytics (Optional)

### Option A: Google Analytics

Add to HTML form (before `</head>`):

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
  
  // Track form submission
  document.getElementById('complaintForm').addEventListener('submit', function() {
    gtag('event', 'form_submit', {
      'event_category': 'Complaint Form',
      'event_label': 'Submission'
    });
  });
</script>
```

### Option B: Application Insights

Add to HTML form:

```html
<script src="https://js.monitor.azure.com/scripts/b/ai.2.min.js"></script>
<script>
  var appInsights = window.appInsights || function(config) {
    // Application Insights initialization
  }({
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY"
  });
  
  appInsights.trackPageView();
</script>
```

---

## Comparison: HTTP Form vs Other Solutions

| Feature | HTTP Form | Google Forms | Typeform | Power Pages |
|---------|-----------|--------------|----------|-------------|
| **Cost** | Free (hosting) | Free | $25/month | $5/user/month |
| **Setup Time** | 1-2 hours | 30 minutes | 1 hour | 2-3 hours |
| **Customization** | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Maintenance** | Medium | Low | Low | Low |
| **File Upload** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Branding** | ✅ Full | ⚠️ Limited | ✅ Full | ✅ Full |
| **Mobile** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Analytics** | ⚠️ Manual | ⚠️ Basic | ✅ Advanced | ✅ Built-in |

---

## Recommendation

**Use HTTP Form + Power Automate if:**
- You have web hosting available
- You want full control over design and branding
- You're comfortable with HTML/CSS/JavaScript
- You want zero ongoing costs
- You need custom validation or logic

**This is the BEST solution for Pulse Pharmaceuticals because:**
1. ✅ Free (no monthly fees)
2. ✅ Full branding control
3. ✅ Direct SharePoint integration
4. ✅ File uploads work perfectly
5. ✅ No external dependencies
6. ✅ Professional appearance
7. ✅ Easy to maintain

---

## Next Steps

1. ✅ Create Power Automate HTTP trigger flow
2. ✅ Copy HTTP POST URL
3. ✅ Update HTML form with URL
4. ✅ Choose hosting option
5. ✅ Upload and test form
6. ✅ Add to company website
7. ✅ Train staff on monitoring
8. ✅ Promote to customers

---

## Support Files Included

- `HTML-Public-Form.html` - Ready-to-use form
- This guide - Complete setup instructions
- Flow configuration guides - Power Automate setup

All files are in the `/forms/` directory.
