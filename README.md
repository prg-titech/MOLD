# MOLD

MOLD synthesizes updates to mutable data structures from edits to visualized object graphs. Its Rust core derives an assignment sequence from Kanon operation records and delegates expression holes to escher-ts.

## Try the browser demo

Open https://prg-titech.github.io/MOLD/ . The demo uses the Kanon graph editor and runs synthesis in the browser through WebAssembly, a Web Worker, and escher-ts. It does not require a local synthesis server.

## Source layout

- `src/`: Rust analysis and synthesis core. The crate and browser binding retain the internal name `refsyn`.
- `runtime/` and `scripts/run_escher.js`: bridge to escher-ts for native execution.
- `web/scripts/build-wasm.mjs`: Rust-to-WebAssembly build.
- `Kanon/`: the graph editor source used by this demo, adapted from [prg-titech/Kanon](https://github.com/prg-titech/Kanon).
- `Kanon/external/escodegen/`: the escodegen source required by the Kanon build.
- `external/escher-ts/`: the expression synthesis backend source.
- `tests/`, `examples/`, and `docs/evaluation_cases/*/mold_payload.json`: tests and their input fixtures.
- `.github/workflows/pages.yml`: builds and deploys the included sources to GitHub Pages.

MOLD does not include the Escher-Scala implementation. The escher-ts README lists its research references.

## Build locally

Install Rust with the `wasm32-unknown-unknown` target, `wasm-bindgen-cli` version `0.2.114`, Node.js 22, and pnpm 10.30.1. From the MOLD repository root:

    pnpm --dir external/escher-ts install --frozen-lockfile
    pnpm --dir external/escher-ts build
    cargo test
    node web/scripts/build-wasm.mjs
    npm --prefix Kanon install
    REFSYN_DIR="$PWD" npm --prefix Kanon run build-refsyn-runtime
    REFSYN_DIR="$PWD" npm --prefix Kanon run verify-refsyn-browser
    npm --prefix Kanon run build-pages
    python3 -m http.server 8000 --directory Kanon/dist

Open http://localhost:8000/ for the locally built demo. GitHub Pages builds these same sources from the MOLD repository.

## Attribution and reuse

No license has been selected for the MOLD source at this time. The included Kanon and escher-ts sources retain their own license terms; see `Kanon/LICENSE`, `external/escher-ts/package.json`, and `THIRD_PARTY_NOTICES.md`.
