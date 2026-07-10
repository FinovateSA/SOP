# Finovate — Claude SOP Library

Internal standard operating procedures for using **Claude** at Finovate, published as static HTML pages via **GitHub Pages**.

## 🌐 Live site

**https://finovatesa.github.io/SOP/**

| Page | Link |
| --- | --- |
| Landing / menu | https://finovatesa.github.io/SOP/ |
| Staff Setup | https://finovatesa.github.io/SOP/finovate-claude-setup.html |
| Using Claude | https://finovatesa.github.io/SOP/finovate-claude-usage_3.html |

> ⚠️ GitHub Pages is **public** — anyone with the link can view these pages, even though contributing to the repo is restricted. Don't put anything confidential in the SOPs.

## 📁 What's in here

| File | Purpose |
| --- | --- |
| `index.html` | Landing page — a menu linking to each SOP |
| `finovate-claude-setup.html` | SOP 01 — installing and configuring Claude |
| `finovate-claude-usage_3.html` | SOP 02 — getting day-to-day work out of Claude |

Each SOP is a **single, self-contained HTML file** (styling and content in one file). The shared look — navy `#202658` + green `#00d683`, Montserrat + JetBrains Mono — is defined inline in each page.

## ✏️ How to add or edit an SOP

Publishing is automatic: **commit to `main` → GitHub Pages redeploys in ~1–2 minutes.** No build step.

1. Add or edit an `.html` file in the repo root.
2. Commit and push to `main`.
3. Wait ~1–2 min, then refresh the live URL.

**Adding a new SOP?** Give it a clean, lowercase, hyphenated filename (e.g. `finovate-claude-security.html`) and add a matching card to `index.html` so it shows on the landing page.

## ⚙️ How it's hosted

- **GitHub Pages**, source = `main` branch, `/(root)` folder (repo → **Settings → Pages**).
- The URL for any file is simply `https://finovatesa.github.io/SOP/<filename>`.
- The base URL works because a file named exactly `index.html` exists at the root.

## 👥 Owners

Yanga & Milan
