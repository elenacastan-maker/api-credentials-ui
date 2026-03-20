# API Credentials UI — Kleinanzeigen PRO

Interactive HTML prototype for the API Credentials management feature in the Kleinanzeigen PRO portal. Built as a single self-contained file for design review and usability testing.

## How to use

Open `api-credentials-ui.html` directly in a browser. Use the black navigation bar at the top to jump between screens.

---

## Screens & flows

### Happy path

| # | Screen | Trigger |
|---|--------|---------|
| ① | **Empty state** | User lands on API Credentials with no key yet |
| ② | **Confirm generate** | User clicks "Generate New API Key" — modal with checkbox gate |
| ③ | **One-time secret reveal** | Key generated successfully — Client ID + Secret shown once |
| ④ | **Active credentials** | User dismisses reveal — key details, rotate and revoke actions |
| ⑤ | **Rotate confirm** | User clicks "Rotate key" — modal with checkbox gate |
| ⑥ | **Revoke confirm** | User clicks "Revoke key" — modal with text input + checkbox gate |
| ⑦ | **Post-revoke empty state** | Key revoked successfully — success banner + option to generate new |

### Error flows

| # | Screen | When it occurs |
|---|--------|----------------|
| ⑧ | **Generate failed** | Server error (500) during key generation. No key was created — existing credentials unchanged. CTA: Try Again. |
| ⑨ | **Rotate failed** | Server error (500) during rotation. Current key is still valid — integrations unaffected. CTA: Try Again or go back to credentials. |
| ⑩ | **Revoke failed** | Server error (500) during revocation. Key remains active — integrations continue to work. CTA: Try Again or go back to credentials. |
| ⑪ | **Permission denied** | User with Viewer role navigates to API Credentials. Generate button is disabled. Error 403 shown. User is directed to contact their account admin. |
| ⑫ | **Key revoked externally** | Another admin revoked the key while the user had the active credentials view open. Banner shows who revoked it and when. Empty state with CTA to generate a new key. |

---

## Use case map

```
[No key]
    └── Generate New API Key
            ├── Success → One-time reveal → Active credentials
            └── Error   → Generate failed (⑧) → Try Again → Confirm generate (②)

[Active key]
    ├── Rotate key
    │       ├── Success → One-time reveal → Active credentials
    │       └── Error   → Rotate failed (⑨) → Try Again → Rotate confirm (⑤)
    │
    ├── Revoke key
    │       ├── Success → Post-revoke empty state (⑦)
    │       └── Error   → Revoke failed (⑩) → Try Again → Revoke confirm (⑥)
    │
    └── Key revoked by another admin → Key revoked externally (⑫)

[Viewer role]
    └── Navigate to API Credentials → Permission denied (⑪)
```

---

## Design decisions

- **One-time secret**: the Client Secret is shown only in the reveal modal (③). Once dismissed it cannot be retrieved — enforced by UX and copy.
- **Confirmation gates**: destructive actions (generate, rotate, revoke) require a checkbox acknowledgement. Revoke additionally requires typing `REVOKE` to prevent accidental clicks.
- **Error messaging**: all error screens clearly state whether the key is still active or unchanged, so users know if their integrations are at risk.
- **Permission denied**: the page renders with all controls disabled rather than hiding the section, so Viewer-role users understand the feature exists and know who to ask.
- **External revocation**: shown as an informational banner on the credentials page rather than a blocking modal, to avoid confusion with an action the user initiated.

---

## File structure

```
api-credentials-ui.html   — single-file prototype (HTML + CSS + JS, no dependencies)
README.md                 — this file
```
