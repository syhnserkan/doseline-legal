# doseline-legal

Legal documents for the **Doseline** mobile application (`com.serkansayhan.doseline`).

Plain static HTML — no build step, no dependencies, no fonts fetched from anywhere. Palette and
type are Doseline's own tokens from `src/theme/index.ts`; the app is light-theme only by
commitment, so these pages are too.

```
index.html     landing page linking to all three
support.html   Support        · the App Store Connect "Support URL"
privacy.html   Privacy Policy · the App Store Connect "Privacy Policy URL"
terms.html     Terms of Service
assets/        app icon at three sizes, generated from doseline/assets/images/icon.png
```

`support.html` leads on the landing page, and the landing page is titled "Support & Legal", because
someone arriving from the App Store's support link wants help rather than law.

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

App Store Connect needs two of these: **Support URL** (`support.html`) and **Privacy Policy URL**
(`privacy.html`), both required on the app listing. A terms/EULA URL becomes required only if the
app ships a subscription. The app itself links to all three from Settings → ABOUT via
`expo-web-browser`, so **these URLs are hardcoded in `app/(tabs)/settings.tsx`** — moving or
renaming a file here breaks a row in a shipped build that cannot be fixed without an app update.

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

Update the **Effective Date** at the top of a page whenever its substance changes. `support.html`
carries no effective date on purpose — it is help, not an agreement, and dating it would imply a
version of it was agreed to.

`support.html` also has to stay true to the app: it names `Settings → Export`, the reminder toggle,
and the erase action by their on-screen positions, and it states plainly that Doseline does not sync
between devices and that CSVs cannot be imported back. All four are things phase 2 could change.
