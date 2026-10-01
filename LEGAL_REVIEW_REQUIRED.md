# Legal review required — internal checklist

This file tracks legal/compliance items on the public website (`app/src/screens/public/`)
that need input from the business owner and/or qualified Saudi legal counsel before the
site can be considered legally final. It is **internal** — do not surface any of this on
the public pages themselves (Privacy Policy, Terms & Conditions, Cookie Policy).

The public legal pages (`PrivacyPolicy.tsx`, `Terms.tsx`, `CookiePolicy.tsx`, all built on
`sections/LegalDocument.tsx`) were rewritten on 2026-09-16 to:
- drop the public "this is a template, not reviewed by counsel" banner (unacceptable on a
  production site — it undermines trust in every other claim on the page), and
- avoid claiming review has happened (it has not).

Instead, each page shows a plain **effective date** and **version number**, and every open
question below is tracked here instead.

## Open items — owner and/or counsel input required

1. **Operating legal entity.** The site currently refers to "SALIS AUTO" without a
   registered legal entity name, commercial registration (CR) number, or registered
   address. Needed: the exact legal entity name as registered in Saudi Arabia, its CR
   number, and registered address, to be added to Terms & Conditions and Privacy Policy.
2. **Data Protection Officer / privacy contact.** Privacy Policy references "our Contact
   page" for rights requests. Saudi PDPL implementing regulations may require a named DPO
   or privacy contact for entities above a certain size/processing volume — confirm
   whether one is required and, if so, name and contact details to publish.
3. **Hosting region and sub-processors.** The Privacy Policy's "International data
   transfers" clause is written generically because no confirmed hosting region or
   sub-processor list exists in this repository. `vercel.json` shows Vercel as the
   frontend build target (Netlify was retired; `netlify.toml` no longer exists), and
   the API and database have no production environment yet, so no data-residency
   region is confirmed for any tier.
   Needed: confirmed hosting region(s) for production data, and the list of
   sub-processors (payment gateways, SMS/WhatsApp provider, email provider, cloud
   storage) so the transfer clause can name them and describe the actual safeguards in
   place (e.g. standard contractual clauses, adequacy).
4. **Verified public contact details.** `Contact.tsx` uses `info@salisauto.sa` and
   `tel:+966112345678` as the site's contact channel. The `salisauto.sa` domain is not
   confirmed as a live, owned, monitored domain anywhere in this repository (see the SEO
   gap noted below) — confirm this email/phone are real, monitored channels before they
   are relied on for legal notices or PDPL rights requests, or replace them.
5. **VAT/ZATCA specifics for the platform's own commercial terms.** Terms & Conditions'
   "Fees and payment" clause states fees are VAT-exclusive "where required by law" as a
   general statement. If/when public pricing or a specific commercial agreement format is
   finalized, confirm the VAT registration number and invoicing details.
6. **Cookie/analytics vendor list.** Cookie Policy describes categories (essential,
   analytics, preference) without naming specific third-party vendors because none exist
   in this codebase yet (no analytics script is wired into the public site). Update this
   page's "Third-party cookies" clause the moment an analytics or other cookie-setting
   vendor is actually integrated — do not let the policy fall behind the real
   implementation.
7. **Formal legal sign-off.** None of the above has been reviewed by qualified counsel.
   Treat all legal-page copy as an operationally-honest draft aligned to PDPL structure,
   not as reviewed legal advice, until counsel confirms it.
8. **AI sub-processor and PDPL basis (batch 8).** The AI platform (docs/AI_GOVERNANCE.md)
   sends minimised workshop data to a model provider — by default Anthropic, PBC, United
   States (`AI_SUB_PROCESSOR`, `AI_SUB_PROCESSOR_REGION`) — only after an organization's
   administrator turns AI on and records a PDPL opt-in and a sub-processor
   acknowledgement in Model Settings. Every organization is **off** by default. Needed
   before any organization turns it on in production: (a) a signed data processing
   agreement with the provider that excludes training on customer data; (b) counsel's
   confirmation that an org-level opt-in is the right legal basis per data class, and
   whether the cross-border transfer needs further safeguards; (c) the provider named in
   the Privacy Policy's sub-processor clause (item 3).

## Sub-processor register — the list item 3 asks for

