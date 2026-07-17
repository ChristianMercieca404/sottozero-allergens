# Sottozero — Allergen & Ingredients Page

Customer-facing allergen/ingredient page for SOTTOZERO — The Gelato Factory,
plus a self-service admin panel.

## Files
- `index.html` — the customer page (QR code target). Loads live flavour data
  from Supabase, falls back to the bundled `data.js` if offline.
- `admin.html` — private admin panel: upload the "Cartello Unico" PDF export,
  edit allergens (all 14 EU), dietary labels, categories, hide/show flavours,
  and publish with the admin password. Publishing writes to Supabase — the live
  page updates instantly, no redeploy needed.
- `data.js` — bundled fallback data (snapshot).

## Infrastructure
- Hosting: Netlify site `sottozero-allergens` → https://sottozero-allergens.netlify.app
- Data: Supabase project `sottozero-products`, table `allergen_site` (id=1, jsonb)
- Publish auth: Supabase Edge Function `allergen-publish` (SHA-256 password check)

## Deploy
Any push to the default branch can auto-deploy if you link this repo to the
Netlify site (Netlify → Site configuration → Build & deploy → Link repository).
Manual alternative: drag the three site files onto the Netlify deploys page.
Note: day-to-day flavour changes do NOT need a deploy — the admin publishes
straight to the database.
