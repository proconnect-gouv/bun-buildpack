# bun-buildpack

Scalingo buildpack for [Bun](https://bun.sh) apps.

## Detection

The buildpack activates when a `bun.lockb` or `bun.lock` file is present at the root of the app.

## Bun version resolution

By default, if no version is specified, the buildpack will:

1. Check `package.json` for the `packageManager` field (e.g. `"packageManager": "bun@1.1.0"`)
2. If `packageManager` doesn't exist, check `package.json` for `engines.bun`
3. If neither exists or `package.json` is not found, use `latest`

### Pin via `packageManager` (recommended)

```json
{
  "packageManager": "bun@1.1.0"
}
```

### Pin via `engines`

```json
{
  "engines": {
    "bun": "1.1.0"
  }
}
```

> **Warning:** Not pinning a version means the buildpack will install the latest bun at build time, which may cause non-reproducible builds.

## Node.js

If your `package.json` includes an `engines.node` field, the buildpack will also install Node.js at that version using [n](https://github.com/tj/n):

```json
{
  "engines": {
    "node": "20.x"
  }
}
```

## Build steps

1. Installs Node.js (if `engines.node` is set)
2. Installs Bun
3. Runs `bun install --frozen-lockfile`
4. Runs `bun run build`
5. Removes dev dependencies
6. Runs `bun install --frozen-lockfile --production`
7. Clears the bun package cache

## Usage on Scalingo

Set this buildpack in your `scalingo.json` or via the Scalingo dashboard:

```json
{
  "stack": "scalingo-24",
  "buildpacks": [
    {
      "url": "https://github.com/proconnect-gouv/bun-buildpack"
    }
  ]
}
```

Or via CLI:

```bash
scalingo env-set BUILDPACK_URL=https://github.com/proconnect-gouv/bun-buildpack
```

## Inspired by

- [confact/bun-buildpack](https://github.com/confact/bun-buildpack)
- [proconnect-gouv/hyyypertool scalingo/buildpack](https://github.com/proconnect-gouv/hyyypertool/tree/scalingo/buildpack)
- [oven-sh/setup-bun](https://github.com/oven-sh/setup-bun)
