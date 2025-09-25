# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

- `pwsh .\build.ps1` - Build all keyboard layouts and tools for x86, x64, and arm64 architectures, then create distribution archive
- `pwsh .\install.ps1` - Install built keyboard layouts on the current system for testing
- `pwsh .\clean.ps1` - Clean all generated files and build artifacts
- `pwsh .\tools\kbdreverse-test\test.ps1` - Test the keyboard reverse-engineering tool

## Project Architecture

This is a Windows keyboard layout project that creates custom keyboard DLL files for various Apple keyboard layouts and international variants. The solution builds keyboard layout DLL files that integrate with Windows system settings.

### Key Components

- **Solution File**: `winkbdlayouts.sln` - Visual Studio solution containing all keyboard layouts and tools
- **Keyboard Layouts**: Located in `keyboards/kbd{language}{variant}/` directories, each containing:
  - `.c` source file with scan code mappings and character generation tables
  - `strings.h` with layout description and language identifiers
  - `.vcxproj` Visual Studio project file
- **Tools**: Located in `tools/` directory:
  - `kbdadmin` - Installation/uninstallation utility
  - `kbdreverse` - Tool to reverse-engineer existing keyboard DLL files
  - `scancodes` - Utility to capture keyboard scan codes
  - `libtools` - Shared library for common functions
- **Build Outputs**: Generated in `x86/`, `x64/`, and `arm64/` directories (not committed)

### Keyboard Layout Structure

Each keyboard layout consists of several key data structures defined in standard Windows `kbd.h`:
- Scan code to virtual key mapping tables
- Modifier definitions (Shift, Ctrl, Alt/AltGr combinations)
- Character generation tables with multi-level character support
- Dead key definitions for accented characters
- Optional ligature definitions

The project includes both regular Apple keyboards and "VM" variants for virtual machine environments where certain scan codes are swapped.

## Development Workflow

1. To add a new keyboard layout, copy an existing similar layout from `keyboards/`
2. Update the `.c` file with appropriate scan code mappings and character tables
3. Modify `strings.h` with correct language description and language ID
4. Add the new `.vcxproj` to `winkbdlayouts.sln` in Visual Studio
5. Build and test using the provided PowerShell scripts

The project follows Windows keyboard layout conventions with 4-space indentation and wide-string literals for exported labels.