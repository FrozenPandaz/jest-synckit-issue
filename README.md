# Jest v30 + synckit@0.11.10 Compatibility Issue Reproduction

This repository demonstrates a compatibility issue between Jest v30 and synckit@0.11.10.

## Issue Summary

synckit@0.11.10 (released July 18, 2025) introduced a breaking change that causes Jest v30 tests to fail with a `createRequire` TypeError.

## Error

```
TypeError: The argument 'filename' must be a file URL object, file URL string, or absolute path string. Received 'http://localhost/index.cjs'
    at Function.createRequire (node_modules/jest-runtime/build/index.js:1407:23)
    at Object.<anonymous> (node_modules/@pkgr/core/lib/index.cjs:10:48)
    at Object.<anonymous> (node_modules/synckit/lib/index.cjs:30:29)
```

## Root Cause

1. **June 1, 2025**: `@pkgr/core@0.3.0` introduced breaking changes with Jest's `createRequire` implementation
2. **July 18, 2025**: `synckit@0.11.10` updated its dependency range to allow `@pkgr/core@0.3.x`
3. **Dependency chain**: Jest → jest-snapshot → synckit → @pkgr/core

## Reproduction Steps

### To see the failure:
1. Comment out or remove the pnpm override from `package.json`
2. Delete `pnpm-lock.yaml`
3. Run `pnpm install`
4. Run `nx test test` or `jest` - tests will fail

### To see it working:
1. Keep the pnpm override in `package.json`: `"synckit": "0.11.9"`
2. Run `pnpm install`
3. Run `nx test test` or `jest` - tests pass

## Current Workaround

The `package.json` includes a pnpm override to pin synckit to the last working version:

```json
{
  "pnpm": {
    "overrides": {
      "synckit": "0.11.9"
    }
  }
}
```

## Environment

- **synckit**: 0.11.10 (broken), 0.11.9 (works)
- **Jest**: 30.0.2
- **NX**: 21.3.0
- **Node.js**: Latest
- **Package manager**: pnpm (affects all package managers)

## Alternative Workarounds

You can also pin the deeper dependency:

```json
{
  "pnpm": {
    "overrides": {
      "@pkgr/core": "0.2.9"
    }
  }
}
```

## Related Links

- [synckit repository](https://github.com/un-ts/synckit)
- [Jest createRequire issues](https://github.com/jestjs/jest/issues/9426)
- [This repository](https://github.com/FrozenPandaz/jest-synckit-issue)