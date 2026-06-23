# Bird World — adambird.com

Adam Bird's blog. A [Hugo](https://gohugo.io/) static site using the (vendored)
`ghostwriter` theme, hosted on [Netlify](https://www.netlify.com/).

## Publishing a post

There is **no manual publish step**. Netlify is connected to this GitHub repo
and auto-builds on every push to `master`.

1. Add a post under `content/posts/` (copy an existing one for the frontmatter
   format: `title`, `description`, `date`, `draft: false`).
2. Commit on a branch, open a PR, and merge into `master`.
3. Netlify detects the merge, runs `hugo`, and deploys to https://adambird.com
   (~1–3 minutes).

That's it. Check it's live: https://adambird.com/posts/<slug>/

## Deploy setup (the bits we keep forgetting)

- **Host:** Netlify. Site name `adambird`, ID `cea14e6a-f126-4f20-80fa-55c11fa5a069`.
- **Build config:** lives in [`netlify.toml`](./netlify.toml) — `command = "hugo"`,
  `publish = "public"`, and a pinned `HUGO_VERSION`.
- **`public/` is gitignored** — Netlify builds it remotely; it's never committed.

### ⚠️ Do NOT bump the Hugo version

`HUGO_VERSION` is pinned to **0.140.2** in `netlify.toml` on purpose. The old
ghostwriter theme breaks on anything newer:

- `.Site.LastChange` was removed in Hugo 0.141.0 (we use `.Site.Lastmod` now,
  but other usages may lurk).
- The internal `_internal/disqus.html` template was renamed in later versions.

If you ever do upgrade Hugo, expect to fix the vendored theme under
`themes/ghostwriter/layouts/` to match.

> Note: Hugo treats a logged **ERROR** (e.g. a deprecation in its final release
> before removal) as a build failure — exit code 1, even though the site renders.
> Don't mask these with `hugo --quiet`; a clean local build must exit 0 with no
> `ERROR` lines. Plain `WARN`s (GA4, raw-HTML) are fine and do not fail the build.

## Local development

Hugo is managed via [asdf](https://asdf-vm.com/). No version is selected by
default, so set one before building:

```sh
asdf local hugo extended_0.140.2   # match the pinned Netlify version
hugo server                        # preview at http://localhost:1313
hugo                               # one-off build into ./public (must exit 0)
```

## Netlify CLI

This folder is linked to the site (`.netlify/`, gitignored). Useful commands:

```sh
netlify status     # who am I / which site
netlify watch      # tail the current build
netlify deploys    # recent deploy history
netlify open       # open the site dashboard in a browser
```

If a fresh checkout isn't linked: `netlify link --id cea14e6a-f126-4f20-80fa-55c11fa5a069`
(requires `netlify login` first).
