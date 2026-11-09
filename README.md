# OPUS Plugin Repository

The app store that OPUS reads on launch.

OPUS fetches **`catalog.json`** from this repository (branch `main`):

```
https://raw.githubusercontent.com/Denver062/OPUS-plugin/main/catalog.json
```

Every entry in the catalog can be installed from inside OPUS. OPUS downloads
`download_url`, unpacks the zip into `%APPDATA%\OPUS\apps\<id>\`, reads
`opus-app.json`, and then runs the app from there. Nothing is shipped inside the
OPUS executable.

## Layout

```
catalog.json                     # the list OPUS downloads
apps/<id>/opus-app.json          # published manifest of each app (reference copy)
packages/<id>-<version>.zip      # downloadable package: opus-app.json + entry exe
```

## Package rules

A zip is unpacked straight into `%APPDATA%\OPUS\apps\<id>\`, so its contents
must sit at the **root** of the zip:

```
schun-aod-1.0.0.zip
  opus-app.json
  schun.scr
```

* `opus-app.json` must declare `id`, `name`, and `entry`.
* `entry` is relative to the app folder. It is used for `commands.start`,
  `commands.install`, and `commands.uninstall` defaults.
* Settings are declared in `settings.fields` and rendered by OPUS. See the
  [app SDK](https://github.com/Denver062/OPUS#app-development-guide).

## Adding an app

1. Drop the app folder under `apps/<id>/` and the zip under
   `packages/<id>-<version>.zip`.
2. Add an entry to `catalog.json`:

```json
{
  "id": "my-app",
  "name": "My App",
  "description": "What it does",
  "version": "1.0.0",
  "author": "You",
  "category": "utility",
  "repo_url": "https://github.com/you/my-app",
  "download_url": "https://raw.githubusercontent.com/Denver062/OPUS-plugin/main/packages/my-app-1.0.0.zip"
}
```

3. Commit and push to `main`. OPUS picks the change up the next time the
   Download tab is opened.

## Releasing a new version

Keep the zip URL versioned (`my-app-1.1.0.zip`) and bump both `version` and
`download_url` in `catalog.json`. OPUS compares the catalog version against the
installed manifest version and shows an **Update** button when they differ.

In the OPUS source tree the whole flow is automated:

```powershell
powershell -ExecutionPolicy Bypass -File publish-plugin.ps1
```
