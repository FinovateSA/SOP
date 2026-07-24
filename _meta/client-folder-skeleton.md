# Standard client folder skeleton

Agreed 24 July 2026 (Milan). Deploy this structure for every client (and use the
same names in the potential-client flow so folders convert seamlessly on win).

```
[CODE] - [Client Legal Name (Pty) Ltd]/     e.g. HER010 - Hermanus Trading And Logistics (Pty) Ltd
  00 Client Card/            client-card.md — the standing briefing Claude reads first, every chat
  01 Admin & Proposals/      engagement letter + all proposals (draft, sent, accepted, declined)
  02 Info Requests/
  03 Accounting/
  04 Tax/
  05 Payroll/
  06 Company Secretarial/
  07 Consulting/
  08 Meetings/               summarised meeting notes as .md, date-first names
```

## Rules

- **Depth:** at most one year subfolder inside a service line (`03 Accounting/2026/`). No deeper.
- **Proposals** are terms documents, not service-line documents — they all live in
  `01 Admin & Proposals` next to the engagement letter. The client card's Engagement
  section records which proposal is the accepted, operative one. Delivered work goes
  in its service-line folder.
- **Meetings:** one folder for all meetings (they span topics). Store the summarised
  markdown, named `YYYY-MM-DD - [topic].md`. Raw Teams transcripts stay in Teams —
  the connector can retrieve them when the verbatim record matters.
- **File naming:** deliverables carry client code + period
  (`HER010_2026-06_Management_Accounts.xlsx`); time-series documents are date-first;
  never encode status in a filename (it changes); no "final_v2".

## Why this shape

The client code prefix is a unique search token; consistent numbered names make
Claude's scoped searches and human navigation predictable; `00 Client Card` exists
from day one so the card's future move out of Project files is a file-drop, not a
restructure.
