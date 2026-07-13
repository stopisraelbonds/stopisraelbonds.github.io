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
addresses live in one place. All addresses below were researched from each
body's own official website (2026-06-30); **the client should re-verify before
go-live.**

Fixed recipients:

| Body | Email | Field |
|------|-------|-------|
| CSSF | `direction@cssf.lu` | to |
| Luxembourg Ministry of Finance | `Ministere-Finances@fi.etat.lu` | cc |
| ESMA | `info@esma.europa.eu` | cc |

National competent authorities (added as `cc` when that EU country is selected).
Prospectus-specific inboxes preferred where published:

| ISO | NCA | Email |
|-----|-----|-------|
| AT | FMA | `fma@fma.gv.at` |
| BE | FSMA | `info.fin@fsma.be` |
| BG | FSC | `bg_fsc@fsc.bg` |
| HR | HANFA | `capital.markets@hanfa.hr` |
| CY | CySEC | `info@cysec.gov.cy` |
| CZ | ČNB | `podatelna@cnb.cz` |
| DK | Finanstilsynet | `finanstilsynet@ftnet.dk` |
| EE | Finantsinspektsioon | `info@fi.ee` |
| FI | FIN-FSA | `kirjaamo@finanssivalvonta.fi` |
| DE | BaFin | `poststelle@bafin.de` |
| GR | HCMC | `info@cmc.gov.gr` |
| HU | MNB | `ugyfelszolgalat@mnb.hu` |
| IE | Central Bank of Ireland | `prospectus@centralbank.ie` |
| IT | CONSOB | `protocollo@consob.it` |
| LV | Latvijas Banka | `info@bank.lv` |
| LT | Lietuvos bankas | `info@lb.lt` |
| MT | MFSA | `communications@mfsa.mt` |
| NL | AFM | `info@afm.nl` |
| PL | KNF | `knf@knf.gov.pl` |
| PT | CMVM | `cmvm@cmvm.pt` |
| RO | ASF | `office@asfromania.ro` |
| SK | NBS | `info@nbs.sk` |
| SI | ATVP | `info@atvp.si` |
| SE | Finansinspektionen | `finansinspektionen@fi.se` |

**No NCA email (send proceeds without one):**
- **FR** (AMF) and **ES** (CNMV) — publish no public email, web-form only. NCA
  is `""`; the visitor still emails CSSF + Ministry + ESMA.
- **LU** — the national regulator is the CSSF, already the primary recipient;
  mapped to `""` to avoid a duplicate.

Implementation: `NCA[isoCode]` returns `""` for FR/ES/LU and for non-EU, so the
build logic simply skips an empty NCA. The dropdown still lists all EU 27 plus
"Other / non-EU".

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
