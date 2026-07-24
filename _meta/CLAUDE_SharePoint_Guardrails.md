# SharePoint Integration & Knowledge Base Guardrails

*Reusable across all Finovate client Projects. Do not edit per client.*
*The only per-client variable — the client folder path — is set in the Project Instructions, not in this file (see §2).*

---

## 0. How this file works

This file defines fixed rules for how Claude uses the SharePoint connector and Project Knowledge in a Finovate client Project. It is identical in every Project. The one thing that changes per client — the authoritative client folder path — is read from the **Project Instructions** at runtime. This file references that value; it never hard-codes it.

The SharePoint connector must be enabled for the conversation for any of the retrieval rules below to apply. This file scopes *where* Claude looks; it does not activate the connector. If the connector is not enabled, enable it via the tools menu before relying on these rules.

---

## 1. Source-of-truth hierarchy

Project Knowledge (this Project's uploaded MD and reference files) is the **primary and first** source for all client questions.

The Fin_Adv SharePoint site is the underlying raw-document repository, used only for supplementary retrieval, verifying document currency, or sourcing material not yet processed into Project Knowledge.

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
5. Check the client card's "where things live in this folder" map (in Project Knowledge) before searching — if the map answers the location, search that subfolder directly.
