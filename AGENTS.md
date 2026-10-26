# one-click-public-app: agent guide

This repo hosts the One Click Android APK for public download. It has no source code. Each APK goes on a GitHub Release, never into git.

The landing site (`taghyeer/one-click.landing`, file `app/download/page.tsx`) links to this stable URL:

```
https://github.com/taghyeer/one-click-public-app/releases/latest/download/one-click.apk
```

`releases/latest/download/<name>` serves the asset called `<name>` from the release marked **Latest**. If you publish a new release with an asset named exactly `one-click.apk`, the landing link serves it automatically. The landing code does not need to change.

## Auth

On this machine the active gh account may be the personal one, which gets 404 on `taghyeer` repos. Start every shell session with:

```bash
export GH_TOKEN=$(gh auth token -u salehshakib-taghyeer)
```

## Publish a new version

1. Get the APK path and the app version (`versionName`) from the user. Ask if either is missing. Do not guess the version.
2. Copy the APK into a temp folder, named exactly `one-click.apk`. The upload uses the file name as the asset name.
3. Check that the tag does not already exist:
   ```bash
   gh release list --repo taghyeer/one-click-public-app
   ```
4. Create the release. Tag format: `v<versionName>`, for example `v1.0.1`.
   ```bash
   gh release create v1.0.1 /path/to/temp/one-click.apk \
     --repo taghyeer/one-click-public-app \
     --title "v1.0.1" \
     --notes "<what changed, from the user>" \
     --latest
   ```
5. Verify that the stable link serves the new file. The last `HTTP` line must be 200, and `content-length` must equal the local file size in bytes.
   ```bash
   curl -sIL https://github.com/taghyeer/one-click-public-app/releases/latest/download/one-click.apk | grep -iE '^(HTTP|content-length)'
   ```
6. The landing page shows the label `Version 1.0 · 75 MB · Android 7.0+` in `app/download/page.tsx`. If the version or size changed, tell the user. Change that text only if they ask.

## Replace the APK on an existing version (rebuild, same version)

```bash
gh release upload v1.0.1 /path/to/temp/one-click.apk --repo taghyeer/one-click-public-app --clobber
```

## Roll back

Mark an older release as Latest. The stable link switches to it immediately.

```bash
gh release edit v1.0.0 --repo taghyeer/one-click-public-app --latest
```

## Rules

- Never commit APK files to git. Releases only.
- Always name the asset `one-click.apk`. Any other name breaks the landing link.
- Do not delete the Latest release unless you have published a replacement first.
- Keep the repo public. Assets in a private repo need a login to download.
- GitHub limits each release asset to 2 GiB.
