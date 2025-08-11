# Archer Attachment Count

## Overview
Small, self-contained HTML/JS snippet that **counts the number of attachments on an RSA Archer record** (by inspecting the attachment grid rows in the DOM) and **writes that count back to Archer via the Content API** (to a numeric field such as `Quantidade`).  
It runs on page load, extracts the current record ID from the page, reads the CSRF token from the session, counts attachments, and performs a POST to your application’s Content API.

## Features
- **Auto-run on page load** using `Sys.Application.add_load(...)`.
- **Attachment counting** by matching `<tr>` IDs in the attachment grid (can support multiple attachment fields via regex).
- **Record ID extraction** from the record layout (configurable selector).
- **Content API update** with CSRF header, posting `{ Id, Count }` to your Archer app.
- **Console logging** for quick diagnostics (extracted values, count, success/error).

## Prerequisites
- An RSA Archer environment with **Content API enabled**.
- A **numeric field** in your target application to store the count (e.g., `Quantidade`).
- An authenticated **user/session** with permission to update records in the target app.
- **jQuery available** on the record page (the snippet uses `$.ajax`).
- Your record layout must expose:
  - The **attachment grid**, whose row IDs follow your instance’s pattern.
  - An element containing the **current record ID** (selector is configurable).

## Installation

> You can run this as a quick test from the browser console or embed it permanently in the record layout.

### Option A — Quick test (DevTools Console)
1. Open a target record in Archer.
2. Open **Developer Tools → Console**.
3. Paste the contents of `AttachmentCountAPI.html` and press **Enter**.  
   The script will initialize on load and send a POST to the Content API.  
   Check the console for logs.

### Option B — Embed in the record layout
1. Edit the record layout where the attachment field lives.
2. Add a **custom HTML/JS** component (e.g., a custom object / HTML block).
3. Paste the script from `AttachmentCountAPI.html`.
4. Publish and test. The code runs automatically on page load.

## Configuration

Update the following parts of the script to match your environment:

- **Content API endpoint & app alias**

Replace <YourAppAlias> with your application’s alias/path (e.g., Control_Self_Assessments_1).

- Payload keys (record id & numeric field)

const payload = {
  "<YourAppAlias>_Id": processedID,
  "Quantidade": anexosCount
};

Map <YourAppAlias>_Id to your app’s ID field name and Quantidade to your numeric field.

- Record ID selector

// Example (replace with your layout’s element)
const idEl = document.getElementById('master_DefaultContent_rts_s1400_f3102c');

Change the selector to wherever your layout exposes the record ID.

- Attachment grid patterns
The script detects which attachment field exists and counts matching <tr> IDs using regex.
Update the regex patterns to match your grid’s generated IDs.

- CSRF & base URL
The script reads x-csrf-token from sessionStorage and composes baseURL from the current Archer context.
Adjust only if your environment stores tokens/URLs differently.

# How it Works
1. Initialize on load → countAttachments() identifies the correct attachment grid and counts its rows.
2. Extract record ID from the page (searchIDInPage).
3. Build request: derive baseURL, read CSRF token, and send a POST to the app’s Content API with { Id, Quantidade }.
4. Log outcome to the console (success/error).

# Notes & Limitations
- Selectors/regex are environment-specific: update them to match your Archer layout and grid ID patterns.
- Runs in the context of a logged-in session; it does not handle authentication.
- Ensure your CSP / page settings allow inline scripts where you embed this code.
- Test in a non-production environment before deploying broadly.


  ```js
  const url = baseURL + '/contentapi/<YourAppAlias>';
