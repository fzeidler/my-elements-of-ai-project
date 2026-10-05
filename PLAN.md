# Our Cookbook – Project Plan

A private online cookbook for our household, with recipe revisions, "next time" notes,
source links, free recipe import, and sharing with friends and family.

Status: **planning** – no code yet.

---

## 1. Goals

1. Store recipes **and their revisions**. Each revision carries a note on what changed,
   and two revisions can be compared side by side.
2. Add **"next time" notes** to a recipe without creating a new revision.
3. **Link to the source** (e.g. koket.se) and keep a snapshot of the original.
4. **Import** a recipe from a link (or pasted text / photo) into our own format – **at no running cost**.
5. **Share** recipes with people outside the household.

---

## 2. Data model

```
Cookbook (our household – both of us are owners/editors)
 └─ Recipe ("Grandma's meatballs")
     ├─ Source        – url, site name, imported date, snapshot of the original
     ├─ Revision 1    – "Imported from koket.se"
     ├─ Revision 2    – "Less salt, added allspice"
     ├─ Revision 3    ← current
     ├─ Notes         – "next time" notes (open / resolved)
     └─ Share links
```

### Recipe
- Title, tags, cookbook it belongs to, pointer to the current revision, created by/at.

### Revision (immutable snapshot)
- Servings, prep/cook time, description.
- **Ingredients as structured rows:** amount · unit · ingredient · optional comment
  (e.g. `2 · dl · grädde · (vispgrädde)`), optionally grouped ("Sauce", "Dough").
- Steps as an ordered list.
- **Change note** (required from revision 2 onwards), author, timestamp, parent revision.
- Revisions are never edited. Any change creates a new revision.

### Note ("next time")
- Text, author, timestamp, which revision it was written against.
- Status: open → resolved. When creating a new revision, the app lists open notes and lets
  us tick the ones the new revision addresses (stored as "resolved in revision N").

### Source
- URL, site name, import date, raw snapshot of the imported data (sites change, links die).

### Share link
- Random unguessable token, recipe, mode (*always latest* or *pinned revision*),
  created by, revoked yes/no.

---

## 3. Revision comparison

- Side-by-side or inline diff between any two revisions.
- Ingredients compared row by row ("200 g → 150 g smör", "+ 1 tsk kryddpeppar").
- Steps compared as text, highlighting changed words.
- Shows the change notes of all revisions in between.

---

## 4. Recipe import (free – no paid API)

All import paths end on a **review screen** where we correct anything before saving.
The saved result becomes *Revision 1 – "Imported from …"* with the source attached.

| Order | Method | Cost | Covers |
|---|---|---|---|
| 1 | **Structured data on the page** (schema.org/Recipe JSON-LD) | Free | Most large recipe sites (to verify: koket.se, ICA, Arla) |
| 2 | **"Copy prompt to your AI chat"** – app gives a ready-made prompt; user pastes it + the recipe into Claude.ai/ChatGPT/Gemini, then pastes the JSON answer back | Free (uses existing chat) | Any page, PDF, cookbook text |
| 3 | **Paste text + rule-based parser** – splits lines like "2 dl grädde" into amount/unit/ingredient | Free | Quick manual entry |

**Photos** (cookbook pages, handwritten cards): use the phone's built-in text recognition
(iPhone Live Text / Google Lens) to copy the text, then use method 2 or 3.
Optional later: in-browser OCR (Tesseract.js).

**Possible later upgrade:** one-click AI import via an API (roughly a cent or less per import
with a small model) – only if the free fallback feels too clunky.

Notes:
- Fetching the page must happen server-side (browsers block reading other sites directly).
- Keep a list of Swedish units and their normalisation (dl, msk, tsk, krm, g, kg, st, nypa…).

---

## 5. Sharing

- **Share link:** read-only, unguessable, revocable; recipient needs no account.
- Choose **"always latest"** or **a specific revision**.
- Shared page always shows the **source / attribution**.
- Imported recipes are someone else's text → share privately, never publish openly.
- Later: invite people with accounts, and **"Save a copy"** into their own cookbook
  (keeps a link back to the original).

---

## 6. Extra features (backlog)

| Feature | Why |
|---|---|
| Cooking log ("Cooked 12 Oct, ★★★★") | History of how often and how well; natural place to add notes |
| Scale servings | 4 → 6 portions automatically |
| Cooking mode | Large text, screen stays awake, tick off steps |
| Photos per revision | See how each version turned out |
| Tags & search | By type ("vegetarian", "weekday") or ingredient ("what can I make with leeks?") |
| Shopping list | Combined list from several recipes |
| Variants | E.g. a vegetarian version alongside the main recipe, instead of replacing it |
| Installable app (PWA) | Home-screen icon, works with poor signal in the kitchen |
| Export / backup | Our recipes stay ours |

---

## 7. Tech stack (all free tiers)

- **Supabase** – Postgres database, login, photo storage, row-level security
  ("only our household can edit").
  Note: free projects pause after ~1 week without activity.
- **Next.js or SvelteKit** – the web app, hosted on **Vercel** (free tier).
- **No paid AI service** in the initial version.

---

## 8. Build order

1. **Foundation** – project setup, login, our shared cookbook.
2. **Recipes & revisions** – create/edit (= new revision), change notes, revision history.
3. **Revision comparison** – diff view.
4. **"Next time" notes** – open/resolve, link to revisions.
5. **Import, step 1** – paste text + rule-based parser, review screen.
6. **Import, step 2** – from link via schema.org data, with source snapshot.
7. **Import, step 3** – "Copy prompt to your AI chat" flow.
8. **Sharing** – share links (latest / pinned revision), revoke.
9. **Backlog** – pick from section 6.

---

## 9. Open questions

1. Language of the app: Swedish, English or both?
2. Main device: phone in the kitchen, or computer?
3. Sharing: are share links enough, or should others get their own accounts/cookbooks?
4. Is this the Building AI course final project? If so, update README.md with the idea.
5. Coding experience → decides between the simplest setup and a more flexible one.
