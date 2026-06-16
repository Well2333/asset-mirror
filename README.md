# asset-mirror

A small, **auto-updated mirror of third-party release binaries**, committed into git so
they can be delivered through the [jsDelivr](https://www.jsdelivr.com/) CDN — which serves a
repo's git-tree files (but *not* GitHub Release assets, and not files over 50 MB).

This exists so tools that need to download release binaries from `github.com` still work in
networks where GitHub is blocked or slow: jsDelivr is a stable, globally-cached CDN.

## How it works

A scheduled GitHub Action (`.github/workflows/mirror.yml`) runs **once a day**:

1. checks each tracked project for a new upstream release;
2. if there is one, downloads the needed assets, **gzip-compresses** them (so even large
   binaries fit under jsDelivr's 50 MB per-file limit) and commits them here;
3. records the mirrored version in `<dir>/VERSION`.

It is idempotent — if the upstream version already matches `VERSION`, nothing changes.
You can also trigger it manually from the Actions tab (*Run workflow*).

## Mirrored resources

### cloudflared (`cloudflared/`)

Latest [`cloudflare/cloudflared`](https://github.com/cloudflare/cloudflared) release binaries,
each stored gzip-compressed as `<asset>.gz`. Fetch + decompress, e.g.:

```
https://cdn.jsdelivr.net/gh/Well2333/asset-mirror@main/cloudflared/cloudflared-linux-amd64.gz
https://cdn.jsdelivr.net/gh/Well2333/asset-mirror@main/cloudflared/cloudflared-windows-amd64.exe.gz
```

Used by the Unturned **well404.WebPanel** plugin as a download mirror for cloudflared
(it gunzips after download). Current version: see [`cloudflared/VERSION`](cloudflared/VERSION).

## Adding another resource

Add a step/job to `mirror.yml` mirroring the new project's assets into its own folder
(same pattern: resolve latest tag → download → `gzip -9` → commit → write a `VERSION`).
