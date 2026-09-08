# Order Delivery Tracker

A single-page tool for auto parts procurement: track what's been delivered against what was ordered, per purchase order, without opening every invoice by hand.

## What it does

Paste a purchase order and its invoices — extracted as CSV by Claude from a PDF, spreadsheet, or photo, using the built-in prompt — and the tracker matches delivered quantities against ordered quantities by part code, per line item. Each line shows a live status: not started, partial, complete, or over-delivered.

## Why it's more than a spreadsheet

- **Matching, not manual reconciliation.** Deliveries are matched to order lines by part code automatically; codes that don't match any line are flagged separately instead of silently dropped, so a wrong code or a substitute part never gets lost.
- **Duplicate part codes on one order get merged, not duplicated.** If a purchase order lists the same part code twice (e.g. split across a delivery schedule), the tracker sums the ordered quantity into one line rather than creating two — otherwise later delivery-matching would only ever find the first one, leaving the second permanently stuck at "not started."
- **No backend.** State lives in the page; "Save progress" / "Load progress" round-trip it as a JSON file, so a procurement person can keep a running file per project without any server or login.

## Known limitation

Save/load uses a Claude Artifacts-specific API (`window.claude.downloads`) and only works when the page is opened inside a Claude Artifact. Opened as a plain HTML file (e.g. straight from this repo), the rest of the tool works normally, but those two buttons are inert — this repo is the source code and UI/logic demo, not a deployable standalone app as-is.

## Usage

1. Open the page (inside a Claude Artifact for full functionality).
2. Copy the extraction prompt from Step 1, paste it into a Claude conversation along with your PO or invoice file.
3. Paste the resulting CSV into **Comandă nouă** to create an order, or into an existing order's **Adaugă livrare** to log a delivery.
4. Save progress before closing — nothing persists automatically between sessions.
