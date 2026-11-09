# links

Static pages on `links.lumeforgelabs.com` (GitHub Pages), used by Lumeforge Labs apps.

| Path | Purpose |
| --- | --- |
| `/recipes/auth/link/` | Target of the sign-in button in the Recipes app's email (F-01 in `lumeforgelabs/recipes`). On a phone it offers "Open in the app", which hands the token to `recipes://auth/link`; on a computer it says to enter the code in the app. |

Rules for this repo:

- **Public on purpose**: GitHub Pages on the organization's free plan only serves public repositories. Never put secrets, keys or user data here.
- The pages never sign anyone in and never send the token anywhere: no external scripts, fonts or analytics, `no-referrer`, and a Content Security Policy that only allows the page's own inline script (by hash). Changing that script means updating the hash in the CSP. The page refuses to run inside a frame.
- Keep LF line endings (`.gitattributes`): the CSP hash covers the exact bytes of the script.
- Branches `main` (live) and `offline` (pause version) can't be deleted or force-pushed (ruleset).
- Later: `/.well-known/assetlinks.json` (Android) and `/.well-known/apple-app-site-association` (iOS), so phones open the app directly. They need the release signing certificate and the Apple Team ID.

## Pause and shutdown

Without the page, sign-in still works: users enter the code from the email. The button in the email then lands on the pause page or an error.

**Pause** (safe, one command, keeps the domain claimed). Serves the `offline` branch: a static "temporarily unavailable" page without scripts.

```bash
gh api -X PUT repos/lumeforgelabs/links/pages -f "source[branch]=offline" -f "source[path]=/"
```

**Resume**:

```bash
gh api -X PUT repos/lumeforgelabs/links/pages -f "source[branch]=main" -f "source[path]=/"
```

GitHub rebuilds the site in about a minute after each switch.

**Full shutdown** (nothing published). Order matters: while `links.lumeforgelabs.com` still points to GitHub but no repository claims it, anyone could claim it for their own Pages site and receive sign-in tokens (subdomain takeover).

1. INWX: delete the `links` CNAME record.
2. Then: `gh api -X DELETE repos/lumeforgelabs/links/pages`
3. Remove `https://links.lumeforgelabs.com/recipes/auth/link` from the Supabase redirect allow-list in `lumeforgelabs/recipes` (`supabase/config.toml`, then `supabase config push`), or the app keeps sending links to it.

Verifying `lumeforgelabs.com` for GitHub Pages in the organization settings blocks such takeovers in any case.
