Recommended permanent changes for the SpotifyPlayStatus library

Goal: make the library SSR-safe and consumer-friendly (do not bundle React, avoid running browser code at import time).

1) package.json
- Move `react` and `react-dom` from devDependencies to peerDependencies:
  "peerDependencies": {
    "react": "^18 || ^19",
    "react-dom": "^18 || ^19"
  }
- Keep them in devDependencies for local testing/building.

2) Rollup (build) config
- Externalize `react` and `react-dom` so they are not bundled. In rollup.config.mjs, add `external: ['react','react-dom']` to the build config.
- Ensure the output formats include ESM and CJS that `main`/`module` point to.

3) Avoid browser APIs at module load
- Move all `window`, `document`, DOM-manipulation, and script insertion code into useEffect callbacks inside components. (The current code already mostly does this, but ensure nothing runs at top-level import.)

4) Exports and types
- Export named and default consistently (e.g., `export { PlayStatus }; export default PlayStatus;`) so both `import PlayStatus from 'spotifyplaystatus'` and `import { PlayStatus } from 'spotifyplaystatus'` work depending on bundler interop.
- Add TypeScript types or a `index.d.ts` with the component's props shape. Or publish @types if preferred.

5) Test
- Add SSR smoke-test using a small Node SSR render with the library imported (without hydration) to confirm there is no runtime browser code executed on import.

If you want, I can:
- Apply these changes to the actual library source in the sibling project if you give me access to that code here (I can modify and rebuild the package, then update the installed tarball).
- Or prepare a PR patch file you can apply to the library repo.
