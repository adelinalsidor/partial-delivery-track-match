# Order Delivery Tracker

**▶ Live demo: [adelinalsidor.github.io/partial-delivery-track-match](https://adelinalsidor.github.io/partial-delivery-track-match/)** — two demo orders are waiting under *Active orders*: upload their sample invoices, or create an order from an Excel / CSV file of your own. English / Romanian, no sign-up, no server.

A single-page tool for auto parts procurement: reconciles a purchase order against the supplier invoices for each partial delivery, so you always know what's shipped and what's still owed — without doing the math by hand from every invoice.

Built for small procurement teams (auto parts resellers, for example) who don't have — or need — full ERP tooling, just a way to stop losing track of half-delivered orders.

## What it does

Create an order from an Excel or CSV file, then add each partial-delivery invoice with one click: choose the file and it is added to its order automatically. When creating an order, the page first tells you which columns are required (part code and quantity; description is optional). The tracker recognises the part-code, description and quantity columns by their headers, shows a preview to confirm, and matches delivered quantities against ordered quantities by part code, per line item. Each line shows a live status: not started, partial, complete, or over-delivered. Files are read in the browser — no server, no account. PDFs and photos are not read yet (planned, see the [roadmap](ROADMAP.md)).

## Why it's more than a spreadsheet

- **Matching, not manual reconciliation.** Deliveries are matched to order lines by part code automatically; codes that don't match any line are flagged separately instead of silently dropped, so a wrong code or a substitute part never gets lost.
- **Every partial invoice is tracked, and none is counted twice.** Each order lists the invoices uploaded so far (number, date, lines, pieces). If you upload an invoice whose number is already registered on that order, its rows are skipped with a warning instead of doubling the delivered quantities — matching ignores case and stray spaces. A mistaken invoice can be removed with confirmation. The invoice number is required (from a column in the file, or typed in), because it is what duplicates are detected by.
- **Duplicate part codes on one order get merged, not duplicated.** If a purchase order lists the same part code twice (e.g. split across a delivery schedule), the tracker sums the ordered quantity into one line rather than creating two — otherwise later delivery-matching would only ever find the first one, leaving the second permanently stuck at "not started."
- **No backend.** State lives in the page and autosaves in the browser, so there is no server or login to run.

## Time saved, safety and cost of running it

The point of the tool is to replace the slowest, most error-prone part of procurement follow-up — re-reading every invoice and ticking quantities off by hand — with automatic reading of the file plus automatic matching.

**Time (illustrative estimate, not a measurement).** Assumption: reconciling one ~30-line invoice against its purchase order by hand takes about 15 minutes. With the tracker it takes about 1 minute (upload the file, check the preview, confirm). At 100 documents a month that is roughly 25 hours down to under 2. Replace the 15-minute figure with your own timing — the result scales linearly.

**Safety — nothing is decided without a person.**
- Reading Excel / CSV files is plain code, not AI: the same file always gives the same result. If AI extraction for PDFs and photos is added later (see the roadmap), it would only *extract*; matching and statuses stay deterministic code.
- The detected columns and the extracted lines are shown in a preview, and the column choices can be corrected, before anything is created.
- Part codes that match no order line are flagged in a warning, never silently dropped or force-matched.
- Over-delivery is flagged explicitly rather than hidden in a total.
- Duplicate part codes on one order are merged, so a later delivery can't get stuck on a second copy of the line.
- Everything stays in the user's browser: files are read locally and never sent to a server. (The only external request is loading the open-source SheetJS library from its official CDN, pinned to a version and verified with an integrity hash, the first time an `.xlsx` file is used.)

**Cost to run.** Uploading Excel / CSV costs nothing: no AI, no server. **Automatic extraction for PDFs and photos is planned** (see [ROADMAP.md](ROADMAP.md)); that is the only part that would cost anything. Prices from the official Claude API pricing page, checked 2026-10-08. Claude Haiku 5.5 costs $0.10 per million input tokens and $0.50 per million output tokens (prompts up to 100,000 tokens); Sonnet 5.5 costs $2 / $10.

| Assumption per document | Haiku 5.5 | Sonnet 5.5 |
|---|---|---|
| ~10,000 input tokens (a few PDF pages + the prompt), ~1,500 output tokens | ≈ $0.002 | ≈ $0.035 |
| 100 documents a month | ≈ $0.20 | ≈ $3.50 |

The token counts are an assumption, not a measurement; real usage depends on page count and layout. Even at 10× the tokens, Haiku stays under about $2 per 100 documents. Against ~23 hours of manual work saved, the AI cost is negligible — the real cost is the review time, which is why the review step stays in the loop.

**Today's version** has no per-document cost: Excel / CSV are read in the browser and the tracker itself is free static HTML.

## Persistence

Runs as a plain static HTML file — no Claude Artifact required. Data autosaves to the browser's `localStorage` on every change, so reopening the page on the same computer restores your orders. The data stays on that computer and browser; nothing is sent to any server.

## Languages

The interface is available in English and Romanian. The **EN / RO** button in the top bar switches everything — labels, statuses, messages and the demo data. The first visit picks Romanian if the browser is set to Romanian, English otherwise, and the choice is remembered. Column headers in uploaded files are recognised in Romanian and English.

## Usage

1. Open the live demo (or `tracker-livrari-comenzi.html`) . The first time, two different demo orders appear under **Active orders** (different supplier, parts and quantities), each with its own partial invoices ready to upload: three for the first, two for the second. Upload them one by one to watch the statuses change (complete, partial, over-delivered, unknown code). Once an invoice is marked *uploaded* it cannot be added again. Demo orders can be deleted with **Delete order** and do not come back.

**With your own files:**

1. Press **Create new order**. A window lists the columns the file must contain — *part code* and *quantity* are required, *description* is optional — and the accepted formats (Excel `.xlsx` / `.xls` and CSV). Choose the file from your folder, give the order a name (it defaults to the file name), check the preview and press **Save order**. It appears under **Active orders**. If a required column is missing, the page says which one and lets you pick the right column.
2. When a partial delivery arrives, press **Extract data from a partial invoice** inside that order and choose the file: the invoice is added to that order automatically (Excel or CSV; PDFs and photos are not read yet). The invoice number is taken from a column in the file, or from the file name if there is none — it is what stops the same invoice being counted twice. A duplicate invoice is flagged and skipped; a mistaken one can be removed.

## What's next

See [ROADMAP.md](ROADMAP.md) for planned extensions (automatic document extraction, multi-user storage).
