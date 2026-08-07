# Website for Mouse vs Cat: Kitchen Chase

Static pages needed for the Google Play listing. No build step, no JavaScript,
no external requests, no cookies — plain HTML and one stylesheet, so the pages
themselves collect nothing either.

```
website/
  index.html            landing page (what the game is, how it plays)
  privacy-policy.html   the URL Play Console asks for
  data-use.html         plain-language version of the Data safety answers
  support.html          support / contact page, also good as the Play support URL
  terms.html            terms of use
  app-ads.txt           must end up at the DOMAIN ROOT, not in a subfolder
  icon-512.png          store icon, also used by the landing page
  style.css
  .nojekyll             tells GitHub Pages to serve the files as they are
```

## 1. Placeholders

Already filled in:

| Value | Where |
| --- | --- |
| `HINDUKUSH` | footers, privacy policy, terms |
| `vikasaivyas@gmail.com` | every contact link |
| `7 August 2026` | effective / last-updated dates |
| `pub-7642513984044740` | `app-ads.txt` |
| Play link `…details?id=com.mousevcat.game` | landing page (works once published) |

| `https://abhiyarecord-hue.github.io/` | privacy policy contact block |

Nothing left to fill in.

## 2. Host it on GitHub Pages

**One rule decides the repo name.** AdMob crawls `app-ads.txt` starting at the *root*
of your domain, so the file has to answer at `https://your-domain/app-ads.txt`.
GitHub Pages gives you two kinds of site, and only one puts files at the root:

| Repo name | Site URL | `app-ads.txt` lands at | Works? |
| --- | --- | --- | --- |
| `abhiyarecord-hue.github.io` | `https://abhiyarecord-hue.github.io/` | `https://abhiyarecord-hue.github.io/app-ads.txt` | **yes** |
| any other name | `https://abhiyarecord-hue.github.io/<repo>/` | `…/<repo>/app-ads.txt` | no — not the root |

So create the repo named **exactly** `abhiyarecord-hue.github.io`, public.

Steps:

1. Create the public repo `abhiyarecord-hue.github.io`.
2. Copy the **contents** of this `website/` folder into the repo root — not the folder
   itself. `index.html` must sit at the top level.
3. Commit and push to the `main` branch.
4. Settings → Pages → Source: *Deploy from a branch*, branch `main`, folder `/ (root)`.
5. Wait a couple of minutes, then check these three URLs open:

```
https://abhiyarecord-hue.github.io/
https://abhiyarecord-hue.github.io/privacy-policy.html
https://abhiyarecord-hue.github.io/app-ads.txt        <- must show plain text, not a 404
```

The last one matters most. If it 404s, AdMob cannot verify app ownership.

## 3. What to enter in Play Console

- **Privacy policy** (App content → Privacy policy): `https://abhiyarecord-hue.github.io/privacy-policy.html`
- **Support email**: `vikasaivyas@gmail.com`
- **Website** (store listing): `https://abhiyarecord-hue.github.io` — AdMob uses this to find `app-ads.txt`
- **Data safety**: answer it to match `data-use.html`:
  - Does your app collect or share any of the required user data types? → **No** for everything the
    developer collects. The app has no accounts and sends nothing to us.
  - Note that a third-party ads SDK (Google AdMob) is present. In the Data safety form, data handled
    by the ad SDK for advertising (device or other IDs) must be declared as **collected and shared**
    for **Advertising or marketing**, with **Device or other IDs** ticked. Google's own guidance for
    AdMob publishers is here: <https://support.google.com/admob/answer/11150746>.
  - Data is **not** encrypted in transit by us because we transmit nothing; the ads SDK uses HTTPS.
  - No data deletion request mechanism is needed as no account data exists; local data is removed by
    uninstalling or clearing app data.
- **Ads declaration**: yes, the app contains ads.
- **Content rating questionnaire**: no violence beyond cartoon chase, no user interaction, no purchases.

## 4. Before the first release — app side checklist

These are in the app, not on the website, but they belong to the same launch.
The full version lives in `PLAY-STORE-CHECKLIST-HINDI.md`; the short form:

1. ~~Real AdMob IDs.~~ **Done** — app ID is in the manifest and all three ad units are wired.
   Debug builds use Google's demo units, release builds use the live ones, decided by the
   APK's debuggable flag.
2. **AdMob account approval.** Live ads cannot serve until the AdMob account is verified
   (enter payment details) and the app passes its readiness review, which needs the app
   to be published first. "Account not approved yet" is expected before that.
3. **EEA / UK consent.** For users in those regions Google requires a consent message before
   personalised ads. Add Google's User Messaging Platform (UMP) SDK and show the form on first run,
   or exclude those countries in the Play listing.
4. **Families policy.** If you plan to target children in the Play listing, AdMob must be configured
   for child-directed treatment (`setTagForChildDirectedTreatment`) and only Families-approved ad
   partners may serve ads.
5. **Release signing.** The release signing config reads `keystore.properties`; create the keystore
   and that file before building the release bundle (`./gradlew :app:bundleRelease`).
6. **Store assets.** Icon 512×512, feature graphic 1024×500, and at least two phone screenshots.

## 5. Keeping it honest

If the app ever gains analytics, crash reporting, cloud saves, purchases or a login, then
`privacy-policy.html`, `data-use.html` and the Play Data safety form all have to be updated in the
same release. Right now all three say the same thing: nothing leaves the device except ad requests.
