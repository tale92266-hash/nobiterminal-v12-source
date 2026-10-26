# NobiTerminal v12 — full source snapshot

This repo is the **complete patched app source tree for NobiTerminal v12**
(the version shipped as the v12 APK on 2026-10-26). It exists so the app's
source can be browsed, understood, and edited by hand in one place.

> This snapshot is for viewing and editing. It is **not** the build route.
> The real build route is the generator repo
> (`tale92266-hash/nobiterminal`): it clones upstream, applies the Nobi
> patches, renames the package, builds the bootstrap and the app in GitHub
> Actions, and produces the APK. Edits made here do **not** flow into
> generator builds automatically — to ship an edit, it must become a patch
> in the generator repo.

## What this snapshot was made from

- **Base:** upstream `termux/termux-app`
  `@8629e632fcb95da272221be327db653fb24befe9` (2026-09-26).
- **Patches:** all 25 Nobi patches from generator branch `v12-batch`
  `@6b772019ffa4d5739beeb293fa32a075fcf9772b`, applied with GNU
  `patch -p1` in plain lexical order (the same order the generator uses).
  All 25 applied cleanly: 0 `.rej`, 0 `.orig` files.
- **Shipped v12 APK for cross-reference:** 35,526,584 bytes,
  SHA-256 `7f626c04bfce745d8a76955a45d91921d40a2c1fdf74c3a4269114e8114dcf1c`,
  generator run `40270888811` (cache HIT build, 11 min 6 s), signer
  SHA-256 fingerprint
  `B6:DA:01:48:0E:EF:D5:FB:F2:CD:37:71:B8:D1:02:1E:C7:91:30:4B:DD:6C:4B:F4:1D:3F:AA:BA:D4:8E:E5:E1`.

### The 25 patches, in apply order

1. `nobiterminal-app-name.patch`
2. `nobiterminal-drawer-default.patch`
3. `nobiterminal-neon-default.patch`
4. `nobiterminal-settings-suite.patch`
5. `nobiterminal-tabs-bar.patch`
6. `nobiterminal-v5-core.patch`
7. `nobiterminal-v5-drawer.patch`
8. `nobiterminal-v5-icon-splash.patch`
9. `nobiterminal-v5-nobicode-icons-a.patch`
10. `nobiterminal-v5-nobicode-icons-b.patch`
11. `nobiterminal-v5-nobicode-res.patch`
12. `nobiterminal-v5-nobicode-src.patch`
13. `nobiterminal-v5-tabs-rename.patch`
14. `nobiterminal-v6-batch.patch`
15. `nobiterminal-v7-batch.patch`
16. `nobiterminal-v7-fix.patch`
17. `nobiterminal-v7-fix2.patch`
18. `nobiterminal-v8-extrakeys.patch`
19. `nobiterminal-v9-drawer-notify.patch`
20. `nobiterminal-w10-editor-permissions.patch`
21. `nobiterminal-w11-followup.patch`
22. `nobiterminal-w12-fix-duplicate-permissions.patch`
23. `nobiterminal-w13-fix-vectors-kotlin.patch`
24. `nobiterminal-w14-autorepo.patch`
25. `nobiterminal-w15-keys-settings-theme.patch`

### What is new in v12 (patch 25, `w15`)

- **Extra-keys press feedback** (`ExtraKeysView.java`): every key button
  now gets an explicit initial background in `reload()` (previously never
  set, so buttons were transparent and the bar's black/navy background
  showed through), and pressing a key now changes **both** its background
  and its text color to the codebase's existing active colors
  (grey_500 background + red_400 text), restoring both on release.
- **Settings theme follows the terminal theme** (both Settings
  activities): before `setContentView`, when the Nobi time-theme
  preference is ON, the local night mode is derived from the applied Nobi
  theme (daylight → light, dark → dark); when the preference is OFF,
  upstream behavior is unchanged. An open Settings screen re-applies on
  resume when the theme changed.
- **Honest caveat on the Settings bridge:** the NobiCode editor's
  Settings theme parent is fixed dark (`Theme.Material3.Dark.NoActionBar`,
  no `values-night`), so the bridge there is currently a harmless no-op
  with no visible effect; the visible effect is on the upstream Settings
  screen (DayNight theme). Making the editor Settings follow the theme
  visibly needs its theme to become DayNight first.

