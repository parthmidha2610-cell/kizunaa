# KIZUNA

KIZUNA is a static web prototype for Japanese anime teams exploring potential audiences in India. It provides an anime intake form, a locally generated market-planning report, and a catalog backed by Firebase Firestore.

## Pages

- `index.html`: landing page
- `input.html`: anime intake with draft data kept in the tab's session storage; database save is optional
- `output.html`: heuristic report and comparisons with catalog records
- `Catalog.html`: Firestore catalog, text search, and local attribute-based matching
- `Processing_File.html`: legacy database ingestion form

**Run with a local web server**, not `file://`, because ES modules and Firebase may require HTTP(S). For example: `python -m http.server 8000`, then visit `http://localhost:8000`.

## Limitations and methodology

The report's segments and recommendations are heuristics, **not** validated audience predictions or financial forecasts. Comparable titles are ranked by shared catalog attributes rather than by a generative AI model. Search links to YouTube and Google Trends can be opened to inspect external information; YouTube view statistics are not collected in the static-only build.

The Firebase web application configuration is intentionally public; it is **not** an authorization mechanism. Access control belongs in Firestore Security Rules and, for privileged operations, a backend. Neither authentication nor Firestore policy enforcement is implemented by this static frontend.

## Security and production checklist

1. **Immediately restrict and rotate** previously embedded Gemini and YouTube API keys in Google Cloud Console; removing strings from the latest branch does not erase Git history or prevent already-copied keys from being abused.
2. Check Firestore `anime_profiles` rules. The current client attempts public reads and client-side writes. **Do not allow anonymous, unrestricted writes in production.** Add Firebase Authentication, validated server-side submission, rate limits and reasonable quotas, then set least-privilege Firestore rules.
3. Decide whether catalog records are intended to be public. Avoid returning private pitch information or user-submitted personal data in public list queries.
4. Deploy any paid AI / YouTube integration through a protected, authenticated backend with quotas; do not embed private service keys in browser JavaScript.
5. Configure an authorized deployment domain and HTTPS; test accessibility, mobile layouts, and a Firestore rules emulator before production.
6. Revisit the legacy `Processing_File.html` form: it saves a different Firestore data schema from `input.html`. Normalize the schemas before relying on cross-source matches.
7. Keep third-party links and claims under review. The report is not independently audited, and its static citations and assumptions should be verified before business use.

## Privacy

The intake form stores a draft in `sessionStorage`, and an optional save sends a title profile to Firestore. The site also fetches catalog data and remote fonts/assets. Closing a tab typically ends a session, but browsers may restore sessions. Use **Clear everything** to remove the current tab's saved draft. A saved Firestore record is not deleted by clearing browser storage.

## Project setup

There is no build, unit test, or CI pipeline configured in this prototype. Firebase browser imports are loaded from Google's CDN. Validate any future deployment in a browser and maintain automated checks for broken paths, safe DOM rendering, and credential exposure.
