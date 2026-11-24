# BMT's blog

Source for [ibmt.pages.dev](https://ibmt.pages.dev). A small personal blog built
with [Zola](https://www.getzola.org) and the [tabi](https://github.com/welpo/tabi)
theme.

If you like how it looks and want the same starting point, fork this repo and
strip it down. This document tells you what to delete, what to keep, and the
exact errors you'll probably hit along the way.

**This assumes** you've used a terminal before (you can `cd` around, run
commands, edit text files). If you've never installed anything from a terminal
or used git, start with [Zola's getting started
guide](https://www.getzola.org/documentation/getting-started/installation/)
first — it will take you about an hour and this repo will make much more sense
afterwards.

---

## What is all this

- **Zola** is a program that takes a folder of Markdown files and turns them
  into a website (HTML files). You write plain text, Zola produces the site.
- **tabi** is a theme. A theme is a bundle of templates, colours, and
  behaviours that makes Zola's output look decent without you writing any HTML
  or CSS. tabi is the "skin."
- **Cloudflare Pages** is where the finished site lives. It watches this GitHub
  repo and rebuilds the site every time you push a commit. Free.

You write Markdown in `content/`. You push to GitHub. Cloudflare turns it into a
website. That's the whole loop.

---

## What you need installed

- **git** — to clone and push. `sudo pacman -S git` on Arch.
- **Zola** 0.23.6 or newer — `sudo pacman -S zola` on Arch, or grab a binary
  from [the releases page](https://github.com/getzola/zola/releases).
- A **text editor**. Any. Nano, Micro, VS Code, whatever you already use.
- Free accounts on [GitHub](https://github.com) and
  [Cloudflare](https://cloudflare.com) if you want to deploy.

Check you have the right Zola version:

```sh
zola --version
```

If it says anything below `0.23.6`, this theme won't build. Update Zola 
first.

---

## Getting it running on your machine

```sh
git clone https://github.com/YOURNAME/blog.git
cd blog
zola serve
```
The last command starts a local web server and opens `http://127.0.0.1:1111` in
your browser. Every time you save a file, the browser reloads automatically.
`Ctrl+C` in the terminal stops it.

When you want the final HTML files (the ones Cloudflare would publish), run:
```sh
zola build
```
That writes everything into `public/`. **Don't edit files in** `public/` — they
get wiped and regenerated on every build. `public/` is also gitignored, meaning
git ignores it and it never gets uploaded. Only `content/`, `static/`,
`config.toml`, and `themes/` matter.

---

## Stripping it back to a blank slate

These files are mine. Delete them if you don't want my stuff:

| Path | What it is |
|---|---|
| `content/blog/why-im-writing-this/` | My first post (English + Arabic) |
| `content/blog/hello/` | Placeholder post (English + Arabic) |
| `content/about.md`, `content/about.ar.md` | Placeholder about page — write your own |
| `content/privacy.md`, `content/privacy.ar.md` | Placeholder privacy page — write your own |
| `static/img/social_cards/` | Empty folder where I used to keep a preview image |

Delete them with `rm -rf` for folders, `rm` for single files.

These files must stay. Deleting them breaks the site:

| Path | Why it matters |
|---|---|
| `content/_index.md`, `content/_index.ar.md` | Your homepage. Edit the title and the paragraph, don't delete. |
| `content/blog/_index.md`, `content/archive/_index.md`, etc. | Section landing pages. Edit the `title` line only. |
| `themes/tabi/` | The theme's templates, styles, and JavaScript. **Don't edit** — see "Updating tabi" below for why. |
| `static/custom.css` | All my visual tweaks in one file. Edit freely. |
| `config.toml` | Site config. Only the top section matters at first — see below. |

---

## Editing `config.toml`

The top 10 lines are the ones you actually need to change:
```toml
base_url = "https://yourdomain.pages.dev"    # change to YOUR deployed URL
title = "~/BMT"                              # your site title (shows in the tab)
description = "..."                          # shown in search engines and link previews
author = "BMT"                               # your name
```
Everything under `[extra]` has working defaults. Change one thing at a time,
run `zola serve`, look at the result, keep or revert. Don't edit twenty settings
at once or you'll have no idea what broke.

The settings you're most likely to want:
```toml
favicon_emoji = "🍋‍🟩"                          # the little icon in the browser tab
stylesheets = ["custom.css"]                  # your CSS overrides
socials = [...]                               # footer social links
footer_menu = [...]                           # about / privacy / sitemap links
```

---

## Writing a post

Create a folder inside `content/blog/` and put an `index.md` inside it:
```sh
mkdir content/blog/my-first-post
touch content/blog/my-first-post/index.md
```
Then fill it with: 
```markdown
+++
title = "My first post"
date = 2026-11-24
[taxonomies]
tags = ["random"]
+++

Normal markdown here. **Bold**, *italic*, [links](https://example.com),
`code`, lists, images — all work.
```
Run `zola serve`. The post appears on the homepage, at `/blog/`, and at
`/tags/random/` automatically. You don't register it anywhere.

If you skip the `date` line, the post won't show up in any date-sorted listing
(which is all of them). That's the number one reason "my post doesn't appear."

---

## Multilingual content (Arabic for example)

Every English file has a matching `.ar.md` twin:

* `content/blog/my-post/index.md` → `content/blog/my-post/index.ar.md`
* `content/_index.md` → `content/_index.ar.md`

Zola pairs them by filename. English lives at `/`, Arabic at `/ar/`.


If you don't want Arabic at all, do all of these together:

1. Delete every `.ar.md` file
2. Delete the `[languages.ar]` block in `config.toml`
3. Delete the `footer_menu.ar` array in `config.toml`

If you only delete some, you'll get 404s in the language switcher.

---

## What's customised in this repo

Three things differ from a vanilla tabi install:

1. **`static/custom.css`** — one file that overrides tabi's colours, hover
   behaviour, and adds a serif font to article bodies. Loaded via the
   `stylesheets` array in `config.toml`. Edit the CSS variables at the bottom of
   the file to re-theme the whole site.

2. **`skin = ""`** in `config.toml` — instead of picking a built-in tabi skin,
   the colours come entirely from `custom.css`. That's why the site is
   mint/green.

3. **The theme is vendored, not a submodule** — `themes/tabi/` is a trimmed
   copy of the tabi repo (demo content, GitHub workflows, and screenshots
   removed to save space). This means:

   - The site builds anywhere with no `git submodule` command needed.
   - You cannot pull tabi updates with `git pull`. To update, compare against
     the [upstream repo](https://github.com/welpo/tabi) and copy changed
     template files over by hand.
   - Editing files inside `themes/tabi/` is fine, but if you ever update the
     theme later, you'll lose those edits. Put all your customisation in
     `static/custom.css` instead.

---

## Deploying to Cloudflare Pages

You'll need the repo pushed to GitHub first.

1. Log in at [dash.cloudflare.com](https://dash.cloudflare.com).
2. In the left sidebar, open the **Compute** dropdown.
3. Click **Workers & Pages**.
4. Click the blue **Create** button.
5. **Don't** follow the big AI/Worker setup flow it shows you. Instead, look
   for a small text link underneath it that says something like **"Looking to
   deploy Pages?"** or **"Go to the legacy Pages UI"**. Click that.
6. You're now in the actual Pages creation screen. Click **Connect to Git**.
7. Pick your repo.
8. Settings:
   - **Framework preset:** Zola
   - **Build command:** `zola build --base-url "$CF_PAGES_URL"`
   - **Build output directory:** `public`
   - **Environment variable:** `ZOLA_VERSION` = `0.23.6`
9. **Save and Deploy.** First build takes ~90 seconds. You'll get a URL like
   `yourproject.pages.dev`.

Change `base_url` in `config.toml` to match that URL, commit, push — Cloudflare
rebuilds automatically. From then on, every `git push` deploys.

`$CF_PAGES_URL` is provided by Cloudflare. On your production branch it's your 
real URL; on preview branches it's the temporary preview URL. The flag makes both 
render correctly. If you disable preview deployments, you can drop the flag and 
use plain `zola build`.

**You only need to change `base_url` once.** When you run `zola serve` locally,
Zola automatically overrides `base_url` with `http://127.0.0.1:1111` — you don't
need to swap it back and forth every time you test.

**Why the "legacy" link?** Cloudflare is pushing Workers (and their AI tooling)
as the primary product, so the Pages UI is now buried a few clicks deep. The
"legacy Pages" link is the officially-supported way in — it's not a deprecated
backdoor, just hidden. If Cloudflare renames it again, look for the smallest
text link on the Create page; that's where they keep moving it.

**Note on the subdomain:** if `yourproject.pages.dev` isn't available, Cloudflare
appends a random suffix like `-8cg` (giving you `yourproject-8cg.pages.dev`).
You can't remove it without deleting the project and recreating with a different
name. It's cosmetic — a custom domain later replaces it entirely.

---

## Errors you'll probably hit

**Theme tabi not found**
`themes/tabi/` is empty. Re-clone it:
`git clone https://github.com/welpo/tabi themes/tabi`

**A page 404s that should exist**
Either the file has no `date` line (required if the parent section uses
`sort_by = "date"`), or it has `hidden = true` in its front matter — which hides
it from listings but keeps the URL working.

**About and Privacy show up on your homepage feed**
Add `hidden = true` to their front matter:
```toml
+++
title = "About"
date = 2026-11-24
hidden = true
+++
```

---

## Credit

Theme by [welpo](https://github.com/welpo/tabi), licensed under MIT.
The trimmed copy in `themes/tabi/` is derived from the upstream repo at the
time of vendoring, see `themes/tabi/LICENSE` for the full license text.