# Lead System v1

Lead capture and follow-up automation for local businesses (coaching institutes, gyms, service businesses), built with **n8n**, **Google Sheets** and **Gmail**.

It makes sure every enquiry is saved, checked, followed up quickly, and reported on, without anyone copying data by hand.

## What it does for a business

- **Every enquiry is saved** in one Google Sheet, with clean phone numbers and emails.
- **Repeat enquiries are caught**: the existing lead is flagged "call today" instead of creating a duplicate.
- **The counsellor is alerted instantly**, with a one-tap WhatsApp link and a ready message.
- **No lead is forgotten**: anyone still uncontacted after 1 hour is chased every hour (9 am – 9 pm).
- **Students who join are welcomed** automatically, exactly once.
- **The owner gets a weekly report** every Monday: leads, sources, status and the ad money numbers.
- **Failures are never silent**: if any workflow fails, an alert email and a "Failed" log row are created.

## How it works

```
Form → clean data → check phone & email → dedupe (find by phone)
     → save lead / mark repeat → alert counsellor + email student + log
Every hour  → find leads uncontacted > 1 hr → alert counsellor → log
Status = Joined → welcome email → mark welcomed → log
Every Monday 9 am → read leads + ad results → weekly report email → log
Any failure → Error Alerts → alert email + "Failed" log row
```

| Workflow | Trigger | Purpose |
|---|---|---|
| `Funnel.json` | Form submission | Clean, validate, dedupe and save leads; alert counsellor; email the student |
| `Uncontacted_Lead_Alert.json` | Every hour | Chase leads still "New" after 1 hour (working hours only) |
| `Joined_Welcome.json` | Sheet row updated (status) | Welcome email when status becomes "Joined", sent only once |
| `Weekly_Lead_Report.json` | Every Monday, 9 am | Weekly summary email with lead counts and ad cost numbers |
| `Error_Alerts.json` | Another workflow fails | Alert email + "Failed" row in the Log |

## Reliability built in

- **Retry On Fail** (3 tries, 3–5 s apart) on every Google Sheets and Gmail step, so short outages and rate limits don't lose data.
- **Error workflow** connected to all live workflows.
- **Log tab**: every important action writes a row (time, workflow, event, details, status), so the business can see what happened and when.
- **Data quality check**: phone numbers that are not 10 digits, don't start with 6–9, or look fake are flagged for the counsellor.

## Tech used

n8n (self-hosted) · Google Sheets · Gmail · JavaScript (Code nodes) · Luxon dates (Asia/Kolkata time zone)

## Set it up

1. **Make the Google Sheet** with two tabs (column names must match exactly; see `sample-data/`):
   - `Sheet1`: name, phone, course, budget, status, next_step, created_at, Email, source, data_quality, welcome_sent
   - `Log`: time, workflow, event, details, status
2. **Import the workflows**: in n8n, create a workflow → ⋯ → Import from file → choose each `.json` file.
3. **Connect accounts**: Google Sheets, Google Sheets Trigger and Gmail (each node shows which one it needs).
4. **Point to your sheet**: in every Google Sheets node, pick your sheet (replaces `YOUR_LEADS_SHEET_ID`). The weekly report also reads an "Ad Results" tab (`YOUR_AD_RESULTS_SHEET_ID`).
5. **Set email addresses**: replace `owner@example.com` and `counsellor@example.com`.
6. **Fill the placeholders** in square brackets (institute name, mentor name, batch date, venue).
7. **Set the error workflow**: in each workflow's Settings, choose "Error Alerts" as the Error workflow.
8. **Test with fake data only**, then publish.

## Limits and what's next

- v1 runs on self-hosted n8n; for 24/7 use it needs a server (VPS or n8n Cloud).
- The weekly report's ad numbers come from a sheet updated from Ads Manager.
- **v2 (planned):** Meta lead-form intake (webhook with secret header + fetching lead details from Meta), WhatsApp Business messages, and an AI lead classifier.

## Privacy

No real customer data, sheet IDs, credentials or email addresses are stored in this repository. All workflow files were cleaned before upload.

---
Built by **Likith D** as part of a client-ready automation portfolio.
