# Candidate Report: Windows Compatibility Fixes

## 1. Investigation and Findings

### Lint Command Syntax Issue

- **Symptom**: On Windows, running `pnpm lint` failed with the error: `'ESLINT_USE_FLAT_CONFIG' is not recognized as an internal or external command`.
- **Cause**: In `apps/web/package.json`, the lint script set `ESLINT_USE_FLAT_CONFIG=false` inline before calling `eslint`. This inline environment variable syntax is only supported on Unix-like shells (bash, zsh).
- **Fix**: Added `cross-env` to handle cross-platform environment variable configuration.

### Build Symlink EPERM Issue

- **Symptom**: Running `pnpm build` failed on Windows with `EPERM: operation not permitted, symlink ...`.
- **Cause**: Next.js standalone output tracing attempts to create symlinks under `node_modules` inside the `.next/standalone` folder. On Windows, symlink creation requires elevated administrator privileges or Developer Mode enabled, failing for standard developers/CI pipelines running on Windows.
- **Fix**: Updated `apps/web/next.config.ts` to disable standalone output conditionally when running on Windows (`process.platform === 'win32'`), while keeping it active for production Linux Docker/CI builds.

---

## 2. Verification Steps

The following check commands were executed and passed successfully:

1. **Linting**:

   ```bash
   pnpm lint
   ```

   _Outcome_: Success. Both workspaces (`apps/api` and `apps/web`) completed linting without errors.

2. **Typechecking**:

   ```bash
   pnpm typecheck
   ```

   _Outcome_: Success. `tsc --noEmit` completed without issues in both workspaces.

3. **Testing**:

   ```bash
   pnpm test
   ```

   _Outcome_: Success. Existing vitest tests ran and passed.

4. **Production Build**:
   ```bash
   pnpm build
   ```
   _Outcome_: Success. Both API typescript build and Next.js production build completed without errors.

---

## 3. Risks & Impact Assessment

- **Risk**: Low. Disabling standalone output on Windows only affects local builds on Windows machines. Docker/production environments running on Linux remain unaffected and will build in standalone mode as configured.
