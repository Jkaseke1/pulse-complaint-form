# Quick Start Guide - Pulse Pharmaceuticals Complaint Management System

## 🚀 Get Started in 3 Steps

### Step 1: Review What's Already Working ✅
Flow 1 (Acknowledgment & Assignment) is already deployed and working. Verify it's running:
1. Go to https://make.powerautomate.com
2. Find flow: `Customer Complaint - Acknowledgment & Assignment`
3. Check status is **On**

### Step 2: Deploy Remaining Flows (2-3 hours)
Follow these guides in order:
1. **Flow 2 - Email Intake** → `flows/Flow2-Email-Intake-Configuration.md`
2. **Flow 3 - Daily Reminders** → `flows/Flow3-Daily-Reminders-Configuration.md`
3. **Flow 4 - Resolution & Closure** → `flows/Flow4-Resolution-Closure-Configuration.md`

### Step 3: Choose & Deploy Public Form (30 min - 2 hours)
Pick one option:
- **RECOMMENDED:** HTML Form (free, full control) → `forms/Power-Automate-HTTP-Form.md`
- **FASTEST:** Google Forms (free, 30 min setup) → `forms/Google-Forms-Setup.md`
- **PREMIUM:** Typeform ($25/month, best UX) → `forms/Typeform-Setup.md`

See comparison: `forms/Forms-Comparison.md`

---

## 📋 What You Have

### Complete Documentation
```
PulsePharma-ComplaintSystem/
├── README.md                          ← Start here
├── QUICK-START.md                     ← This file
│
├── documentation/
│   ├── SharePoint-List-Structure.md   ← All 21 columns explained
│   ├── Department-Routing-Map.md      ← Type → Department mapping
│   ├── SLA-Requirements.md            ← 2-day response, 5-day resolution
│
├── flows/
│   ├── Flow2-Email-Intake-Configuration.md        ← Complete setup guide
│   ├── Flow3-Daily-Reminders-Configuration.md     ← Complete setup guide
│   ├── Flow4-Resolution-Closure-Configuration.md  ← Complete setup guide
│
├── forms/
│   ├── HTML-Public-Form.html                  ← Ready-to-use form
│   ├── Power-Automate-HTTP-Form.md            ← Setup guide (RECOMMENDED)
│   ├── Google-Forms-Setup.md                  ← Setup guide (FASTEST)
│   ├── Typeform-Setup.md                      ← Setup guide (PREMIUM)
│   ├── Forms-Comparison.md                    ← Compare all options
│
└── deployment/
    ├── Deployment-Guide.md            ← Full deployment process
    ├── Testing-Checklist.md           ← Test every feature
    ├── Troubleshooting.md             ← Fix common issues
```

---

## 🎯 System Overview

### What It Does
1. **Captures** complaints via SharePoint, Email, or Public Form
2. **Auto-generates** complaint numbers (CCF-2026-00001)
3. **Routes** to correct department based on type
4. **Tracks** SLA compliance (2-day response, 5-day resolution)
5. **Reminds** departments at Day 3
6. **Escalates** to MD/HR at Day 5
7. **Manages** resolution and closure workflow
8. **Alerts** urgently for adverse events

### 4 Power Automate Flows

| Flow | Status | Purpose |
|------|--------|---------|
| **Flow 1** | ✅ Working | Acknowledgment & Assignment |
| **Flow 2** | 🔧 Ready to deploy | Email Intake from complaints@tpg.co.zw |
| **Flow 3** | 🔧 Ready to deploy | Daily Reminders & Escalation |
| **Flow 4** | 🔧 Ready to deploy | Resolution & Closure |

---

## ⚡ Priority Actions

### Today
- [ ] Deploy Flow 2 (Email Intake) - **1 hour**
- [ ] Test email submission to complaints@tpg.co.zw
- [ ] Verify SharePoint item created

### This Week
- [ ] Deploy Flow 3 (Daily Reminders) - **30 minutes**
- [ ] Deploy Flow 4 (Resolution & Closure) - **30 minutes**
- [ ] Choose public form solution
- [ ] Deploy public form - **30 min to 2 hours**

### Next Week
- [ ] Train department managers
- [ ] Test all scenarios (see Testing-Checklist.md)
- [ ] Go live and announce to customers

---

## 🔑 Key Information

### SharePoint
- **Site:** https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final
- **List:** Customer Feedback and Complaints Final
- **Columns:** 21 (all configured)

### Email
- **Shared Mailbox:** complaints@tpg.co.zw
- **Default Contact:** jkaseke@tpg.co.zw

### Department Routing
| Type | Department | Email |
|------|------------|-------|
| Product Quality | Quality Assurance | qa.manager@tpg.co.zw |
| Adverse Event | Pharmacovigilance | pharmacovigilance@tpg.co.zw |
| Product Portfolio | Product Management | product.manager@tpg.co.zw |
| Account Services | Customer Service | cs.manager@tpg.co.zw |
| Delivery - Local | Logistics - Local | logistics.local@tpg.co.zw |
| Delivery - Out of Harare | Logistics - Regional | logistics.regional@tpg.co.zw |
| Query - Sales | Sales | sales.manager@tpg.co.zw |
| Query - Marketing | Marketing | marketing.manager@tpg.co.zw |
| Query - Product | Product Management | product.manager@tpg.co.zw |
| Query - General | Customer Service | jkaseke@tpg.co.zw |

