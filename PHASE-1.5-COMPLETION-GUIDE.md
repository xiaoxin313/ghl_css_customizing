# BrokerEvolve Phase 1.5 — Completion Guide

This guide aligns with the Phase 1.5 trial scope, Milestone 1–2 deliverables, and Mike’s requirements. Use it to complete the project and validate propagation.

---

## 1. Contract & Scope Summary

| Item | Scope |
|------|--------|
| **Milestone 1** | Discovery & Architecture Blueprint — DOM map, safe overrides, written architecture plan. **Deliverable:** Architecture blueprint document. **Price:** $400 |
| **Milestone 2** | Token system + Master CSS — token library, centralized CSS, snapshot-safe, propagation-validated. **Deliverable:** Working token system + master CSS in test sub-account. **Price:** $700 |
| **Constraints** | No CRM custom fields, no provisioning logic, no pipelines/automations/telecom/SaaS config. Frontend styling layer only. |
| **Environment** | Work in dedicated test sub-account (cloned from Master Template). Master Template itself is protected until propagation is confirmed. |

---

## 2. What’s Already Done (Current Repo)

- **master.css** — Single entry point, 6-tier import order (config → reset → layout → components → themes → utilities). Ready for GHL Custom Code or CDN.
- **0-config/tokens.css** — Full token set: primitives, semantic colors, typography, spacing, layout (sidebar/header/content), z-index, radius, shadows, transitions, component tokens (sidebar, header, cards, buttons, inputs, tables, modals, widgets), agency override placeholders.
- **0-config/breakpoints.css** — Placeholder only.
- **1-reset/normalize.css** — Present (152 lines).
- **1-reset/ghl-overrides.css** — Substantial (528 lines): sidebar, header, dashboard, cards/widgets, nav, buttons, forms; uses tokens and targets real GHL selectors.
- **2-layout/** — Files exist but are placeholders (sidebar, header, content, dashboard-grid).
- **3-components/** — Placeholders (buttons, forms, cards, tables, modals, navigation).
- **4-themes/** — Placeholders (default, agency-override, seasonal).
- **5-utilities/** — Placeholders (spacing, typography, visibility).

So: **token system and master CSS structure are in place;** reset + GHL overrides do the heavy lifting. Layout/component/theme/utility files are optional enhancement layers.

---

## 3. What You Must Do to Complete Perfectly

### 3.1 Milestone 1 — Architecture Blueprint (if not yet delivered)

Produce a short **Architecture Blueprint** document (PDF or shared doc) that includes:

1. **DOM map summary** — Which GHL areas were audited (sidebar, header, main content, widgets, modals, etc.) and how they’re targeted (e.g. `#sidebar-v2`, `.sidebar`, `.agency-logo-container`, etc.). You can derive this from `ghl-overrides.css` and any notes from the test sub-account.
2. **Safe override zones** — What is safe to style (layout shell, nav, headers, cards, buttons, forms) vs what must not be changed (CRM data areas, provisioning UI, core GHL forms if they affect logic).
3. **CSS isolation approach** — Token-only styling, no hardcoded values; overrides scoped to known selectors; minimal `!important` only where GHL specificity forces it; no edits to GHL core markup or scripts.
4. **Snapshot-safety** — Single CSS entry point (master.css) loaded from one place (Custom Code or CDN); no per-sub-account custom code; all sub-accounts receive the same CSS when cloned from the template.
5. **Implementation strategy** — Order of layers (config → reset → layout → components → themes → utilities), where tokens live, where agency/seasonal overrides will hook in later.

**Action:** Create this document and deliver it as the Milestone 1 deliverable if you haven’t already.

---

### 3.2 Milestone 2 — Token System & Master CSS (finish and validate)

**A. Ensure one canonical entry point**

- GHL will load one URL (e.g. Custom Code snippet or `https://cdn.brokerevolve.com/css/master.css`).
- **If you use separate files:** All `@import url('...')` in `master.css` are relative. So either:
  - Deploy the whole folder structure to the same path (e.g. `https://cdn.brokerevolve.com/css/` so `0-config/tokens.css` resolves), or
  - Add a build step that concatenates all imported files into a single `master.css` and deploy that single file.
- **Recommendation:** Document in the blueprint: “Production: serve single concatenated master.css from CDN” or “Serve all files under /css/ with same structure.”

**B. Fill breakpoints (optional but recommended)**

In `0-config/breakpoints.css`, define media-query tokens so layout/visibility can stay token-driven:

```css
/** BrokerEvolve Breakpoints — use in max-width/min-width */
:root {
  --bp-sm: 640px;
  --bp-md: 768px;
  --bp-lg: 1024px;
  --bp-xl: 1280px;
  --bp-2xl: 1536px;
}
```

**C. Theme placeholders (variable swaps only)**

- **4-themes/default.css** — Can be empty or just a comment; `:root` in `tokens.css` is already the default.
- **4-themes/agency-override.css** — Comment that this file (or a dynamically injected block) will override `--agency-primary-color`, `--agency-logo-url`, etc., when agency branding is active.
- **4-themes/seasonal.css** — Comment (and optionally a small example) for seasonal overrides (e.g. holiday colors) so future you or Mike know where to add variable overrides.

No layout or structural changes in themes — only variable overrides.

**D. Propagation and deployment in test sub-account**

1. Get test sub-account access from Zeke/Mike.
2. Deploy the CSS:
   - Either paste the full concatenated `master.css` into GHL Custom Code (if under size limit), or
   - Host the file(s) on CDN and add one `<link rel="stylesheet" href="...">` in the approved place (Custom Code / Master Template–approved area).
3. Confirm:
   - Sidebar, header, and main areas use BrokerEvolve tokens (navy, spacing, typography).
   - No console errors, no broken layout.
   - Create a second sub-account from the same template (or snapshot) and confirm the same CSS applies without manual steps.

**E. Documentation**

Add a short **README or “CSS structure and propagation”** section (in the blueprint or separate):

- How to deploy: single file vs multiple files, and exact URL/path.
- How propagation works: “One stylesheet (or one import tree) in the template → all sub-accounts get it on clone/snapshot.”
- Where to change branding: tokens in `0-config/tokens.css`; agency overrides in `4-themes/agency-override.css` or via JS theme controller.
- What not to do: don’t edit GHL core, don’t add per-sub-account custom CSS for design tokens.

---

## 4. Phase 1.5 Trial Success Criteria (Mike’s List)

| Criterion | How to confirm |
|-----------|----------------|
| Global design token system (CSS variables) | ✅ Implemented in `0-config/tokens.css`. |
| Centralized Master CSS, snapshot-safe | ✅ Single entry `master.css`; document deployment so one source propagates to all sub-accounts. |
| BrokerEvolve branding on sidebar, header, dashboard, layout | ✅ Handled in `1-reset/ghl-overrides.css` with tokens. Verify in test sub-account. |
| No interference with provisioning, hierarchy, permissions, CRM | ✅ No changes to GHL config or CRM; CSS only. Confirm with Mike/Zeke that behavior is unchanged. |
| Styling propagates correctly | Create/clone a sub-account and confirm same look without extra steps. |
| Documentation of CSS structure and propagation | Write Architecture Blueprint (M1) + deployment/propagation notes (M2). |

---

## 5. Optional (Post–Phase 1.5) — Don’t block sign-off

- **2-layout/** — Add layout utilities or wrapper classes if you want a clearer “shell” layer; not required if overrides already achieve the look.
- **3-components/** — Flesh out when building dashboards (Command Center, ACA, Medicare, Agency, Training, Phone System) in the next phase.
- **5-utilities/** — Add when you need spacing/typography/visibility helpers; placeholders are fine for trial.
- **Plan-based visibility** — Not in scope for Phase 1.5; keep for when feature-flag / plan-tier logic is implemented.

---

## 6. Pre–go-live Checklist

- [ ] Architecture Blueprint document created and delivered (Milestone 1).
- [ ] Token library and Master CSS implemented and deployed in test sub-account (Milestone 2).
- [ ] Breakpoints (and optionally theme placeholders) filled or explicitly left as “future.”
- [ ] Propagation tested: two sub-accounts from same template show same styling.
- [ ] No custom fields, provisioning, pipelines, automations, telecom, or SaaS config touched.
- [ ] Documentation of CSS structure and propagation method shared with Mike/Zeke.
- [ ] Contract/milestones and DocuSign (Terms of Service, Independent Contractor) signed so access and go-live are unblocked.

---

## 7. One-Paragraph “What to do next”

**Complete Milestone 1** by writing the Architecture Blueprint (DOM map, safe zones, isolation, snapshot-safety, implementation strategy). **Complete Milestone 2** by finalizing the token system and master CSS (add breakpoints and theme comments if useful), deploying to the BrokerEvolve test sub-account, and validating that styling propagates to a second sub-account without manual fixes. **Document** deployment (single file vs multi-file, exact URL) and propagation method, and **confirm** with Mike that Phase 1.5 trial criteria are met before applying anything to the Master Template. After that, you’re ready for the next phase: dashboard UI styling and component implementation.
