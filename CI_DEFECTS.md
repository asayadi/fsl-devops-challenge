# CI Pipeline — Diagnosed Defects

Seeded defects found in `.github/workflows/ci.yaml`:

## Defect 1 — Wrong Node.js version
- **Location:** All jobs, `node-version: '14'`
- **Root cause:** `package.json` `engines` field requires `node >=15.0.0 <16.0.0`. Node 14 will fail on install or build.
- **Fix:** Change all `node-version` values from `'14'` to `'15'`.

## Defect 2 — Branch trigger pattern uses hyphen
- **Location:** `on.push.branches`, value `feature-*`
- **Root cause:** Branching strategy uses `feature/<name>` (slash), not `feature-<name>` (hyphen). The pattern never matches real feature branches.
- **Fix:** Change `feature-*` to `feature/**`.

## Defect 3 — PR trigger missing `devel` branch
- **Location:** `on.pull_request.branches`, only contains `main`
- **Root cause:** All feature/bugfix work flows into `devel` via PR. CI never fires on those PRs.
- **Fix:** Add `devel` (and `stage`) to the `pull_request.branches` list.

## Defect 4 — `build` job does not depend on `test`
- **Location:** `build.needs: [lint]`
- **Root cause:** Build can run and pass even when tests fail, breaking the required sequential gate.
- **Fix:** Change to `needs: [lint, test]`.

## Defect 5 — Cache key mismatch in `build` job
- **Location:** `build` job, `actions/cache/restore`, key `deps-${{ hashFiles('package-lock.json') }}`
- **Root cause:** The `install` job saves cache with key `node-modules-${{...}}`. The `build` job looks for `deps-${{...}}`. Cache never hits, `node_modules` is empty, build fails.
- **Fix:** Change the restore key in `build` to `node-modules-${{ hashFiles('package-lock.json') }}`.
