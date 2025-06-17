# indentChecker

## Overview
indentChecker is a cross-platform console utility for validating indentation consistency in source code files across multiple directories. It helps maintain clean, consistent code by detecting and reporting indentation issues.

## Features
- Recursive directory scanning
- Multi-encoding support (UTF-8, Shift_JIS, etc.)
- Configurable indentation rules (strict or flexible)
- Customizable ignore patterns for files, names, and extensions
- Real-time colored console feedback
- Detailed report generation
- No database or network required

## How It Works
1. Reads configuration files to determine scan roots, ignore rules, encodings, and indentation modes.
2. Recursively scans specified directories, applying ignore patterns.
3. Checks each file for indentation issues:
   - Only ASCII spaces allowed for indentation
   - Minimum indentation of 4 spaces
   - Strict mode: indentation must be a multiple of 4
   - Flexible mode: any indentation ≥4 spaces
4. Reports issues in the console and writes a detailed report to your desktop.

## Configuration
indentChecker uses simple text and JSON files for configuration:
- `scanRoots.txt`: Directories to scan
- `ignoredFullPaths.txt`, `ignoredNames.txt`, `ignoredExtensions.txt`: Ignore rules
- `encodingMap.json`: File encoding mappings
- `indentModeMap.json`: Indentation mode mappings

## Output
- Console: Real-time, color-coded feedback
- Report: Detailed log file saved to your desktop

## License
GPL-3.0

---
For more details, see the full specifications in `specs/indentChecker-specs-v0.1.md`.