## Package name: this tree is `com.termux`, not `com.nobiterminal`

That is intentional and matches what the generator patches produce. The
`com.nobiterminal` rename happens **at generator build time, after
patching**, via `TERMUX_APP__PACKAGE_NAME=com.nobiterminal`
(`replace_termux_name` + `migrate_termux_folder_tree` in the generator
scripts): it rewrites `com.termux` → `com.nobiterminal` in text files and
moves the `com/termux/...` Java folders. So:

- If you edit this tree, edit the `com.termux` paths. Do not rename
  things to `com.nobiterminal` here by hand.
- Building this tree directly produces a `com.termux` app. To get a
  `com.nobiterminal` build, build through the generator.

## Signing: local debug builds use the same certificate as the shipped APK

`app/testkey_untrusted.jks` (the upstream debug keystore) is included in
this tree. Its certificate was verified with `keytool`: Owner
`CN=APK Signer, OU=Earth, O=Earth`, SHA-256 fingerprint
`B6:DA:01:48:0E:EF:D5:FB:F2:CD:37:71:B8:D1:02:1E:C7:91:30:4B:DD:6C:4B:F4:1D:3F:AA:BA:D4:8E:E5:E1`
— identical to the shipped v12 APK's signer. A debug build from this tree
therefore signs with the same certificate and installs over the shipped
v12 build. (This is the upstream-published debug keystore, not a private
release key.)

## What is NOT in this snapshot

- **Bootstrap files.** There is no `app/src/main/assets/` here. In CI the
  bootstrap (`bootstrap-aarch64.tar.xz` + `xz` libs) is built from
  `termux/termux-packages`
  `@14ef1032809e1d885bf120b1ee03d751e3727325` (2026-10-26, the same HEAD
  as the v11 seed — the v12 build was a cache HIT on the v11 seed key)
  and placed into the app's assets by the generator build. A local build
  from this tree needs bootstrap files supplied separately.
- **Generator CI files.** The generator's workflows, cache system and
  scripts live in the generator repo, not here. The `.github/` folder in
  this tree is upstream Termux's own CI, not NobiTerminal's build.
- **Binary patch hunks.** GNU `patch` cannot apply git binary hunks, so
  any binary file a patch tried to add is absent here — exactly as in
  the generator CI tree (CI uses the same GNU `patch -p1`). The legacy
  `mipmap` PNGs present here are upstream's, not Nobi's.

## Building on a phone (ACS): read this first

This snapshot's `main` branch is **not directly buildable in ACS
(AndroidIDE) as-is**. Three modules wire `externalNativeBuild` ndkBuild
in this tree:

| Module | Native lib |
|---|---|
| `app` | `libtermux-bootstrap` (`app/src/main/cpp/Android.mk`) |
| `terminal-emulator` | `libtermux` (`terminal-emulator/src/main/jni/Android.mk`) |
| `termux-shared` | `liblocal-socket` (`termux-shared/src/main/cpp/Android.mk`) |

Phone IDEs cannot run Google NDK's inner `ndk-build` (on v4 this failed
with exit 126 on `termux-shared`). v4 solved it with a separate
`acs-prebuilt` branch: prebuilt `.so` files taken from the shipped APK
plus the `externalNativeBuild` blocks removed, with `main` untouched.
**No v12 `acs-prebuilt` branch was created in this snapshot** — that is a
separate next step if on-phone ACS builds are wanted. Two facts for that
step: the shipped v12 APK contains only `libtermux.so` and
`liblocal-socket.so` (× 4 ABIs each) — it contains **no**
`libtermux-bootstrap.so`, even though the `app` module wires ndkBuild in
this tree, so the `app` module's ndk step needs investigation before any
`acs-prebuilt` treatment. The Gradle wrapper jar
(`gradle/wrapper/gradle-wrapper.jar`) **is** included in this tree, so an
ACS import does not hang on a missing wrapper jar.

## Modules in this tree

`app`, `termux-shared`, `terminal-emulator`, `terminal-view`, plus
`nobicode` (the inbuilt editor, added by the v5 patches and extended by
later batches, including the v11 follow-up features, auto repo setup in
`app/src/main/java/com/termux/app/nobi/NobiRepoSetup.java`, and the v12
keys/theme fixes).

## License

Upstream Termux code is GPL-3.0 (see `LICENSE.md`). The Nobi patches on
top are Sam's own changes.
