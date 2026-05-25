# AetherPak Actions — Demo

A minimal, working showcase of the [AetherPak Actions](https://github.com/aetherpak/actions)
happy path: build a Flatpak from a manifest, publish it as an OCI image on GHCR,
and serve a signed Flatpak repository and landing page from GitHub Pages.

The app is [GNOME Sudoku](https://apps.gnome.org/Sudoku/) (`org.gnome.Sudoku`),
built for `x86_64` and `aarch64` on the `stable` channel.

## How it works

[`.github/workflows/publish.yml`](.github/workflows/publish.yml) calls the shared
reusable workflow and points it at the manifest:

```yaml
jobs:
  publish:
    uses: aetherpak/actions/.github/workflows/publish.yml@v1
    with:
      manifest-path: org.gnome.Sudoku.json
      runtime: gnome-50
      branch: stable
      run-linter: false
    secrets:
      gpg-private-key: ${{ secrets.AETHERPAK_GPG_KEY }}
```

On every push to `main` the workflow builds the manifest, pushes the image to
GHCR, and deploys the index + landing page to Pages. Because `GPG_PRIVATE_KEY` is
set, images are GPG-signed (the `signing` input defaults to `auto`), so installs
are verified.

## Install

Landing page: **https://aetherpak.github.io/actions-demo/**

The page lists GNOME Sudoku with an **Install** button (a verified one-click
`.flatpakref`) and prints the exact CLI commands for this repository. From the
command line, with verification:

```bash
curl -fsSLO https://aetherpak.github.io/actions-demo/sigs/key.asc
flatpak remote-add --user \
  --gpg-import=key.asc \
  --signature-lookaside=https://aetherpak.github.io/actions-demo/sigs \
  actions-demo oci+https://aetherpak.github.io/actions-demo
flatpak install --user actions-demo org.gnome.Sudoku
```

## Reproduce it

1. Add a manifest and a `publish.yml` like the one above.
2. Settings → Pages → Source: **GitHub Actions**.
3. For an org, allow public packages first: **Organization → Settings → Packages
   → Package creation → Public**.
4. (Optional) Add a `AETHERPAK_GPG_KEY` secret to enable signing.
5. Push to `main`; after the first run, set the GHCR package to public.

See the [AetherPak Actions README](https://github.com/aetherpak/actions) for the
full option list.
