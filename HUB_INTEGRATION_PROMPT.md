# Prompt: integrate the Allergen & Ingredients app into the Sottozero HUB

Paste everything below into the chat where you build the HUB.

---

You are integrating an existing Sottozero app into the HUB: the customer-facing
**Allergen & Ingredients page** for the gelaterias, plus its private admin panel.

Before writing any UI code, read
~/Documents/Claude/Projects/Sottozero HUB/brand/design-system.md and follow every
rule strictly. Use the CSS variables from
~/Documents/Claude/Projects/Sottozero HUB/brand/brand-tokens.css — never invent
colours. Mobile rules are non-negotiable (640px breakpoint, 16px inputs,
44px touch targets, :active scale(0.97) instead of hover).

## The existing app (working, do not regress it)

Source files: ~/Documents/Claude/Projects/Sottozero HUB/brand/allergen-site/
- index.html — customer page (QR target): https://sottozero-allergens.netlify.app
- admin.html — admin panel (PDF upload, editing, publish)
- data.js — bundled fallback flavour list (112 flavours) — THE flavours list;
  treat it as the canonical seed data

### Data model (window.FLAVOURS, array of):
{
  c: "Milk-Based",            // category (editable wording)
  n: "Pistacchio",            // display name
  i: "MILK, sugar, ...",      // full ingredient text; allergens in CAPITALS
  a: ["Milk","Soya","Nuts"],  // EU-14 allergen keys present
  col: "#93B874",             // flavour colour dot (gelato-realistic)
  hidden: false,              // admin can hide from customers
  d: { lf: false, sf: false, gf: false }  // lactose-free / sugar-free / gluten-free badges
}
EU-14 allergen keys: Gluten, Crustaceans, Eggs, Fish, Peanuts, Soya, Milk,
Nuts, Celery, Mustard, Sesame, Sulphites, Lupin, Molluscs.
"May contain" (facility traces, shown for every flavour): Gluten, Eggs, Fish,
Crustaceans, Milk, Nuts.

### Backend (live, already deployed)
- Supabase project: sottozero-products (ref xtoddpoaerqwfhrgpnvn)
- URL: https://xtoddpoaerqwfhrgpnvn.supabase.co
- Table: public.allergen_site — single row id=1,
  data jsonb = { flavours: [...], updated: "17 July 2026" }, RLS: anon SELECT only
- Reads (customer page): GET /rest/v1/allergen_site?id=eq.1&select=data with the
  project anon key; falls back to bundled data.js when unreachable
- Writes: Edge Function POST /functions/v1/allergen-publish
  body { password, flavours, updated } — password verified server-side via
  SHA-256 hash baked into the function; on success it upserts row id=1.
  No other write path exists. Never expose a service key client-side.

### Customer page features (keep all)
Centred hero (Sottozero logo, orange "Our flavours" in Cormorant italic),
allergen picker ("I'm allergic to…" pills), sticky "Avoiding" bar with live
safe-count once scrolled, search, uppercase category tabs, 2-col flavour tile
grid (colour dot + name + allergens + green diet badges), safe-first sorting
with "Contains your allergens" divider, tap-to-expand inline detail panel
(EU-14 matrix Contains/May-contain, "Does not contain" list, full ingredients
with allergen words highlighted), amber facility callout, full legal disclaimer
section, footer with updated date.

### Admin panel features (keep all)
Loads live data from Supabase (fallback data.js). Upload the "Cartello Unico
degli Ingredienti" PDF — parsed fully in-browser with pdf.js (two-column layout:
item names x<150, ingredients x≥150, categories centred x>200; rows split on
vertical gaps ≥11pt; repeated Italian footer blocks skipped; allergens
auto-detected from CAPITALISED words incl. Italian terms mandorla/nocciola/
pistacchio and safety mappings kadayif→gluten, wafer/biscuit/cookie→gluten).
Manual edits survive re-upload when a flavour's ingredients are unchanged.
Per-flavour editor: all 14 EU allergen checkboxes, 3 dietary toggles
(lactose-free / sugar-free / gluten-free), category dropdown to move flavours.
Category rename (✎) and "+ New category". Hide/show per flavour and per
category, live visible-count, publish with admin password.

## Your task in the HUB
1. Add an "Allergens & Ingredients" tile to the HUB launcher (signature tile
   pattern) linking to the customer page, and an admin entry (admin-gated)
   linking to the admin panel.
2. If the HUB has authentication, replace the admin password field with the
   HUB's auth: only signed-in staff with the right role can open the admin and
   publish (the edge function password can stay as a second factor server-side).
3. Keep the customer page exactly as designed — it is public-facing and
   compliance-relevant. Do not change allergen logic, the disclaimer wording,
   or the "may contain" list without being explicitly asked.
4. Audit against design-system.md afterwards and list the rules you applied.
