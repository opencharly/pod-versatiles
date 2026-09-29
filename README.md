# pod-versatiles

The `versatiles` candy of the OpenCharly candy library, as a standalone repo
(the candy de-submodule cutover, kind-prefixed naming). It provides the
VersaTiles CLI (versatiles-rs) and a tile server, serving vector tiles over HTTP
on port 8090.

## What it provides

Installs the versatiles-rs single Rust binary at `/usr/local/bin/versatiles`
(`convert` / `serve` / `probe` / `dev` for the `.versatiles`, `.pmtiles`,
`.mbtiles`, and `.tar` tile-container formats), plus a supervisord-managed
wrapper that runs `versatiles serve` on port 8090 (host-mapped 28090).

| Property | Value |
|---|---|
| Requires | `layer-supervisord` |
| Port | `8090` (host-mapped 28090) |
| Service | `versatiles` (`/usr/local/bin/versatiles-wrapper.sh`, `restart: always`, priority 37) |
| Env (provided) | `VERSATILES_PUBLIC_URL` |
| Tile dir | `/workspace/tiles/shortbread/` |

The wrapper always includes the public `download.versatiles.org/osm.versatiles`
source so the service starts cleanly on a fresh deploy before any local
shortbread tiles exist, then appends local `/workspace/tiles/shortbread/`
containers as the OSM DAG produces them. The `convert` subcommand is symmetric
(PMTiles ↔ `.versatiles` ↔ MBTiles round-trip).

Every fact is observable: the installed binary, the linked `convert` subcommand,
the serve wrapper, the running service, the reachable port, and the HTTP 200 a
known OSM tile endpoint returns.

## How to use it

```yaml
my-image:
  candy:
    - '@github.com/opencharly/pod-versatiles:<tag>'
```

Convert between tile containers:

```bash
versatiles convert monaco.pmtiles monaco.versatiles
versatiles convert monaco.versatiles monaco-roundtrip.pmtiles
```

## Verification

The candy's `check:` plan asserts the installed binary, `versatiles --version`,
`versatiles convert --help`, the serve wrapper, and — at deploy scope — the
running service, the reachable port, an HTTP 200 on a known OSM tile endpoint,
and an end-to-end PMTiles round-trip (which SKIPs cleanly when the OSM DAG has
not yet produced `monaco.pmtiles`).

## Layout

- `charly.yml` — the `versatiles:` candy entity (description, `require`,
  `distro`, `port`, `env_provide`, `service`, `plan`) plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-versa:versatiles` — the layer properties, the URL
  surface, the `convert` subcommand, and the supervisord restart race.
- `/charly-versa:shortbread` — produces the PMTiles files versatiles serves.
- `/charly-versa:versatiles-frontend` — the SPA for exploring tile content.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
