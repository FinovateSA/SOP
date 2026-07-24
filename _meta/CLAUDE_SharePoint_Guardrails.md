# SharePoint Integration & Knowledge Base Guardrails

*Reusable across all Finovate client Projects. Do not edit per client.*
*The only per-client variable — the client folder path — is set in the Project Instructions, not in this file (see §2).*

---

## 0. How this file works

This file defines fixed rules for how Claude uses the SharePoint connector and Project Knowledge in a Finovate client Project. It is identical in every Project. The one thing that changes per client — the authoritative client folder path — is read from the **Project Instructions** at runtime. This file references that value; it never hard-codes it.

The SharePoint connector must be enabled for the conversation for any of the retrieval rules below to apply. This file scopes *where* Claude looks; it does not activate the connector. If the connector is not enabled, enable it via the tools menu before relying on these rules.

---

## 1. Source-of-truth hierarchy

The **client card** — `00 Client Card/client-card.md` in the authorised client folder — is the primary briefing for all client questions. Read it at the start of every conversation and treat it as the current truth about the client; trust it over older documents elsewhere in the folder. If SharePoint is unreachable (connector not enabled), stop and tell the user — do not work on client matters without the card.

Project Knowledge holds this guardrails file, plus any reference files the team has deliberately promoted (§6). The rest of the Fin_Adv SharePoint client folder is the underlying raw-document repository, used for supplementary retrieval, verifying document currency, or sourcing material not yet distilled into the card.

---

## 2. Authorised folder scope

The authoritative client folder path for this Project is declared in the **Project Instructions** on a line beginning `CLIENT_FOLDER:`.

Read that value at the start of every conversation and treat it as the **sole authoritative SharePoint location** for this client's financial reports, contracts, registers, and structured deliverables. Do not run any SharePoint search until this value is known.

Read the path only from the Project Instructions — never from project memory or from a value recalled from a prior chat. Memory may be used to recognise which client a Project belongs to (a convenience), but the scope-critical path must always come verbatim from the Instructions line.

## 2a. Missing-path guard

If no `CLIENT_FOLDER:` value is present in the Project Instructions, **do not search SharePoint at all.** Do not substitute a guessed path, a client-name-only search, or a broad index search. Instead, run the first-run setup in §2b.

## 2b. First-run setup (when CLIENT_FOLDER is missing)

If no `CLIENT_FOLDER:` value is found, do not proceed with the user's task. Walk the user through setup:

1. Ask the user for the client's SharePoint folder — either the full path or the unique folder segment (e.g. `RUN001 - Run Jump Fly Creations`, not just the client name).
2. If given a partial name, use `sharepoint_folder_search` to locate it, show the user the matching path(s), and confirm the correct one before continuing. This verifies the folder exists and guards against partial-match siblings (e.g. a similarly named client).
3. Once confirmed, output the exact line for the user to paste into **Project Settings → Instructions**:
   ```
   CLIENT_FOLDER: <confirmed full path>
   ```
4. Tell the user this is a one-time step per Project, and remind them the SharePoint connector must be enabled via the tools menu.
5. Do not carry out the originally requested task until the user confirms the line has been saved to the Instructions.

---

## 3. Exclusion rule

Do not search personal OneDrive locations (paths containing `/personal/`) for this client's content unless a scoped search of the authorised folder returns no result. Personal folders may hold stale duplicates or draft copies not reflective of the current record.

---

## 4. Search discipline

Scope every `sharepoint_search` call to the authorised folder. Use the `folderName` parameter set to the folder name from `CLIENT_FOLDER:`, and/or a filename/keyword specific enough to avoid cross-client results.

To confirm or locate the folder before searching its contents, use `sharepoint_folder_search` with the folder name, then read the returned URI with `read_resource`.

Do not run unscoped single-word searches (e.g. a client name alone) against the full SharePoint index. `folderName` is a **partial match**, so it can catch sibling folders with similar names — if matching proves too broad, include enough of the full path in the `query` to disambiguate, and discard any result whose path is not under `CLIENT_FOLDER:`.

---

## 5. Fallback behaviour

If a requested document is not found within the authorised folder, **stop and report this to the user** rather than broadening the search to the full SharePoint site. Only widen scope on explicit user permission.

---

## 6. Promotion to Project Knowledge

When a SharePoint document becomes relevant to an active workstream:

1. Retrieve it.
2. Apply the appropriate processing skill (e.g. transcript-to-markdown for calls/recordings).
3. Save the result as a new file and **produce it for the user to upload** to Project Knowledge.
4. Produce an **updated Project Knowledge Index file** reflecting the addition, for the user to upload.

Claude cannot write back to Project Knowledge or SharePoint (connector access is read-only). Every promotion step ends with a file the user uploads manually — never assume auto-persistence.

---

## 7. Cross-client confidentiality

Never surface, summarise, or reference content from another client's folder within this Project, even if returned incidentally by a broad search. Discard any incidental cross-client result silently and do not act on it.

---

## 8. Query coaching

How the user phrases a request drives search cost and accuracy. Help them ask well:

1. If a request is ambiguous — no document named, several service lines could plausibly match, or the period is unclear (e.g. "the accounts", "that proposal") — ask **one short clarifying question** before searching, offering the likely readings: "The latest management accounts in Accounting, or the signed AFS?"
2. If the ambiguity is mild, state your assumption and proceed: "Taking the June 2026 management accounts — say if you meant another period."
3. Never resolve ambiguity by searching more broadly. Clarify or assume-and-state; widening scope is governed by §5.
4. When a document took more than one search attempt to find, tell the user its exact file name and subfolder afterwards, so they can name it directly next time.
5. Check the standard folder map (§9) and the client card's "where things live in this folder" notes before searching — if they answer the location, search that subfolder directly.

---

## 9. Standard client folder map

Every client folder follows the same skeleton. Go directly to the right subfolder instead of searching the whole client folder:

| Looking for | Search in |
|---|---|
| Client card / client briefing | `00 Client Card` |
| Engagement letter, proposals (any status), terms | `01 Admin & Proposals` |
| Information requests to/from the client | `02 Info Requests` |
| Management accounts, AFS, ledgers, reconciliations | `03 Accounting` |
| Tax returns, SARS correspondence, assessments | `04 Tax` |
| Payroll runs, payslips, EMP filings | `05 Payroll` |
| CIPC, registers, resolutions, company records | `06 Company Secretarial` |
| Advisory and consulting deliverables | `07 Consulting` |
| Meeting notes and summaries | `08 Meetings` |

Conventions to exploit when searching:

- Service-line folders may contain one level of year subfolders (e.g. `03 Accounting/2026/`) — nothing deeper.
- Meeting notes are markdown named date-first (`YYYY-MM-DD - topic.md`), so "the latest meeting" is the newest filename in `08 Meetings`, not a content search.
- Deliverables carry the client code and period (e.g. `HER010_2026-06_Management_Accounts.xlsx`) — include the code and period in the query when the user names one.
- Which proposal is the accepted, operative one is stated on the client card — check it before treating any proposal in `01 Admin & Proposals` as current.
- If a client folder predates this skeleton and the expected subfolder is missing, fall back to the client card's folder notes, then to a search scoped to the client folder as a whole (§4). Tell the user the folder doesn't match the standard so it can be fixed.