Compiled 2026-09-26 from the code, the CI and deployment workflows, the project-control
registers and the open Batch 1–3 pull request
([tazamohd/SalisAuto-Unified#50](https://github.com/tazamohd/SalisAuto-Unified/pull/50)).
Every row names the evidence it came from, so counsel can check it rather than trust it.
The status column says whether personal data reaches the party **today**, not whether
a relationship has been discussed.

Status vocabulary: **IN USE** — personal data reaches this party now; **HELD DATA** — held
personal data in the past, and may still hold it; **PLANNED** — named in product copy,
seed data or legal text, but no adapter exists and no data flows; **UNDECIDED** — a
category the platform will need, with no vendor chosen.

| # | Party | What it does for SALIS AUTO | Personal data involved | Evidence in this repository | Status | Region known? |
|---|---|---|---|---|---|---|
| SP-01 | **Vercel Inc.** | Hosts and serves the web app (`app/`) and the 2030 landing page (project `sl-cl-ne`, auto-deploys from `main`) | Visitor IP addresses, user agents and request logs; everything a signed-in user's browser sends passes through its edge | `vercel.json`, `app/vercel.json`, comment in `.github/workflows/deploy-hostinger.yml` | IN USE | No. Edge is global; no function region is pinned |
| SP-02 | **GitHub, Inc. (Microsoft)** | Source hosting, GitHub Actions CI, and GitHub Pages (`deploy-pages.yml` publishes `app/dist`) | Pages visitor logs. The repository itself held a production-shaped dump with 370 users' password hashes, phones and addresses (`audits/slsdb.sql`, DEF-001) | `.github/workflows/*.yml`; `audits/slsdb.sql` (still present on `main`; removed from the tip only in #50) | IN USE + HELD DATA | No (US-hosted by default) |
| SP-03 | **Hostinger** | Hosts the Salisco company site (`site/`) over FTP | Visitor IP addresses and server logs | `.github/workflows/deploy-hostinger.yml` | IN USE (the deploy currently fails on DNS; the host is still the target) | No |
| SP-04 | **Google LLC — Google Fonts** | Serves web fonts to every `site/` page | Each visitor's IP address and user agent are sent to Google on page load, before any consent | `<link href="https://fonts.googleapis.com/…">` in `site/*/index.html` | IN USE | No (global) |
| SP-05 | **Mailbox provider for `salisco.sa`** | Receives demo requests. The form falls back to a `mailto:` draft to `hello@salisco.sa`, because `LEAD_API_BASE` is empty | Name, company, phone and email typed into the demo form | `site/assets/demo-request.js` (`FALLBACK_EMAIL`), `site/request-demo/index.html` | IN USE — the provider is not identified anywhere | No |
| SP-06 | **Neon (Neon Inc.)** | Postgres host of the legacy SALIS-GMS database whose dump is DEF-001 | 370 user rows: password hashes, phones, addresses, vehicles | `ROT-002` and `ROT-004` in `project-control/SECRET_ROTATION_LOG.json` on #50 (`neondb_owner`) | HELD DATA — still live unless decommissioned | No |
| SP-07 | **Replit, Inc.** | Ran the legacy SALIS-GMS "Garage Management System" | Whatever that deployment's users entered | `ROT-001` on #50; `audits/replit.md`, `audits/replit.nix` | HELD DATA — decommission not recorded | No |
| SP-08 | **Unifonic** | Named in product copy as "the approved SMS/WhatsApp relationship" for reminders and one-time codes | Phone numbers and message content, once connected | `app/src/data/ar-overrides.ts`; seed row "Unifonic SMS" in `app/src/data/generated/tables.ts`. `OTP_TRANSPORT` and `MESSAGING_TRANSPORT` accept only `unconfigured`/`log`/`memory`/`mock` (`server/src/auth/config.ts`, `server/src/integrations/config.ts`) | PLANNED — nothing connected | Not applicable yet |
| SP-09 | **HyperPay (Mada acquiring)** | Named in product copy and seed data as the card/Mada gateway | Transaction amounts and payment tokens, once connected | Seed row "Mada / HyperPay" in `server/src/db/seed-data.ts`; `ar-overrides.ts` | PLANNED — no gateway adapter | Not applicable yet |
| SP-10 | **Stripe, Inc.** | Listed as the payment sub-processor in the DPA | As SP-09 | `docs/legal/data-processing-agreement.md` §4.1 | PLANNED on paper only. **This contradicts SP-09**: the DPA says Stripe, the product says HyperPay | — |
| SP-11 | **Production API and database host** | Will host the Fastify API and PostgreSQL with all tenant data | All tenant data | No production environment exists (item 3 above). DPA §4.1 nonetheless states "Kingdom of Saudi Arabia" | UNDECIDED. **The DPA's location claim is unsupported** | No |
| SP-12 | **Transactional email provider** | Password reset, notifications, invoices by email | Email addresses and message content | DPA §4.1 lists "Email service provider"; no email adapter exists in `server/src` | UNDECIDED | — |
| SP-13 | **Object storage provider** | Photos, documents and attachments | Vehicle photos, documents that may show people or plates | `server/src/storage/media.ts`: "No object-storage adapter exists anywhere in this codebase yet"; `docs/FILES_STORAGE.md` | UNDECIDED | — |
| SP-14 | **AI model provider** | Any future model call from the AI features | Whatever a prompt carries. `docs/AI_GOVERNANCE.md` §3.2 makes sending personal data a residency decision | `docs/AI_GOVERNANCE.md`: no model provider client exists in `server/` | UNDECIDED — no calls today | — |

Reviewed and **not** sub-processors, so they need no transfer clause, though counsel may
want them named elsewhere:

- **ZATCA (Fatoora).** Invoice data goes to the tax authority under a legal obligation, as
  a recipient in its own right, not on SALIS AUTO's instructions.
- **Bosch, Denso, Continental, Shell Lubricants.** Named as partner relationships in product
  copy (`ar-overrides.ts`). No customer data flows to them in code. If a parts-ordering
  integration ever sends customer or vehicle data, they become recipients and belong in
  the table above.
- **Microsoft Edge TTS.** Generates narration for the marketing videos
  (`audits/video-series/renders/ep00-generator/voiceover.py`). It processes scripted
  narration text only, with no personal data.
- **The OBD bridge.** It is on-premises at the workshop by design (`OBD_BRIDGE_URL`), and
  no live adapter exists.

What the owner and counsel need to do with this register:

1. **Confirm or remove each IN USE and HELD DATA row.** For each one kept, supply the
   contracting entity, the processing region and the transfer safeguard (SDAIA standard
   contractual clauses, or an adequacy basis).
2. **Pick the vendors for SP-11 to SP-14** before any production data exists, and choose a
   KSA region wherever the DPA is meant to keep saying "Kingdom of Saudi Arabia".
3. **Fix the two DPA contradictions** before the DPA is sent to anyone: Stripe versus
   HyperPay (SP-09/SP-10), and the KSA hosting claim with no host (SP-11).
4. **Decide on Google Fonts (SP-04).** Either self-host the fonts, which removes the
   sub-processor and the pre-consent transfer, or name Google in the Privacy Policy and
   the Cookie Policy.
5. **Remove the internal filename from the public Privacy Policy.** Its "International
   data transfers" clause tells visitors to "see LEGAL_REVIEW_REQUIRED.md"
   (`app/src/screens/public/PrivacyPolicy.tsx`). That breaks this file's own rule that
   nothing here is surfaced publicly. It is an app-code change, tracked as an owner action.

Each open row is also an entry in
[`docs/03_PRINCE2_GOVERNANCE/OWNER_ACTIONS.md`](docs/03_PRINCE2_GOVERNANCE/OWNER_ACTIONS.md).

## Related: promotions

`DealsOffers.tsx` (`/public-portal/deals-offers`) previously listed six specific
promotions (percentage discounts, a flat referral credit, free inspections) with no
verified start/expiry date, eligibility, participating branches, or terms link behind
them. Per the "no fabricated commercial obligation" rule, these were removed and replaced
with a general description of the *categories* of program that may exist, routing
visitors to Contact/Request Demo. **Before republishing any specific offer**, it needs:
start date, expiry date, eligibility, participating branches, redemption rules,
exclusions, combination rules, and a link to full terms — then it can be restored as a
structured `Offer` with all of those fields, not just a headline and a badge.

## Related: origin domain

`app/public/sitemap.xml` and `robots.txt` reference `https://salisauto.sa` as the
canonical origin. That domain's ownership/DNS is not confirmed anywhere in this
repository. Confirm the production domain before launch — canonical URLs, Open Graph
URLs, and the sitemap all assume it.
