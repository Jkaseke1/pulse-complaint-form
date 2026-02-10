# SharePoint List Structure

## List Name
**Customer Feedback and Complaints Final**

## Full URL
https://pulsepharmeceuticalszw.sharepoint.com/sites/Pulse-Intranet-Final/Lists/Customer%20Feedback%20and%20Complaints%20Final/AllItems.aspx

## Columns Configuration

| Column Name | Type | Required | Notes |
|-------------|------|----------|-------|
| ComplaintNo | Single line of text | No | Auto-generated: CCF-2026-00001 |
| DateReceived | Date and time | Yes | When complaint submitted |
| Category | Choice | Yes | Complaint, Query |
| Type | Choice | Yes | See Type Options below |
| CustomerName | Single line of text | Yes | |
| CustomerEmail | Single line of text | Yes | |
| CustomerPhone | Single line of text | No | |
| ProductName | Single line of text | No | Product involved |
| BatchNumber | Single line of text | No | Product batch/lot |
| ExpiryDate | Date and time | No | Product expiry |
| Description | Multiple lines of text | Yes | Full complaint details |
| DepartmentResponsible | Choice | No | Auto-assigned by flow |
| ResponsiblePerson | Person | No | Auto-assigned by flow |
| AssignedTo | Person | No | Team member handling |
| Status | Choice | No | Default: Open |
| Priority | Choice | No | Auto-assigned by flow |
| ResolutionSummary | Multiple lines of text | No | How resolved |
| FeedbackClosedDate | Date and time | No | When closed |
| Evidence | Attachments | No | Photos/documents |
| DaysOpen | Calculated | No | Formula below |
| CustomerSatisfied | Choice | No | Yes, No, Pending |

## Type Options (Choice Field)
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

## Status Options (Choice Field)
- Open (default)
- Under Review
- Awaiting Response
- Resolved
- Closed

## Priority Options (Choice Field)
- Low
- Medium
- High
- Critical

## DepartmentResponsible Options (Choice Field)
- Quality Assurance
- Pharmacovigilance
- Product Management
- Customer Service
- Sales
- Marketing
- Logistics - Local
- Logistics - Regional

## CustomerSatisfied Options (Choice Field)
- Yes
- No
- Pending (default)

## Calculated Column Formula

**DaysOpen:**
```
=IF(ISBLANK([FeedbackClosedDate]),INT(NOW()-[DateReceived]),INT([FeedbackClosedDate]-[DateReceived]))
```

## List Settings
- **Versioning:** Enabled (recommended)
- **Attachments:** Enabled
- **Content Approval:** Not required
- **Item-level Permissions:** Default (all items)
