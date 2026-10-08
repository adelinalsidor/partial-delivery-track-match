# Roadmap

Current state (**Stage 1**): standalone static page, no backend. Extraction is manual — the user runs the built-in prompt in a Claude conversation and pastes the CSV back. Data lives in the browser (`localStorage`) with JSON export/import. A "try with an example" button demonstrates the full flow with no setup and no cost.

## Stage 2 — Automatic extraction (planned)

Replace the copy-paste step with a file upload: the user drops a PDF, spreadsheet or photo, and the tracker gets the CSV back directly.

- **Backend:** one serverless function (e.g. Vercel, `api/extract.js`) that calls the Claude API (`messages.create`, PDF/image content blocks) with the same extraction prompt already in the page. The API key lives in a server-side environment variable, never in the page.
- **Model:** Haiku 5.5 — cheap and sufficient for table extraction. At the official price checked 2026-10-08 ($0.10 / $0.50 per million input / output tokens) a typical document is roughly $0.002; see the README for the assumptions. Note Haiku 5.5 pricing rises for prompts over 100,000 tokens.
- **Cost protection (required before making it public):**
  - Per-IP and global daily request limits.
  - Upload size cap (serverless request bodies are limited to a few MB — verify current limit before building).
  - Monthly spend cap set in the Anthropic console.
  - Keep the "try with an example" button as the zero-cost path for visitors.
- **Privacy:** documents would leave the user's browser and reach the Claude API. Tell users explicitly before enabling it for real client data.

## Stage 3 — Shared, persistent data (planned)

For teams that need more than one computer or one person per order.

- Accounts and storage (e.g. Supabase: auth + Postgres), orders and deliveries as tables instead of a local JSON blob.
- Multiple users on the same order, full delivery history, per-supplier views.
- Security review (row-level security, input validation) before real data goes in.
- Only worth building once there is a real user asking for it.

## Smaller ideas

- Export reconciliation report (what's still owed per supplier) as CSV/PDF.
- Handle substitute part codes: map `XX-0001` → `AM-5518` instead of just flagging it.
- Delivery date vs. promised date, to flag late orders.
