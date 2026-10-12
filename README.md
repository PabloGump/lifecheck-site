# thelifecheckapp.com

Static site for LifeCheck, hosted on GitHub Pages.

| Path | What it is |
|---|---|
| `/` | Landing page. `MODE` at the bottom of `index.html` switches it: `'prelaunch'` shows the email sign-up, `'live'` shows the App Store button and the launch link card. |
| `/privacy/`, `/support/` | Copied from the `lifecheck-privacy` repo (keep those two in sync until App Store Connect points here). |
| `/.well-known/apple-app-site-association` | Universal links for `VNKNWSJN4W.app.lifecheck.mobile`: `/add/<code>`, `/packs`, `/programs`, `/upgrade` and the other in-app paths from `src/utils/linking.js` open the app. |
| `404.html` | Any other path. App paths get an "Open LifeCheck" button (`lifecheck://…`) plus a way to get the app; everything else is a normal not-found page. |
| `.nojekyll` | Required, or GitHub Pages hides `.well-known`. |
| `CNAME` | The custom domain. |

Images come from the app repo: `python3 marketing/tools/web_mocks.py ~/Desktop/lifecheck-site`.

## Going live (once)

1. **Deploy the sign-up endpoint** from the app repo:
   `firebase deploy --only functions:joinWaitlist`
   Sign-ups land in Firestore `waitlist/` (closed to every client; read them in the Firebase console).
2. **Push this folder** to a new GitHub repo, then Settings → Pages → Deploy from branch `main`, folder `/`.
3. **Porkbun → thelifecheckapp.com → DNS**:
   - remove the URL forwarding to `thelifecheckapp-com.l.ink`
   - `A` records for `@`: `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - `CNAME` for `www`: `<github-username>.github.io`
4. Back in GitHub Pages settings: custom domain `thelifecheckapp.com`, then tick **Enforce HTTPS** once the certificate is issued (can take up to an hour).
5. Check: `https://thelifecheckapp.com/.well-known/apple-app-site-association` returns the JSON (no redirect), and a friend link opens the app on a phone that has it.

## Launch day

Set `const MODE = 'live'` in `index.html` and push. Export the waitlist from Firestore and send the one launch email.
