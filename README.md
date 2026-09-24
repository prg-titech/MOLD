# MOLD

MOLD synthesizes updates to mutable data structures from edits to visualized object graphs. Its Rust core derives an assignment sequence from Kanon operation records and delegates expression holes to escher-ts.

## Try the browser demo

After the Pages deployment succeeds, open https://prg-titech.github.io/MOLD/ . The demo uses the Kanon graph editor and runs synthesis in the browser through WebAssembly, a Web Worker, and escher-ts. It does not require a local synthesis server.

## Source layout

- `src/`: Rust analysis and synthesis core. The crate and browser binding retain the internal name `refsyn`.
- `runtime/` and `scripts/run_escher.js`: bridge to the escher-ts backend for native execution.
- `web/scripts/build-wasm.mjs`: Rust-to-WebAssembly build.
- `tests/`, `examples/`, and `docs/evaluation_cases/*/mold_payload.json`: tests and the input fixtures used by those tests.
- `.github/workflows/pages.yml`: builds the Kanon-integrated demo and deploys it to GitHub Pages.

The Kanon UI and escher-ts backend are separate public repositories, checked out at fixed commits by the Pages workflow. They are not copied into this repository:

- Kanon fork: https://github.com/ta-yoshi02/Kanon at `82d23adcab8549e11d5e846f0c239c8c90682f8f` (based on https://github.com/prg-titech/Kanon). Kanon is MIT-licensed; see its LICENSE.
- escher-ts: https://github.com/ta-yoshi02/escher-ts at `e187df0795af897ba208f8e2e7b801236e045c86`. Its package metadata declares ISC.

MOLD does not include the Escher-Scala source. escher-ts implements ideas from Escher and AscendRec; see its README for the research references.

## Build locally

Install Rust with the `wasm32-unknown-unknown` target, `wasm-bindgen-cli` version `0.2.114`, Node.js 22, and pnpm 10.30.1. From the MOLD repository root:

    git clone https://github.com/ta-yoshi02/escher-ts.git external/escher-ts
    git -C external/escher-ts checkout e187df0795af897ba208f8e2e7b801236e045c86
    pnpm --dir external/escher-ts install --frozen-lockfile
    pnpm --dir external/escher-ts build
    cargo test
    node web/scripts/build-wasm.mjs
    git clone --recurse-submodules https://github.com/ta-yoshi02/Kanon.git Kanon
    git -C Kanon checkout 82d23adcab8549e11d5e846f0c239c8c90682f8f
    npm --prefix Kanon install
    REFSYN_DIR="$PWD" npm --prefix Kanon run build-refsyn-runtime
    REFSYN_DIR="$PWD" npm --prefix Kanon run verify-refsyn-browser
    npm --prefix Kanon run build-pages
    python3 -m http.server 8000 --directory Kanon/dist

Open http://localhost:8000/ for the locally built demo. The deployed Pages site is built from the same fixed dependency commits. The workflow records the resolved commits in its run summary.

## Attribution

No license has been selected for the MOLD source at this time. Kanon and escher-ts keep their own license terms in their repositories; they are dependencies, not part of the MOLD source tree.
