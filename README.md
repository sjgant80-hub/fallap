# ◊ fallap · sovereign invoice rewriter

> Rewrites existing invoices as one-click-pay landing pages.
> Cuts AP friction. Gets paid faster.

**Live:** https://sjgant80-hub.github.io/fallap/

---

## For end users · what it does

You've already sent an invoice. It's sitting in someone's AP queue. They have
to download the PDF, key in your bank details, switch to their banking app,
key the amount, key the reference, hit send, then email you confirmation.

That's seven steps. Seven points where they bounce off and pay someone else
first. PYMNTS (March 2026) calls this the *hierarchy of convenience* — the
invoices that win the queue are the ones that take **one click**.

**fallap rewrites your existing invoice as a one-click pay landing page.**

1. Paste the invoice text (or upload PDF, or fill the form)
2. fallap auto-parses customer, amount, due date, line items, your bank/Stripe details
3. Hit *Rewrite*
4. You get a single HTML file: hero amount, one big PAY NOW button, payment-method picker pre-populated with amount and reference. Itemised breakdown collapsed by default. Late terms in small print.
5. Email that HTML as an attachment, or self-host it, or post the link in WhatsApp. Your customer opens it on their phone, picks Stripe/PayPal/Wise/bank transfer, pays in one tap.

### Companion to fallinvoice

- **fallinvoice** generates new invoices from scratch (forward-looking)
- **fallap** rewrites the ones you've already sent (legacy clean-up · AP optimisation)

Pro tier bundles both.

### Pricing (Konomi shim)

- **Free:** 3 rewrites/month · "rewritten by fallap" watermark
- **Pro:** unlimited · branded landing · bulk rewrite · API hook

---

## For developers · architecture

**Sovereign single-file.** One `index.html`, <400KB, vanilla JS, no build step, no framework, no CDN runtime dependencies (Google Fonts only). Works from `file://`.

### Stack

| Layer | Choice |
|---|---|
| Storage | IndexedDB primary · localStorage fallback |
| PDF parsing | Minimal vanilla text extractor (PDF stream `BT...ET` blocks). Falls back gracefully to paste-text. No pdf.js. |
| QR generator | Inline ~2KB QR encoder (numeric / alphanumeric / byte modes, ECC-L, version 1-10) |
| Manifest | Embedded data: URL |
| Fonts | Libre Baskerville (serif) · Syne (sans) · DM Mono (numbers) · DM Sans (body) |
| Mesh | `BroadcastChannel('fall-signal')` · prime **383** · emits `invoice_rewritten` |
| Licence | Konomi shim · Ed25519 dead-file-until-activated · 30-day trial |

### Palette

```
--ox    #8b1a1a  (oxblood · accents)
--brass #b8974a  (secondary)
--cream #c4bfb2  (text)
--void  #0b0a0f  (background)
--gold  #d4a853  (primary CTA)
```

### Mesh signal

```javascript
const ch = new BroadcastChannel('fall-signal');
ch.postMessage({
  kind: 'invoice_rewritten',
  prime: 383,
  source: 'fallap',
  amount: 1240.00,
  currency: 'GBP',
  friction_points_removed: 6,
  ts: Date.now()
});
```

Listen for this if you're wiring fallap into a larger estate (fallmesh, fallcore).

### File layout

```
fallap/
├── index.html   # the deliverable · all logic, styles, manifest inline
├── README.md    # this file
├── LICENSE      # MIT
└── .nojekyll    # tells GitHub Pages to skip Jekyll
```

### Deploy

```bash
git push -u origin main
gh api -X POST repos/sjgant80-hub/fallap/pages -f source[branch]=main -f source[path]=/
```

That's it. No GitHub Actions. No build pipeline.

---

## Source authority

PYMNTS · March 2026 · *The Payment Frictions Costing SMBs Their Cash Flow*

The "hierarchy of convenience" finding: SMBs that surface a one-click pay
action **above the line items** get paid an average of 11 days faster than
those whose invoices require AP to derive the amount, the reference, and the
destination from a PDF.

fallap is one implementation of that finding.

---

## Licence

MIT · Simon Gant · ◊ prime 383 · part of the AIN estate
