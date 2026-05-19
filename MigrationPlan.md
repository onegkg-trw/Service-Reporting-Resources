Here's the roadmap. I'll call out the schema mismatches explicitly since those drive most of the work.

---

## Phase 1 — Define your API contracts

Before touching any app code, nail down the request/response shapes for each endpoint. This is the most important step because the schema has several structural differences from the current mock data.

**Key mismatches to resolve:**

- **Companies are not in the report schema.** `service_report.company` is a string inside the report. Your `/api/company/` routes imply companies are first-class entities — decide if they live in their own collection or are just derived by grouping reports.
- **Technician (`trw_contact`) lives inside `our_reference`**, not at the top level. Your current mock has it flat on the report.
- **Time entries are different.** The mock has named entries (`Time 1`, `Travel 1`) with separate start/end strings. The schema has `service_dates.times` as an array of `{ arrival, departure }` ISO date-times, plus a separate `travel` duration field (ISO 8601 duration, e.g. `PT2H30M`) for total travel time — not individual travel segments.
- **Clock in/out is now per-entry, not per-report.** There's no single `clockIn`/`clockOut` on the report anymore — it's derived from the first arrival and last departure across all `times` entries.
- **`on_site` doesn't exist in the schema.** Decide if you're dropping it or adding it.
- **`description` maps to `service_dates.description`**, though the schema itself notes its purpose is unclear — worth clarifying with stakeholders.

Write explicit JSON examples for at least these endpoints before moving on: `GET /api/reports`, `GET /api/company/`, `GET /api/reports/{id}`, `GET /api/reports/{id}/time`.

---

## Phase 2 — Data & type layer

Once contracts are defined:

1. Write TypeScript interfaces that match the API responses exactly — separate from the current mock-data types in the component. Keep them in a `types/api.ts` file.
2. Write a **transform layer** (`lib/transforms.ts`) that maps API shapes → the UI's internal shape. This keeps the components insulated from backend changes. The trickiest transforms will be:
   - Deriving a display `clockIn`/`clockOut` from the `times` array
   - Converting ISO durations (`PT2H`) to human-readable strings for travel/break display
   - Flattening `our_reference.trw_contact` into the technician field
3. Set up a data-fetching layer — React Query or SWR both work well here. One hook per logical resource: `useCompanies`, `useReports(companyId)`, `useReport(id)`, `useTimeEntries(reportId)`.

---

## Phase 3 — Auth

The minimal addition is:

A /app/sign-in/page.tsx with a "Sign in with [Provider]" button
An auth library to handle the OAuth flow — NextAuth.js (Auth.js) is the standard choice for Next.js and has built-in OAuth provider support
A middleware file (middleware.ts) at the root that redirects unauthenticated users to /sign-in

That's roughly:

1 new page
1 middleware file
NextAuth config (/app/api/auth/[...nextauth]/route.ts)
Some environment variables for your OAuth client ID/secre

Since there's "some OAuth stuff," get this working before any authenticated endpoints. You'll need:
- Session/token storage
- An auth wrapper or middleware in Next.js
- Passing the token on every API request

Don't build Phase 4 without this, or you'll retrofit auth headers everywhere.

---

## Phase 4 — Read paths (GET routes)

Replace mock data screen by screen:

1. **Report list screen** — `GET /api/company/` then `GET /api/company/{company}/reports` per accordion. Note: the list endpoint probably shouldn't return full report objects, just enough for the list UI (name, id, company). Confirm this in your Phase 1 contracts.
2. **View Report tab** — `GET /api/reports/{id}` for the Info section, `GET /api/reports/{id}/time` for the time entries.
3. **Track Time tab** — pre-populate the form from `GET /api/reports/{id}`.

---

## Phase 5 — Write paths (POST/PATCH routes)

The current form's Submit button maps to multiple endpoints depending on what changed:

- **Time entries** → `POST /api/reports/{id}/time` to create, `PATCH /api/reports/{id}/time/{time_id}` to update, `PATCH /api/reports/{id}/time/{time_id}/complete` to clock out. The clock-out-only patch is a separate endpoint — your form UI may need to reflect this distinction (e.g. a "Clock Out" action vs a full edit).
- **Everything else on the form** → `PATCH /api/reports/{id}`.

You may want to split the current single Submit into more granular save actions rather than one bulk submit, since the backend separates time from non-time updates.

---

## Phase 6 — Missing UI for new schema fields

Several fields in the schema have no UI yet. Decide which ones belong in the app:

| Schema field | Current UI | Decision needed |
|---|---|---|
| `contact_person_one/two` | None | Add to View Report info section? |
| `instrument_configuration` | None | Read-only info panel? |
| `service_call_information` | None | Add to report form? |
| `our_reference.po_no` / `billing_company` | None | Likely read-only |
| `service_dates.break` | None | Add to Track Time form? |
| `hours.total_hours` / `total_travel` | None | Computed display in View Report? |
| `export` (Word doc) | None | Download button in View Report |

---

## Phase 7 — Error states & loading UI

Currently absent since everything is synchronous mock data. Each screen needs loading skeletons, empty states, and error handling before the app is production-ready.

---

**Suggested order of execution:** 1 → 2 → 3 → 4 → 5 → 6 → 7. Phases 1 and 2 are the highest leverage — getting the types and transforms right early prevents a lot of rework in the later phases.
