# Order Delivery Tracker

A single-page tool for auto parts procurement: reconciles a purchase order against the supplier invoices for each partial delivery, so you always know what's shipped and what's still owed — without doing the math by hand from every invoice.

Built for small procurement teams (auto parts resellers, for example) who don't have — or need — full ERP tooling, just a way to stop losing track of half-delivered orders.

## What it does

Paste a purchase order and its invoices — extracted as CSV by Claude from a PDF, spreadsheet, or photo, using the built-in prompt — and the tracker matches delivered quantities against ordered quantities by part code, per line item. Each line shows a live status: not started, partial, complete, or over-delivered.

## Why it's more than a spreadsheet

- **Matching, not manual reconciliation.** Deliveries are matched to order lines by part code automatically; codes that don't match any line are flagged separately instead of silently dropped, so a wrong code or a substitute part never gets lost.
- **Every partial invoice is tracked, and none is counted twice.** Each order lists the invoices uploaded so far (number, date, lines, pieces). If you paste an invoice whose number is already registered on that order, its rows are skipped with a warning instead of doubling the delivered quantities — matching ignores case and stray spaces. A mistaken invoice can be removed with confirmation. Rows with no invoice number can't be checked for duplicates, so the tracker says so; the extraction prompt asks Claude to fill the invoice number on every row.
- **Duplicate part codes on one order get merged, not duplicated.** If a purchase order lists the same part code twice (e.g. split across a delivery schedule), the tracker sums the ordered quantity into one line rather than creating two — otherwise later delivery-matching would only ever find the first one, leaving the second permanently stuck at "not started."
- **No backend.** State lives in the page; "Save progress" / "Load progress" round-trip it as a JSON file, so a procurement person can keep a running file per project without any server or login.

## Time saved, safety and cost of running it

The point of the tool is to replace the slowest, most error-prone part of procurement follow-up — re-reading every invoice and ticking quantities off by hand — with AI extraction plus automatic matching.

**Time (illustrative estimate, not a measurement).** Assumption: reconciling one ~30-line invoice against its purchase order by hand takes about 15 minutes. With AI extraction plus the tracker it takes about 1 minute (upload, glance at the CSV, paste). At 100 documents a month that is roughly 25 hours down to under 2. Replace the 15-minute figure with your own timing — the result scales linearly.

**Safety — the AI never decides alone.**
- The AI only *extracts*; the matching and status logic is deterministic code, so the same input always gives the same result.
- The extracted CSV is shown and editable before anything is created, so a person confirms what goes in.
- Part codes that match no order line are flagged in a warning, never silently dropped or force-matched.
- Over-delivery is flagged explicitly rather than hidden in a total.
- Duplicate part codes on one order are merged, so a later delivery can't get stuck on a second copy of the line.
- Everything stays in the user's browser; nothing is sent to a server in the current version.

**Cost to run (automatic extraction — planned, see [ROADMAP.md](ROADMAP.md)).** Prices from the official Claude API pricing page, checked 2026-10-08. Claude Haiku 5.5 costs $0.10 per million input tokens and $0.50 per million output tokens (prompts up to 100,000 tokens); Sonnet 5.5 costs $2 / $10.

| Assumption per document | Haiku 5.5 | Sonnet 5.5 |
|---|---|---|
| ~10,000 input tokens (a few PDF pages + the prompt), ~1,500 output tokens | ≈ $0.002 | ≈ $0.035 |
| 100 documents a month | ≈ $0.20 | ≈ $3.50 |

The token counts are an assumption, not a measurement; real usage depends on page count and layout. Even at 10× the tokens, Haiku stays under about $2 per 100 documents. Against ~23 hours of manual work saved, the AI cost is negligible — the real cost is the review time, which is why the review step stays in the loop.

**Today's version** has no per-document cost: extraction is done by the user in a normal Claude conversation, and the tracker itself is free static HTML.

## Persistence

Runs as a plain static HTML file — no Claude Artifact required. Data autosaves to the browser's `localStorage` on every change, so reopening the page on the same computer restores your orders. **Salvează progres** / **Încarcă progres** export and import a JSON file, for backup or to move your data to another computer. Nothing is sent to any server.

## Languages

The interface is available in English and Romanian. The **EN / RO** button in the top bar switches everything — labels, statuses, messages, the demo data and the extraction prompt. The first visit picks Romanian if the browser is set to Romanian, English otherwise, and the choice is remembered. CSV column names (`cod_piesa`, `denumire`, …) stay the same in both languages because they are the format the app reads; the English prompt tells Claude to use them exactly.

## Usage

1. Open `tracker-livrari-comenzi.html` in a browser, or click **Încearcă cu un exemplu** to see a sample order with partial, complete and over-delivered lines.
2. Copy the extraction prompt from Step 1, paste it into a Claude conversation along with your PO or invoice file.
3. Paste the resulting CSV into **Comandă nouă** to create an order, or into an existing order's **Adaugă livrare** to log a delivery.

## What's next

See [ROADMAP.md](ROADMAP.md) for planned extensions (automatic document extraction, multi-user storage).
