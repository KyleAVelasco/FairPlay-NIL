# FairPlayNIL — NIL Contract Review

Working app, rebuilt Nov 11, 2026 from the merged build.

## Using it
Double-click `index.html`. It opens on an introduction page (what FairPlay is, how it works, how to read the score); press Review a contract to continue, or "Try it with a sample deal". Then add a contract (PDF, Word .docx, text file, or pasted text), give it a name, and press Review contract. Each review is saved in the browser and listed under Saved reviews.

The report shows the Deal Score, key terms, every flagged clause with what to ask for, missing protections, and the contract itself with the flagged language highlighted. Click a highlight to jump to its flag, or "Show in contract" to jump the other way. Print report gives a clean printout or PDF.

## Files
- `index.html` — the page (markup and styles)
- `app.js` — the app script and red-flag rules (kept separate so the security policy can block inline scripts)
- `terms.html` — Terms & Privacy, linked from every page footer
- `og-image.png` — the 1200×630 image shown when the link is shared
- `vendor/` — PDF.js 3.11 and Mammoth 1.8, which read PDF and Word files on the device
- `fonts/` — Public Sans, the interface typeface
- `manifest.json`, `sw.js`, `icons/` — lets it install as an app when hosted over https
- `originals/` — untouched copies of the two original artifacts. Do not upload this folder to GitHub: the old copies still use the unpatched PDF reader.

## Limits
- Contracts are read on the device and nothing is uploaded. Reviews live in this browser's storage only; clearing browser data removes them, and they do not sync between devices.
- Scanned or photographed PDFs have no text to read. The app says so and asks for pasted text.
- Older `.doc` files are not supported; save as `.docx` or PDF.
- The rule check only catches wording it knows (31 red-flag rules, 5 favorable terms, 4 missing-protection checks). It has been tested on made-up contracts, not real ones yet.
- "Run deep read" (an AI review of every clause) only appears when the page is published as a Claude artifact.

## Scoring
Penalty points: serious 15, worth negotiating 8, minor 3, missing protection 6; favorable clauses take off 2 each (up to 8).
Score = 100 × e^(−points/110), kept between 1 and 98.
80+ Fair as written, 60+ Negotiate first, 40+ Risky, below 40 Do not sign as written.

## Security
- PDF.js 3.11 is loaded with `isEvalSupported:false`, which closes CVE-2024-4367 (a malicious PDF running code on the page). Upgrading to PDF.js 4.2.67 or later is the long-term fix.
- A Content Security Policy in `index.html` and `terms.html` only allows code and fonts from the site itself, blocks inline and injected scripts, and blocks `eval`. If you add a script, put it in a `.js` file; inline `<script>` blocks and `onclick=` attributes will not run.
- There is no server, database, account or analytics, so there is nothing stored off the user's device.

## Publishing on GitHub Pages
1. Create a public repository (for example `FairPlayNIL`) and upload everything in this folder except `originals/`.
2. In the repository: Settings → Pages → Source: "Deploy from a branch", branch `main`, folder `/ (root)`. The site appears at `https://<username>.github.io/FairPlayNIL/` after a minute or two, over HTTPS.
3. Link previews: in `index.html`, change `og:image` from `og-image.png` to the full address (`https://<username>.github.io/FairPlayNIL/og-image.png`) and add `<meta property="og:url" content="https://<username>.github.io/FairPlayNIL/">`. LinkedIn ignores images given as a relative path. Check the result at linkedin.com/post-inspector.
4. After any change to the files, bump `CACHE` in `sw.js` (for example `fairplay-v6`) so installed copies pick up the new version.
