# CLAUDE.md

Guidance for AI agents (and humans) working in this repository.

## Who maintains this

This site is operated by a **non-technical person**. You are the one doing the technical work — they are describing what they want in plain English. Optimize for safety and clarity:

- **Explain in plain language.** Avoid jargon; when a technical term is unavoidable, say what it means in one short sentence. Describe *what* changed and *why it matters to them*, not the implementation details.
- If something is complex, broken, or risky, slow down and explain the situation simply before acting — what's wrong, what the options are, and what you recommend.
- Prefer small, reversible changes. Double-check before deleting or overwriting anything.
- Never break the live contact form or the build. When in doubt, ask before acting.
- Don't assume the user can run terminal commands. **You** handle all the Git/code/deploy steps; only ask them to do something themselves if it truly requires their account or a manual login.

## Content style (what appears on the site)

- **No emojis anywhere in the site's visible content.** Use text labels, words, or CSS/SVG icons instead.
- **No em dashes (—) in site copy.** Rewrite the sentence, or use a comma, colon, or simple hyphen (`-`) instead.
- This applies to text users see on the page (headings, body copy, buttons, form labels, etc.). It does not restrict code comments or this CLAUDE.md file.

## Workflow rules (required)

**For every code change, run the full cycle yourself — start to finish — without making the user do any Git steps:**

1. Create a new branch (`fix/...`, `chore/...`, `feat/...` — e.g. `fix/emailjs-credentials`).
2. Make the change and commit it.
3. Push the branch to GitHub.
4. Open a pull request (PR).
5. Merge the PR into `main`.
6. Sync local `main` afterwards (`git checkout main && git pull`).

This is the default — don't ask "should I open a PR?" each time; just do the whole cycle. Merging is what makes the change go live (the site auto-deploys when `main` updates). After it's done, tell the user in plain language what shipped.

Other rules:

- **Never commit directly to `main`.** Always go through a branch + PR, even for tiny changes.
- **Commit messages: short and semantic.** Use Conventional Commit prefixes — `fix:`, `chore:`, `feat:`, `docs:`, `refactor:`. Keep the summary line brief.
- **Do not add "Co-authored-by" or any authored-by trailers** to commits or PR descriptions.
- If a change is risky, destructive, or could break the live site, pause and explain it simply before merging — don't auto-merge those blindly.

## What this is

Petra Construction Co. (Pvt.) Ltd. — a single-page business homepage (marketing site + contact form). Built as a **Vite + React 19 single-page app**. No backend.

## Tech stack

- **React 19** (`react`, `react-dom`)
- **Vite 8** build tool (`vite.config.js`)
- **EmailJS** (`@emailjs/browser`) for the contact form — no server needed
- Plain **CSS** in `src/index.css` (no Tailwind, no CSS framework)

## Commands

```bash
npm install      # install dependencies
npm run dev      # local dev server
npm run build    # production build → dist/
npm run preview  # preview the production build locally
npm run lint     # eslint
```

## Deployment

- Hosted on **Cloudflare Pages** (migrated from Vercel).
- **Build command:** `npm run build`
- **Build output directory:** `dist`
- No SPA fallback / `_redirects` needed — the app is a single URL (see routing note below). Deploys automatically when `main` updates.

## Architecture notes

- **Entry:** `src/main.jsx` renders `src/HomePage.jsx`.
- **"Routing" is fake.** There are no real routes/URLs. `HomePage.jsx` holds a `page` state (`"home"` | `"contact"`) and conditionally renders `Contact.jsx`. Everything lives at `/`.
- **Styling** is `src/index.css` (custom classes like `prj-card`, `nav-links`) plus inline `style={{}}` objects in the JSX, plus one injected `<style>` string in `Contact.jsx` (`contactStyles`). There is exactly one stylesheet — `src/index.css`.
- **Project images** are auto-loaded per folder via `import.meta.glob("/src/assets/projects/<folder>/*.{jpg,jpeg,png,webp}")` in `HomePage.jsx`. To add/remove project photos, just add/delete files in the right folder — no code change needed. Avoid spaces in filenames.

## Contact form / EmailJS

`src/Contact.jsx` sends form submissions via EmailJS using three constants at the top of the file: `EMAILJS_SERVICE_ID`, `EMAILJS_TEMPLATE_ID`, `EMAILJS_PUBLIC_KEY` (the public key is safe to commit).

The form sends these template variables: `name`, `email`, `phone`, `location`, `discipline`, `message`.

**Important EmailJS gotchas:**
- The template's **"To Email"** field (set in the EmailJS dashboard, not in code) must be a **hardcoded address** (`petraconstructionlk@gmail.com`), NOT a `{{variable}}`. A variable there causes the "recipient address is corrupted" error.
- After changing the credentials in code, you must **redeploy** — the live site runs a compiled bundle, so changes don't take effect until a new build ships.

## Known quirks (intentional, do not "fix" blindly)

- The folder `src/assets/projects/nedimala-reidence/` is misspelled on disk, but the code maps it correctly via the `nedimala-residence` key in `allProjectImages`. Renaming the folder requires updating that glob path too.
