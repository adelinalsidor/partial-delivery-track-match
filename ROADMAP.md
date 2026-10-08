# Roadmap

Current state (**Stage 1**): standalone static page, no backend, no AI, no cost. The user creates an order from an Excel or CSV file and extracts the data of each partial invoice from a file the same way; the page lists the required columns up front when creating an order, recognises them by their headers and previews it; invoices are added to their order automatically as soon as the file is chosen. Everything is read in the browser. Data lives in the browser (`localStorage`). Two demo orders, with partial invoices ready to upload, demonstrate the full flow with no setup and no cost. PDFs and photos are not read yet.

## Stage 2 — Automatic extraction from PDFs and photos (planned)

Excel / CSV already upload directly. Stage 2 closes the remaining gap, the documents that are not spreadsheets: the user drops a PDF or a photo, and the tracker shows the same required-fields check and preview.

- **Backend:** one serverless function (e.g. Vercel, `api/extract.js`) that calls the Claude API (`messages.create`, PDF/image content blocks) with an extraction prompt that returns the same columns. The API key lives in a server-side environment variable, never in the page.
- **Model:** Haiku 5.5 — cheap and sufficient for table extraction. At the official price checked 2026-10-08 ($0.10 / $0.50 per million input / output tokens) a typical document is roughly $0.002; see the README for the assumptions. Note Haiku 5.5 pricing rises for prompts over 100,000 tokens.
- **Cost protection (required before making it public):**
  - Per-IP and global daily request limits.
  - Upload size cap (serverless request bodies are limited to a few MB — verify current limit before building).
  - Monthly spend cap set in the Anthropic console.
  - Keep the demo orders and the Excel / CSV upload as the zero-cost paths for visitors; only PDF / photo extraction would cost anything.
- **Privacy:** documents would leave the user's browser and reach the Claude API. Tell users explicitly before enabling it for real client data.

## Stage 3 — Shared, persistent data (planned)

For teams that need more than one computer or one person per order.

- Accounts and storage (e.g. Supabase: auth + Postgres), orders and deliveries as tables instead of a local JSON blob.
- Multiple users on the same order, full delivery history, per-supplier views.
- Security review (row-level security, input validation) before real data goes in.
- Only worth building once there is a real user asking for it.

## Smaller ideas

- Backup / move data between computers: export and import progress as a JSON file (it existed earlier and was removed from the demo UI to keep the page simple).

- Export reconciliation report (what's still owed per supplier) as CSV/PDF.
- Handle substitute part codes: map `XX-0001` → `AM-5518` instead of just flagging it.
- Delivery date vs. promised date, to flag late orders.
