# Contact Your Representatives — Design

**Date:** 2026-06-30
**Branch:** Contact-your-representatives-form
**Status:** Approved

## Purpose

Let site visitors email the regulators who can stop the sale of Israel war
bonds through Luxembourg, pressuring the Luxembourg government and the CSSF to
end it. The visitor sends from their own email address — a real constituent
carries more weight with a regulator than an anonymous form blast.

## Constraint

The site is a single static `index.html` on GitHub Pages. No backend, no build
step. Delivery therefore uses `mailto:`, reusing the existing `sendMail()`
pattern already in the contact section — no dependency added.

## Placement

New section `#represent`, inserted **before** the existing `#contact` section.
The existing "Write to your MP" button in the Action section (`#action`) links
to it (`href="#represent"`). The existing "Connect with us" contact form stays
untouched.

## Recipients

Built into every generated `mailto:`:

| Body | Field | When |
|------|-------|------|
| CSSF | `to:` | always |
| Luxembourg Ministry of Finance | `cc:` | always |
| ESMA (EU securities regulator) | `cc:` | always |
| Visitor's national competent authority (NCA) | `cc:` | only when an EU country is selected |

The NCA is the CSSF-equivalent in the visitor's country — the body with legal
power over prospectus passporting (e.g. Germany = BaFin, France = AMF,
Ireland = Central Bank of Ireland). Non-EU selection omits the NCA line.

## Inputs

- **Country** — dropdown: EU 27 + "Other / non-EU". Default is an unselected
  prompt. Drives which NCA is added.
- **Name** — text, inserted into the letter signature.

No email field — `mailto` sends from the visitor's own client.

## Data

One JS object at the top of the section's `<script>`, clearly commented, so the
client owns the addresses in one place:

```js
// Fixed recipients
const CSSF = "...";
const MIN_FINANCE = "...";   // cc
const ESMA = "...";          // cc
// EU national competent authorities — fill in addresses
const NCA = { DE: "", FR: "", IE: "", /* ...EU27... */ };
```

Addresses are supplied/verified by the client before merge. The repo ships with
`""` placeholders for the NCAs and the three fixed addresses.

## Letter

One English template, prefilled and fully editable in a `<textarea>`. Subject
prefilled too. Draft drawn from the campaign's existing asks (suspend/refuse the
prospectus under Art. 23 Prospectus Regulation, ICJ duties of non-assistance and
prevention, coordinate with Ireland and notify ESMA). Client can rewrite before
merge.

## Behaviour

"Send" reads the country + name, builds
`mailto:<CSSF>?cc=<...>&subject=<...>&body=<...>` and sets
`window.location.href` — identical to the existing `sendMail()`. If the country
is left on the default prompt, show an inline nudge reusing the existing
`form-note` red-text pattern; do not build the link.

## Styling

Reuses existing `.section-*`, `.form-group`, `.form-label`, `.form-input`,
`.form-textarea`, `.form-submit`, `.form-note` classes. A `<select>` is styled
to match `.form-input`. No new CSS system.

## Out of scope (YAGNI)

- Per-country letter translations — single English template; add later if needed.
- Email validation — no email is collected.
- Backend / one-click send — `mailto` only.

## Check

A `mailto` string is built correctly: EU selection includes the NCA in `cc`,
non-EU omits it, subject and body are URL-encoded, name appears in the
signature. Verified by opening the page and inspecting the generated link for
one EU and one non-EU selection.
