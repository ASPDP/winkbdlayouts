# Repository Guidelines

## Project Structure & Module Organization
- `winkbdlayouts.sln` collects keyboard layouts and helper tools; open it in Visual Studio for multi-architecture builds.
- Layouts live in `keyboards/kbdXXname/` with matching `.c` and `strings.h` files; shared resources sit beside them in `kbd.rc`, `kbdrc.h`, and `unicode.h`.
- Tooling code (e.g., `kbdreverse`, `kbdadmin`, `winutils`) is under `tools/`; run `tools/kbdreverse-test` for reverse-engineering checks and `build-unicode-header.py` when updating Unicode pages.
- Generated binaries and installers stage into `x86`, `x64`, and `arm64`; do not commit contents of these folders.
- `images/` stores documentation assets that surface in the README and release notes.

## Build, Test, and Development Commands
- `pwsh .\build.ps1` builds every project, signs outputs, and recreates `winkbdlayouts.zip`.
- `pwsh .\install.ps1` installs the freshly built layouts on the local machine for smoke-testing.
- `pwsh .\clean.ps1` removes generated binaries and intermediate project files.
- `pwsh .\tools\kbdreverse-test\test.ps1` rebuilds `kbdreverse`, regenerates a sample layout, and compiles the verification harness.

## Coding Style & Naming Conventions
- C/C++ sources use 4-space indentation, Windows line endings, and wide-string literals (`L"..."`) for exported labels.
- New layout folders should follow `kbd<language><variant>` (e.g., `keyboards\kbdusapple\`) and keep file names aligned with the folder.
- Keep tables sorted by scan code and pad columns with spaces to match the existing visual grid.
- Regenerate `unicode.h` via `python tools/build-unicode-header.py` when adding code points.

## Testing Guidelines
- Run `pwsh .\build.ps1` before raising a PR; ensure Release builds succeed for x86, x64, and arm64.
- Execute `pwsh .\tools\kbdreverse-test\test.ps1` when modifying reverse tooling or Unicode tables; inspect the generated `kbdtest.c`.
- Manually install and validate new layouts with `install.ps1`, confirming key outputs on hardware or VM keyboard viewer.
- Capture observed issues as GitHub discussions before altering shared headers.

## Commit & Pull Request Guidelines
- Follow the short, descriptive style used in history (e.g., `Added Polish Layout for JIS keyboards`); prefer imperative verbs when adding code.
- Reference issue numbers with `(#id)` when applicable and group related layout changes in a single commit.
- PRs should summarize the layout or tool change, list the architectures built, and attach screenshots or key maps if user-facing behaviour shifts.
- Update `README.md` sections such as "List of available keyboards" or installation notes whenever assets change.

## Layout Contribution Checklist
- Duplicate `kbdXXYYY.vcxproj`, update `winkbdlayouts.sln`, and register the layout in the README.
- Verify locale IDs, dead-key tables, and ligatures match upstream references before submitting.
