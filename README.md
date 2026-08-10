# ianhardman.com

Static personal website — plain HTML and one CSS file, no build step, no dependencies.

```
index.html        Home (bio, education, contact)
research.html     Works in progress and publications
teaching.html     Courses
styles.css        All styling (light + dark mode, responsive)
assets/headshot.jpg   Portrait shown on the home page
CNAME             Custom domain for GitHub Pages
```

## Editing

Open the `.html` file in any text editor and change the text. To add a paper, copy an
existing `<li>` block in `research.html` and edit it. Nothing needs to be compiled or
rebuilt — save the file, commit, and the live site updates.

To preview locally before pushing, run this in the folder and open http://localhost:8000:

```bash
python3 -m http.server 8000
```

## Replacing the headshot

Save your portrait as `assets/headshot.jpg`. Anything roughly square or 4:5 portrait works;
about 800×1000 pixels is plenty. The file currently in place is a placeholder.

## Deploying to GitHub Pages

1. Create a public repo on GitHub. Naming it `ianhardman.github.io` is the simplest option,
   but any name works.
2. Push these files to the repo root:

   ```bash
   cd "path/to/this/folder"
   git init
   git add .
   git commit -m "New personal website"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo>.git
   git push -u origin main
   ```

3. In the repo: **Settings → Pages → Build and deployment**. Set Source to
   "Deploy from a branch", branch `main`, folder `/ (root)`. Save.
4. Still under Settings → Pages, enter `ianhardman.com` as the custom domain. The `CNAME`
   file in this repo already declares it, so it should populate automatically.
5. At your domain registrar (wherever ianhardman.com is currently pointed at Google Sites),
   replace the existing DNS records with:

   | Type  | Name  | Value                   |
   |-------|-------|-------------------------|
   | A     | @     | 185.199.108.153         |
   | A     | @     | 185.199.109.153         |
   | A     | @     | 185.199.110.153         |
   | A     | @     | 185.199.111.153         |
   | CNAME | www   | `<your-username>.github.io.` |

   Delete the old Google Sites records for `@` and `www` — leaving them in place will make
   the domain resolve inconsistently.
6. Wait for DNS to propagate (usually minutes, up to a few hours), then tick
   **Enforce HTTPS** in Settings → Pages. GitHub issues the certificate automatically.

The old Google Sites site can stay published until DNS has switched over; nothing breaks by
leaving it there.
