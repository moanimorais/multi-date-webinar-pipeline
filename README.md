# Multi-Date Webinar Delivery Pipeline

Automated end-to-end registration and delivery system for multi-date webinar events. Built for [Live Life With Zest](https://www.livelifewithzest.com), a health coaching brand based in New York.

---

## Overview

The core problem: sending 8 different Zoom access links — each with a unique meeting ID and password — without managing 8 different emails or risking human error.

The solution: a two-layer pipeline that captures registration data from Eventbrite via Zapier, routes each contact by event date inside GoHighLevel, injects the correct Zoom credentials into the contact record, and delivers a single master email that reads those credentials dynamically.

---

## Stack

| Tool | Role |
|---|---|
| Eventbrite | Event registration platform |
| Zapier | Integration layer — captures registration and pushes to GHL |
| GoHighLevel (All In One Marketing) | CRM, workflow automation, email delivery |

---

## How It Works

### Layer 1 — Zapier

Each webinar date has a dedicated Zapier workflow. This was necessary because each Eventbrite event has a unique Event ID — Zapier uses that ID to listen to the correct event and pull the right registrations.

Each Zapier workflow:
- Trigger: New Attendee Registered on Eventbrite (specific Event ID)
- Action: Create or update contact in GoHighLevel via LeadConnector
- Data mapped: first name, last name, email, phone
- Tag applied: date-specific slug (e.g. `webinar_sep_26`)

The tag is what connects the Zapier layer to the GHL routing logic.

### Layer 2 — GoHighLevel Workflow

**Trigger:** Contact Tag Added — system activates when the Zapier-applied tag arrives.

**If/Else Branch ("Webinar's Dates"):** Acts as the routing engine. Eight branches, one per date:

| Branch | Condition |
|---|---|
| Branch — May 23 | Tag includes `webinar_may_23` |
| Branch 1 — Jun 13 | Tag includes `webinar_jun_13` |
| Branch 2 — Jul 25 | Tag includes `webinar_jul_25` |
| Branch 3 — Aug 15 | Tag includes `webinar_aug_15` |
| Branch 4 — Sep 26 | Tag includes `webinar_sep_26` |
| Branch 5 — Oct 10 | Tag includes `webinar_oct_10` |
| Branch 6 — Nov 28 | Tag includes `webinar_nov_28` |
| Branch 7 — Dec 12 | Tag includes `webinar_dec_12` |

**Update Contact Field:** Each branch injects the correct Zoom credentials into the custom field `zoom_access_details` on the contact record.

**Go To:** Every branch ends with a Go To connector pointing to the same email block.

**Master Email:** Uses the merge tag `{{contact.zoom_access_details}}` to render the correct credentials at send time.

---

## Why This Design

The alternative — one workflow per date, one email per date — would mean 8x the maintenance work for every copy change, and a significant risk of sending the wrong Zoom link to the wrong attendee. Each webinar date uses a unique Zoom meeting ID and password specifically to prevent unauthorized access (Zoom bombing), so accuracy is non-negotiable.

This architecture reduces that to a single point of truth: one email template, one custom field, one place to update per new date added.

---

## Key Results

- 8 webinar dates automated under a single email template
- Zero manual sends — fully triggered by registration
- Each attendee receives credentials specific to their registered date
- Copy changes deploy in under 30 seconds (edit one block)
- System is extensible: adding a new date requires one new branch, one field update, one Go To connection

---

## Planned Extensions

- Pre-event reminder workflow (GoHighLevel)
  - Trigger: 24 hours before event date
  - Trigger: 1 hour before event date
  - Same branch logic, same `zoom_access_details` field
  - Same convergence pattern — single reminder email template

---

## Documentation

- [Implementation Report](./docs/implementation-report.pdf) — Technical architecture, logic breakdown, and execution steps
- [Team Guide](./docs/Team%20Guide.pdf) — Step-by-step guide for updating workflows when adding new event dates

---

## Status

Production — fully tested and live.
```

---

Cole isso no modo **"Code"**, depois clique em **"Preview"** para conferir antes de salvar.
