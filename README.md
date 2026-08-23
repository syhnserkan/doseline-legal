# doseline-legal

Legal documents for the **Doseline** mobile application (`com.serkansayhan.doseline`).

Plain static HTML — no build step, no dependencies, no fonts fetched from anywhere. Palette and
type are Doseline's own tokens from `src/theme/index.ts`; the app is light-theme only by
commitment, so these pages are too.

```
index.html     landing page linking to both documents
privacy.html   Privacy Policy
terms.html     Terms of Service
assets/        app icon at three sizes, generated from doseline/assets/images/icon.png
```

## English only, unlike the other -legal repos

`alibi-legal` and `dark-ai-studio-legal` each ship three languages behind a switcher. This one does
not, because Doseline itself does not: `PROJECT.md` puts every language other than English outside
the MVP, and the app ships one bundle. A Turkish legal page would be the only Turkish surface in
the product, read by nobody in the US/GB/CA/AU/IE market the app is aimed at, and — per the warning
in `alibi-legal/README.md` — one more thing to drift out of date.

If a second app language ever ships, add the matching language block here in the same release, and
adopt the `.lang-xx` switcher pattern from `alibi-legal` rather than inventing a second one.

## Deployment (GitHub Pages)

1. Push this repository to GitHub
2. **Settings → Pages**
3. Source: `main` branch, `/ (root)` folder
4. Save — the site goes live at:

```
https://<username>.github.io/doseline-legal/
https://<username>.github.io/doseline-legal/privacy.html
https://<username>.github.io/doseline-legal/terms.html
```

Both URLs are required by App Store Connect: the privacy policy URL on the app listing, and a terms
(EULA) URL if the app ever ships a subscription. `STATUS.md` in the app repo lists them as the last
two blocking items before submission.

## Keeping the privacy policy true

This is the part that matters. The policy makes **specific, checkable factual claims** about the
app, and most of them were verified against the source before being written:

| Claim | How it was verified | Breaks if… |
| --- | --- | --- |
| No networking of any kind | `grep` for `fetch(`, `axios`, `XMLHttpRequest`, `WebSocket`, `https://` across `src/` and `app/` returns nothing | any SDK, crash reporter or remote config is added |
| No permissions except notifications | no `UsageDescription` keys in `ios/Doseline/Info.plist` | camera, photos or HealthKit is added — progress photos would do it |
| No analytics, ads, or tracking SDK | `package.json` dependencies | any is added |
| No HealthKit | no HealthKit dependency or entitlement | Apple Health integration is added |
| Exports are temporary and not backed up | `src/features/export/index.ts` writes to `Paths.cache` | the export path moves to Documents |
| Database is covered by device backup | `expo-sqlite` stores at `Documents/SQLite/doseline.db` | the file is excluded from backup, or CloudKit sync ships |
| No in-app purchases yet | `src/features/purchases/` is a manual switch; no RevenueCat | the paid tier ships (PROJECT.md → phase 2) |
| Rating prompt sends no data | `src/features/review/` calls `StoreReview.requestReview()` and nothing else | — |

**CloudKit sync is the one on this list that is actually planned** (`PROJECT.md` → phase 2). When
it ships, section 4 stops being true as written — it currently says plainly that Doseline does not
share your record between devices. Update this document in the same release, not after.

Update the **Effective Date** at the top of a page whenever its substance changes.
