# Putting this site on ianhardman.com

Written for your specific setup: domain at Hover, currently pointed at Google Sites,
publishing to GitHub Pages. Replace `YOURNAME` with your GitHub username throughout.

## Where your domain points right now

| Record | Current value | What it does |
|--------|---------------|--------------|
| A `@` | 216.40.34.41 | Hover's forwarding service (sends ianhardman.com → www) |
| CNAME `www` | ghs.googlehosted.com | Google Sites |

Both get replaced below. Nothing is deleted at Google until you choose to — the old site
just stops receiving traffic once DNS moves.

---

## Step 1 — Create the repository

On github.com: **New repository**. Name it `ianhardman.github.io` (or anything else — the
custom domain works either way). Set it **Public**. Do **not** add a README, .gitignore, or
license; the folder already has files and an empty repo avoids a merge conflict on first push.

## Step 2 — Push the files

In Terminal:

```bash
cd "/Users/ianhardman/Desktop/Local Data/Personal Website"
git init
git add .
git commit -m "New personal website"
git branch -M main
git remote add origin https://github.com/YOURNAME/ianhardman.github.io.git
git push -u origin main
```

If the push asks for a password: GitHub stopped accepting account passwords over HTTPS.
Generate a personal access token (github.com → Settings → Developer settings → Personal
access tokens → Tokens (classic) → Generate new token, `repo` scope) and paste that as the
password. macOS will remember it in Keychain after the first time. GitHub Desktop is a
fine alternative if you'd rather not deal with tokens.

## Step 3 — Turn on GitHub Pages

Repo → **Settings → Pages → Build and deployment**. Source: "Deploy from a branch",
branch `main`, folder `/ (root)`. Save.

Within a minute the site is live at `https://YOURNAME.github.io/` (or
`https://YOURNAME.github.io/<repo>/` if you named the repo something else). Check that it
looks right before touching DNS — if something is broken, better to find out now.

## Step 4 — Change DNS at Hover

Sign in at hover.com → **Domains → ianhardman.com**.

First, the **Forwards** tab: if domain forwarding is switched on, turn it off. Hover's
forwarding owns the `@` A record, and you can't edit that record while forwarding is active.

Then the **DNS** tab:

Delete these two records:

- `A` `@` → 216.40.34.41
- `CNAME` `www` → ghs.googlehosted.com

Add these five:

| Type  | Hostname | Value                  |
|-------|----------|------------------------|
| A     | @        | 185.199.108.153        |
| A     | @        | 185.199.109.153        |
| A     | @        | 185.199.110.153        |
| A     | @        | 185.199.111.153        |
| CNAME | www      | YOURNAME.github.io.    |

(The four A records are GitHub's Pages servers — all four, for redundancy. Note the trailing
dot on the CNAME value; Hover accepts it either way but the dot is correct.)

Leave MX and any other records alone — those are unrelated to the website.

## Step 5 — Tell GitHub about the domain

Repo → **Settings → Pages → Custom domain**. Enter `ianhardman.com` and save. The `CNAME`
file in this repo already declares it, so the field may be filled in for you.

GitHub then runs a DNS check. If it complains right after you changed records at Hover, that's
propagation, not a mistake — wait and re-check. Hover's TTL is short, so this is usually
minutes rather than hours.

## Step 6 — HTTPS

Once the DNS check passes, tick **Enforce HTTPS** on the same settings page. GitHub issues a
Let's Encrypt certificate automatically. The checkbox stays greyed out until the certificate
is provisioned, which can take up to an hour after DNS resolves.

## Step 7 — Retire the Google Site

After ianhardman.com is serving the new site, open the old Google Site → **Publish settings**
and remove the custom domain, so Google releases the mapping. You can leave the Google Site
itself published at its `sites.google.com` URL or unpublish it — either is fine.

---

## Making edits later

```bash
cd "/Users/ianhardman/Desktop/Local Data/Personal Website"
# edit the .html files
git add .
git commit -m "Add working paper"
git push
```

The live site updates about 30 seconds after the push.

## If something goes wrong

- **404 on ianhardman.com but YOURNAME.github.io works** — DNS hasn't propagated, or the
  custom domain field is empty. Check both.
- **"Domain does not resolve to the GitHub Pages server"** — an old Hover record is still
  present, usually because forwarding got re-enabled. Recheck the Forwards tab.
- **Site loads unstyled** — `styles.css` didn't get committed. Run `git status` in the folder.
- **Old site still showing** — browser or DNS cache. Try a private window, or
  `dscacheutil -flushcache` on macOS.
