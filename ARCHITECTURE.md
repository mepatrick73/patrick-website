# Architecture

## Stack

| Layer | Tool |
|---|---|
| Framework | [Astro](https://astro.build) |
| Hosting | Cloudflare Pages |
| Content | Markdown/MDX (built-in), Typst (compiled to SVG) |
| Graphics | Astro islands (Three.js / Canvas, added as needed) |

## Branch strategy

```
feature/* → dev → main
                ↓        ↓
            staging    production
```

- **feature branches** — ephemeral, get a Cloudflare preview URL automatically
- **dev** — auto-deploys to staging on push, CI must pass
- **main** — production, requires manual approval in GitHub Actions before deploy

## CI/CD pipeline

```
push to dev
└── GitHub Actions
    ├── lint / build check
    ├── typst compilation check
    └── deploy to staging (Cloudflare Pages preview)

PR: dev → main
└── GitHub Actions
    ├── same checks
    ├── wait for manual approval  ← "environment: production" gate
    └── deploy to production on approval
```

The manual approval gate uses GitHub's built-in environment protection rules:
`Settings → Environments → production → Required reviewers`.

## Build pipeline

```
source files
├── src/content/     ← Markdown/MDX pages
├── src/typst/       ← Typst source files (.typ)
└── src/components/  ← Astro/UI components

build step
├── typst compile src/typst/*.typ → public/typst/*.svg
└── astro build → dist/

deploy
└── dist/ → Cloudflare Pages CDN
```

## Implementation order

1. Scaffold Astro + deploy to Cloudflare Pages (pipeline first)
2. Basic layout & styling (nav, footer, typography)
3. Markdown pages (blog posts, notes)
4. Typst integration (build script + SVG embedding)
5. Web graphics (Astro islands with Three.js / Canvas)
