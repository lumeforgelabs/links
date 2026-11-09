# links

Static pages on `links.lumeforgelabs.com` (GitHub Pages), used by Lumeforge Labs apps.

| Path | Purpose |
| --- | --- |
| `/recipes/auth/link/` | Target of the sign-in button in the Recipes app's email (F-01 in `lumeforgelabs/recipes`). On a phone it offers "Open in the app", which hands the token to `recipes://auth/link`; on a computer it says to enter the code in the app. |

Rules for this repo:

- **Public on purpose**: GitHub Pages on the organization's free plan only serves public repositories. Never put secrets, keys or user data here.
- The pages never sign anyone in and never send the token anywhere: no external scripts, fonts or analytics, `no-referrer`, and a Content Security Policy that only allows the page's own inline script (by hash). Changing that script means updating the hash in the CSP.
- Later: `/.well-known/assetlinks.json` (Android) and `/.well-known/apple-app-site-association` (iOS), so phones open the app directly. They need the release signing certificate and the Apple Team ID.
