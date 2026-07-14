# Summary of Changes

To run this repository on Windows, several cross-platform compatibility issues were resolved. Here is the log of what changed:

## 1. Environment Variable Syntax in Lint Script

- **File Modified**: [apps/web/package.json](file:///c:/Users/BASIT/Desktop/Cipher/FSD-Assignment/apps/web/package.json)
- **Problem**: Setting `ESLINT_USE_FLAT_CONFIG=false` inline before a command is Unix/macOS syntax and fails on Windows with the error: `'ESLINT_USE_FLAT_CONFIG' is not recognized as an internal or external command`.
- **Fix**:
  - Installed `cross-env` as a devDependency in `@bugforge/web`.
  - Prepend the command with `cross-env`: `"lint": "cross-env ESLINT_USE_FLAT_CONFIG=false eslint . --ext .ts,.tsx --max-warnings=0"`.

## 2. Next.js Standalone Output Symlink Error (Windows EPERM)

- **File Modified**: [apps/web/next.config.ts](file:///c:/Users/BASIT/Desktop/Cipher/FSD-Assignment/apps/web/next.config.ts)
- **Problem**: Next.js building with `{ output: 'standalone' }` attempts to create symlinks in `node_modules` during tracing. On Windows, creating symlinks requires Administrator privileges or Developer Mode, causing `EPERM: operation not permitted` errors.
- **Fix**:
  - Conditionally disabled standalone output when running locally on Windows: `output: process.platform === 'win32' ? undefined : 'standalone'`.
  - This keeps production Docker builds (which run on Linux) fully functional, while allowing local Windows verification/building to succeed without elevated permissions.

---

## Verification Results

- **pnpm lint**: Passed successfully.
- **pnpm test**: Passed successfully (all API tests passed).
- **pnpm build**: Passed successfully without permission errors.