### Escalation Contacts
- **MD:** md@tpg.co.zw
- **HR:** hr@tpg.co.zw
- **Regulatory:** regulatory@tpg.co.zw

---

## 🎨 Form Options Summary

### Option 1: HTML Form (RECOMMENDED)
- **Cost:** $0
- **Time:** 2 hours
- **Pros:** Full control, professional, zero cost
- **Best for:** Long-term solution

### Option 2: Google Forms (FASTEST)
- **Cost:** $0
- **Time:** 30 minutes
- **Pros:** Quick, easy, reliable
- **Best for:** Immediate deployment

### Option 3: Typeform (PREMIUM)
- **Cost:** $25/month
- **Time:** 1 hour
- **Pros:** Best UX, beautiful, analytics
- **Best for:** Customer experience priority

See detailed comparison: `forms/Forms-Comparison.md`

---

## 📊 SLA Requirements

| Metric | Target | Automation |
|--------|--------|------------|
| Initial Response | 2 working days | ✅ Automatic acknowledgment |
| Full Resolution | 5 working days | ✅ Reminders & escalations |
| Adverse Event Acknowledgment | 1 hour | ✅ Urgent alert to MD/HR |
| Adverse Event Assessment | 4 hours | ⚠️ Manual (alert sent) |
| Day 3 Reminder | Daily at 8 AM | ✅ Automatic |
| Day 5 Escalation | Daily at 8 AM | ✅ Automatic to MD/HR |

---

## 🧪 Testing Scenarios

### Must Test Before Go-Live
1. **Manual SharePoint entry** → Flow 1 triggers
2. **Email to complaints@tpg.co.zw** → Flow 2 creates item
3. **Public form submission** → Item created, emails sent
4. **Adverse Event** → Urgent alerts to MD/HR/Regulatory
5. **3-day old complaint** → Reminder sent
6. **5-day old complaint** → Escalation sent
7. **Status = Resolved** → Resolution email with satisfaction buttons
8. **Status = Closed** → Closure emails sent

Full checklist: `deployment/Testing-Checklist.md`

---

## 🆘 Need Help?

### Common Issues
- **Flow not triggering?** → Check it's turned On
- **SharePoint item not created?** → Verify list name and permissions
- **Emails not sent?** → Check email addresses and permissions
- **Attachments not uploading?** → Check file size (<10MB)

Full troubleshooting: `deployment/Troubleshooting.md`

### Support
- **System Admin:** jkaseke@tpg.co.zw
- **Documentation:** This folder
- **Power Automate Help:** https://make.powerautomate.com

---

## 📈 Success Metrics

Track these after go-live:
- Total complaints received
- Average resolution time
- % resolved within 5 days
- Customer satisfaction rate
- Complaints by type
- Complaints by department
- SLA compliance %

---

## 🎓 Training Resources

### For Department Managers
1. How to check SharePoint for new complaints
2. How to update Status field
3. How to add ResolutionSummary
4. How to close complaints
5. Understanding email notifications

### For Customers
1. How to submit complaints (form link)
2. What to expect (acknowledgment, timeline)
3. How to track complaint (reference number)
4. How to provide feedback (satisfaction buttons)

---

## 🚦 Deployment Phases

### Phase 1: Foundation (Complete ✅)
- SharePoint list created
- Flow 1 deployed and working

### Phase 2: Core Automation (This Week)
- Deploy Flow 2 (Email Intake)
- Deploy Flow 3 (Daily Reminders)
- Deploy Flow 4 (Resolution & Closure)

### Phase 3: Public Access (Next Week)
- Deploy public form
- Test end-to-end
- Train staff

### Phase 4: Go Live (Week 3)
- Announce to customers
- Monitor closely
- Gather feedback

### Phase 5: Optimize (Month 2)
- Review performance
- Optimize flows
- Add enhancements

---

## 💡 Pro Tips

1. **Start with Google Forms** if you need to go live immediately, then migrate to HTML form later
2. **Update email addresses** in all flows before deployment
3. **Test with real data** before announcing to customers
4. **Monitor daily** for the first week
5. **Keep documentation updated** as you make changes
6. **Export flows** as backup before making changes
7. **Use test complaints** with DateReceived in the past to test reminders/escalations

---

## 📞 Next Steps

1. **Read:** `deployment/Deployment-Guide.md` (comprehensive guide)
2. **Deploy:** Flows 2, 3, 4 (follow configuration guides)
3. **Choose:** Public form option (see Forms-Comparison.md)
4. **Test:** All scenarios (use Testing-Checklist.md)
5. **Train:** Department managers
6. **Launch:** Announce to customers

---

## ✅ Ready to Deploy?

**Checklist:**
- [ ] SharePoint list verified (21 columns)
- [ ] Flow 1 working
- [ ] Department email addresses updated
- [ ] Shared mailbox access granted
- [ ] Read Deployment-Guide.md
- [ ] Chosen public form solution
- [ ] Testing plan ready

**If all checked, proceed to:** `deployment/Deployment-Guide.md`

---

**System Created:** February 2026  
**SOP Reference:** MK-03-02.01  
**Maintained By:** jkaseke@tpg.co.zw  

**🎉 You have everything you need to deploy a world-class complaint management system!**
