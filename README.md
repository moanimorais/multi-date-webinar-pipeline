Readme · MDCopiarMulti-Date Webinar Delivery Pipeline
Automated end-to-end registration and delivery system for multi-date webinar events. Built for Live Life With Zest, a health coaching brand based in New York.

Overview
The core problem: sending 8 different Zoom access links — each with a unique meeting ID and password — without managing 8 different emails or risking human error.
The solution: a two-layer pipeline that captures registration data from Eventbrite via Zapier, routes each contact by event date inside GoHighLevel, injects the correct Zoom credentials into the contact record, and delivers a single master email that reads those credentials dynamically.

Stack
ToolRoleEventbriteEvent registration platformZapierIntegration layer — captures registration and pushes to GHLGoHighLevel (All In One Marketing)CRM, workflow automation, email delivery

Architecture
Eventbrite Registration
        |
        v
   Zapier Workflow
   (per event date)
   - Pulls: name, email, phone
   - Assigns: date-specific tag (e.g. webinar_sep_26)
   - Creates/updates contact in GHL
        |
        v
   GHL Workflow Trigger
   Tag Added — contact enters pipeline
        |
        v
   If/Else Branch — "Webinar's Dates"
   Evaluates which date tag the contact holds
        |
   _____|_____________________________
   |        |        |              |
Branch   Branch   Branch  ...   Branch
May 23  Jun 13   Jul 25       Dec 12
   |        |        |              |
   v        v        v              v
Update   Update   Update  ...   Update
Contact  Contact  Contact       Contact
Field    Field    Field         Field
(Zoom    (Zoom    (Zoom         (Zoom
link +   link +   link +        link +
password) password) password)  password)
   |        |        |              |
   v        v        v              v
        Go To (all paths converge)
                    |
                    v
           Master Email Template
     {{contact.zoom_access_details}}

How It Works
Layer 1 — Zapier
Each webinar date has a dedicated Zapier workflow. This was necessary because each Eventbrite event has a unique Event ID — Zapier uses that ID to listen to the correct event and pull the right registrations.
Each Zapier workflow:

Trigger: New Attendee Registered on Eventbrite (specific Event ID)
Action: Create or update contact in GoHighLevel via LeadConnector
Data mapped: first name, last name, email, phone
Tag applied: date-specific slug (e.g. webinar_sep_26)

The tag is what connects the Zapier layer to the GHL routing logic.
Layer 2 — GoHighLevel Workflow
Trigger: Contact Tag Added — system activates when the Zapier-applied tag arrives.
If/Else Branch ("Webinar's Dates"): Acts as the routing engine. Eight branches, one per date:
BranchConditionBranch — May 23Tag includes webinar_may_23Branch 1 — Jun 13Tag includes webinar_jun_13Branch 2 — Jul 25Tag includes webinar_jul_25Branch 3 — Aug 15Tag includes webinar_aug_15Branch 4 — Sep 26Tag includes webinar_sep_26Branch 5 — Oct 10Tag includes webinar_oct_10Branch 6 — Nov 28Tag includes webinar_nov_28Branch 7 — Dec 12Tag includes webinar_dec_12
Update Contact Field: Each branch runs an "Update Contact Field" action that injects the correct Zoom credentials into a custom field: zoom_access_details. This includes the full join link, meeting ID, and password specific to that date.
Go To: Every branch ends with a Go To connector pointing to the same email block. This ensures a single master template serves all 8 dates.
Master Email: Uses the merge tag {{contact.zoom_access_details}} to render the correct credentials at send time. Changing the email copy requires editing one block only.

Why This Design
The alternative — one workflow per date, one email per date — would mean 8x the maintenance work for every copy change, and a significant risk of sending the wrong Zoom link to the wrong attendee. Each webinar date uses a unique Zoom meeting ID and password specifically to prevent unauthorized access (Zoom bombing), so accuracy is non-negotiable.
This architecture reduces that to a single point of truth: one email template, one custom field, one place to update per new date added.

Key Results

8 webinar dates automated under a single email template
Zero manual sends — fully triggered by registration
Each attendee receives credentials specific to their registered date
Copy changes deploy in under 30 seconds (edit one block)
System is extensible: adding a new date requires one new branch, one field update, one Go To connection


Planned Extensions

Pre-event reminder workflow (GoHighLevel)

Trigger: 24 hours before event date
Trigger: 1 hour before event date
Same branch logic, same zoom_access_details field
Same convergence pattern — single reminder email template




Documentation
Full implementation documentation is available in the /docs folder:

/docs/implementation-report.pdf — Technical architecture, logic breakdown, and execution steps
/docs/team-guide.pdf — Step-by-step guide for updating workflows when adding new event dates


Status
Production — fully tested and live.
