# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Build Commands

- `pwsh .\build.ps1` - Build all keyboard layouts for x86, x64, and arm64, create distribution archive (winkbdlayouts.zip)
- `pwsh .\install.ps1` - Install built keyboard layouts on current system for testing
- `pwsh .\clean.ps1` - Clean all generated files and build artifacts
- `pwsh .\tools\kbdreverse-test\test.ps1` - Test keyboard reverse-engineering tool
- `python tools\build-unicode-header.py` - Regenerate unicode.h when adding new code points

## Project Architecture

Windows keyboard layout project creating custom DLL files for Apple keyboards and international variants. Each keyboard layout DLL integrates with Windows system settings.

### Core Structure

**Solution**: `winkbdlayouts.sln` - Visual Studio solution containing all keyboards and tools
**Keyboards**: `keyboards/kbd{lang}{variant}/` directories with:
  - `.c` file: scan code mappings, character generation tables
  - `strings.h`: layout description, language identifiers  
  - `.vcxproj`: Visual Studio project file
**Tools**: `tools/` directory containing:
  - `kbdadmin`: Installation/uninstallation utility
  - `kbdreverse`: Reverse-engineer existing keyboard DLLs
  - `scancodes`: Capture keyboard scan codes utility
  - `libtools`: Shared library functions
**Build Output**: `x86/`, `x64/`, `arm64/` directories (not committed)

### Keyboard Data Structures

Each keyboard layout implements Windows `kbd.h` structures:
- Scan code to virtual key mapping (`scancode_to_vk`)
- Modifier definitions (Shift/Ctrl/Alt combinations via `char_modifiers`)
- Character generation tables (`vk_to_wchar`)
- Dead key definitions for accented characters (`dead_keys`)
- Optional ligature definitions

Project includes regular Apple keyboards and "VM" variants for virtual machines where scan codes 0x29/0x56 are swapped.

## Adding New Keyboard Layout

1. Create directory `keyboards/kbd{lang}{variant}/` (e.g., `kbdusapple`)
2. Copy `.vcxproj` from existing keyboard, rename to match directory
3. Either:
   - Copy source files from similar existing keyboard, OR
   - Use `kbdreverse` to extract from installed DLL:
     ```
     kbdreverse fr -o keyboards\kbdXXYYY\kbdXXYYY.c
     kbdreverse fr -r -o keyboards\kbdXXYYY\strings.h
     ```
4. Update `kbdXXYYY.c`:
   - Map scan codes to virtual keys in `scancode_to_vk`
   - Define character generation in `vk_to_wchar` tables
   - Configure dead keys if needed
5. Update `strings.h`:
   - `WKL_TEXT`: Layout description for System Settings
   - `WKL_LANG`: 4-digit hex language ID (e.g., 0x040c for French)
6. Add project to solution in Visual Studio:
   - Right-click solution → Add → Existing Project
   - Select new `.vcxproj` file
7. Update README.md "List of available keyboards" section

## Testing Workflow

1. Build: `pwsh .\build.ps1`
2. Install locally: `pwsh .\install.ps1`  
3. Test keyboard in Windows Settings or use `scancodes.exe` tool
4. For reverse-engineering verification: `pwsh .\tools\kbdreverse-test\test.ps1`

## Key Technical Details

### Virtual Key Codes
- Alphanumeric: Use character literals `'A'`-`'Z'`, `'0'`-`'9'`
- Punctuation: Use `VK_OEM_1` through `VK_OEM_8`
- Special keys: `VK_ESCAPE`, `VK_RETURN`, `VK_SHIFT`, etc.
- Flags: `KBDEXT`, `KBDNUMPAD`, `KBDSPECIAL`

### Modification Numbers
Index positions in character arrays correspond to modifier combinations:
- 0: No modifier
- 1: Shift
- 2: Control  
- 3: Control+Alt (AltGr)
- 4: Shift+Control

### Dead Keys
Define as `WCH_DEAD` in character table, followed by dummy entry with `VK__none_` containing actual dead character.

## Code Style

- 4-space indentation
- Windows line endings (CRLF)
- Wide string literals (`L"..."`) for exported labels
- Align table columns with spaces for readability
- Keep scan code tables sorted numerically