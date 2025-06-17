# indentChecker Application Specifications v0.1

## Table of Contents
1. [Overview](#overview)
2. [System Requirements](#system-requirements)
3. [Architecture](#architecture)
4. [Core Components](#core-components)
5. [Configuration System](#configuration-system)
6. [Indentation Validation Rules](#indentation-validation-rules)
7. [File Processing Workflow](#file-processing-workflow)
8. [Output and Reporting](#output-and-reporting)
9. [Error Handling](#error-handling)
10. [Extensibility](#extensibility)
11. [Usage Examples](#usage-examples)
12. [Technical Implementation Details](#technical-implementation-details)

## Overview

**indentChecker** is a console utility application designed to validate and check indentation consistency in source code files across multiple directories and file types. The application provides comprehensive scanning capabilities with configurable rules for different file types and encoding support.

### Key Features
- Recursive directory scanning with configurable root paths
- Multi-encoding support (UTF-8, Shift_JIS, etc.)
- Flexible indentation validation modes (Strict/Flex)
- Comprehensive ignore patterns (paths, names, extensions)
- Detailed reporting with statistics
- Colored console output for better visibility
- Cross-platform compatibility (.NET 9.0)
- Only ASCII spaces (U+0020) are allowed for indentation; tabs and other whitespace are flagged as errors
- All configuration and processing errors are aggregated and reported

### Project Metadata
- **Version**: 0.1
- **Target Framework**: .NET 9.0
- **License**: GPL-3.0
- **Author**: nao7sep
- **Company**: Purrfect Code
- **Repository**: https://github.com/nao7sep/indentChecker

## System Requirements

### Runtime Requirements
- .NET 9.0 Runtime
- Windows, macOS, or Linux operating system
- Sufficient disk space for report generation
- Read access to target directories

### Development Requirements
- .NET 9.0 SDK
- C# 12.0 language features
- Visual Studio 2022 or compatible IDE

## Architecture

The application follows a modular, object-oriented design with clear separation of concerns:

```
indentCheckerApp/
├── Program.cs              # Main entry point and orchestration
├── IndentMode.cs           # Enumeration for validation modes
├── ConsoleColorUtil.cs     # Colored console output utilities
├── MappingFile.cs          # JSON configuration file handler
├── TrimmedLinesFile.cs     # Text file configuration handler
├── ScanStatistics.cs       # Statistics collection and reporting
└── Configuration Files/
    ├── scanRoots.txt       # Root directories to scan
    ├── ignoredFullPaths.txt # Specific paths to ignore
    ├── ignoredNames.txt    # File/directory names to ignore
    ├── ignoredExtensions.txt # File extensions to ignore
    ├── encodingMap.json    # File encoding mappings
    └── indentModeMap.json  # Indentation mode mappings
```

## Core Components

### 1. Program Class
**File**: [`Program.cs`](../src/indentCheckerApp/Program.cs)

The main orchestrator that:
- Validates required configuration files
- Loads all configuration settings
- Coordinates the scanning process
- Manages error handling and user interaction

**Key Methods**:
- [`Main(string[] args)`](../src/indentCheckerApp/Program.cs:22) - Application entry point
- [`ScanDirectory(...)`](../src/indentCheckerApp/Program.cs:82) - Recursive directory scanning
- [`NeedsIndentationAttention(...)`](../src/indentCheckerApp/Program.cs:142) - Core validation logic
- [`ParseIndentMode(string?)`](../src/indentCheckerApp/Program.cs:190) - Mode parsing utility

### 2. IndentMode Enumeration
**File**: [`IndentMode.cs`](../src/indentCheckerApp/IndentMode.cs)

Defines validation strictness levels:
- [`Flex`](../src/indentCheckerApp/IndentMode.cs:5) - Allows any indentation ≥4 spaces
- [`Strict`](../src/indentCheckerApp/IndentMode.cs:6) - Requires indentation in multiples of 4 spaces

### 3. ConsoleColorUtil Class
**File**: [`ConsoleColorUtil.cs`](../src/indentCheckerApp/ConsoleColorUtil.cs)

Provides colored console output capabilities:
- [`Write(string, ConsoleColor, ConsoleColor?)`](../src/indentCheckerApp/ConsoleColorUtil.cs:8) - Colored text output
- [`WriteLine(string, ConsoleColor, ConsoleColor?)`](../src/indentCheckerApp/ConsoleColorUtil.cs:21) - Colored line output

### 4. MappingFile Class
**File**: [`MappingFile.cs`](../src/indentCheckerApp/MappingFile.cs)

Handles JSON-based configuration mappings:
- [`Map`](../src/indentCheckerApp/MappingFile.cs:13) - Read-only dictionary of mappings
- [`GetValue(string)`](../src/indentCheckerApp/MappingFile.cs:27) - Retrieves mapped values

### 5. TrimmedLinesFile Class
**File**: [`TrimmedLinesFile.cs`](../src/indentCheckerApp/TrimmedLinesFile.cs)

Processes text-based configuration files:
- [`Lines`](../src/indentCheckerApp/TrimmedLinesFile.cs:13) - Collection of non-empty, trimmed lines
- Automatically filters comments (lines starting with #)

### 6. ScanStatistics Class
**File**: [`ScanStatistics.cs`](../src/indentCheckerApp/ScanStatistics.cs)

Comprehensive statistics collection and reporting:
- [`ScannedDirectories`](../src/indentCheckerApp/ScanStatistics.cs:7) - Tracked directories
- [`CheckedFiles`](../src/indentCheckerApp/ScanStatistics.cs:9) - Successfully processed files
- [`FilesWithIssues`](../src/indentCheckerApp/ScanStatistics.cs:13) - Files with indentation problems
- [`WriteReport(string)`](../src/indentCheckerApp/ScanStatistics.cs:29) - Generates detailed reports

## Configuration System

### Text-Based Configuration Files

#### scanRoots.txt
**Purpose**: Defines root directories for scanning
**Format**: One directory path per line
**Example**:
```
C:\Repositories
D:\Projects\MyApp
```

#### ignoredFullPaths.txt
**Purpose**: Specifies exact paths to ignore (files or directories)
**Format**: One full path per line
**Example**:
```
C:\Repositories\MyProject\temp
C:\Repositories\MyProject\build\output.log
```

#### ignoredNames.txt
**Purpose**: File or directory names to ignore regardless of location
**Format**: One name per line
**Default Values**:
```
.DS_Store
.git
.idea
.svn
.vs
.vscode
bin
desktop.ini
obj
Thumbs.db
```

#### ignoredExtensions.txt
**Purpose**: File extensions to skip during scanning
**Format**: One extension per line (with or without leading dot)
**Example**:
```
.exe
.dll
.bin
```

### JSON-Based Configuration Files

#### encodingMap.json
**Purpose**: Maps file extensions or paths to specific text encodings
**Format**: JSON object with string key-value pairs
**Example**:
```json
{
    ".bat": "shift_jis",
    ".cmd": "shift_jis",
    "C:\\legacy\\file.txt": "iso-8859-1"
}
```

#### indentModeMap.json
**Purpose**: Maps file extensions or paths to indentation validation modes
**Format**: JSON object with string key-value pairs
**Example**:
```json
{
    ".cs": "flex",
    ".json": "strict",
    ".py": "strict"
}
```

### Configuration Loading Priority
1. **File-specific mappings**: Full file paths take precedence
2. **Extension mappings**: File extensions are used as fallback
3. **Default values**: Built-in defaults when no mapping exists
   - Encoding: UTF-8
   - Indent Mode: Strict

## Indentation Validation Rules

### Core Validation Logic
The application validates indentation based on the following rules (as implemented in `NeedsIndentationAttention`):

#### 1. Character Validation
- **Only ASCII spaces (U+0020) are allowed** for indentation
- Any other whitespace character (tabs, non-breaking spaces, etc.) triggers an error
- Error format: `"Non-ASCII-space char (U+XXXX) used for indentation at line N"`

#### 2. Empty Line Detection
- Lines containing only whitespace characters are flagged as errors
- Error format: `"Line N contains only indentation and no visible characters"`

#### 3. Minimum Indentation Requirement
- All indented lines must have **at least 4 spaces**
- Lines with 1-3 spaces of indentation trigger an error
- Error format: `"Indentation less than 4 spaces at line N (found X spaces)"`

#### 4. Mode-Specific Validation

##### Flex Mode
- Allows any indentation ≥4 spaces
- No requirement for specific multiples
- More permissive for mixed coding styles

##### Strict Mode
- Requires indentation to be **multiples of 4 spaces** (4, 8, 12, 16, etc.)
- Error format: `"Indentation not a multiple of 4 spaces at line N (found X spaces)"`
- Enforces consistent indentation patterns

### Validation Algorithm (as implemented)
```csharp
for each line in file:
    count leading whitespace characters
    if (entire line is whitespace):
        report error: "Line contains only indentation"
    else if (contains non-ASCII-space in indentation):
        report error: "Non-ASCII-space character used"
    else if (indentation > 0 and indentation < 4):
        report error: "Indentation less than 4 spaces"
    else if (strict mode and indentation % 4 != 0):
        report error: "Indentation not multiple of 4"
```

## File Processing Workflow

### 1. Initialization Phase
1. **Configuration Validation**: Verify all required files exist
2. **Configuration Loading**: Parse all configuration files
3. **Statistics Initialization**: Create tracking objects

### 2. Directory Scanning Phase
1. **Root Processing**: Process each root directory from [`scanRoots.txt`](../src/indentCheckerApp/scanRoots.txt)
2. **Recursive Traversal**: Depth-first directory traversal
3. **Sorting**: Alphabetical sorting of directories and files
4. **Filtering**: Apply ignore rules at each level

### 3. File Processing Phase
For each file:
1. **Ignore Check**: Verify file should be processed
2. **Encoding Detection**: Determine appropriate text encoding
3. **Content Reading**: Load file content with specified encoding
4. **Mode Determination**: Select validation mode (Strict/Flex)
5. **Validation**: Apply indentation rules
6. **Statistics Update**: Record results

### 4. Reporting Phase
1. **Statistics Compilation**: Aggregate all collected data
2. **Report Generation**: Create comprehensive report file
3. **Console Output**: Display summary and issues
4. **File Output**: Save detailed report to desktop

## Output and Reporting

### Console Output
- **Real-time feedback**: Issues displayed as they're found
- **Color coding**:
  - Red: Errors and missing files
  - Yellow: Indentation issues
  - Default: General information
- **Summary**: Final count of issues found

### Report File Generation
- **Location**: User's desktop directory
- **Filename**: `indentChecker-{UTC_timestamp}.log`
- **Format**: UTF-8 with BOM
- **Timestamp**: ISO 8601 format (YYYYMMDDTHHMMSSZ)

### Report Structure
```
Scan Report

[Scanned Directories]
List of all processed directories

[Detected Extensions]
All file extensions encountered

[Checked Files]
Files successfully processed with encoding and mode info

[Ignored by Full Path]
Files/directories ignored by full path matching

[Ignored by Name]
Files/directories ignored by name matching

[Ignored by Extension]
Files ignored by extension matching

[Files with Issues]
Detailed list of files with indentation problems

[Errors]
Any processing errors encountered
```

## Error Handling

### Configuration Errors
- **Missing Files**: Application exits with error message
- **Invalid JSON**: Detailed parsing error information; application exits
- **Invalid Encodings**: File is skipped, error is logged and aggregated

### File Processing Errors
- **Access Denied**: Log error and continue processing
- **Encoding Issues**: File is skipped, error is logged and aggregated
- **Malformed Files**: Log error and continue

### Recovery Strategies
- **Graceful Degradation**: Continue processing other files when individual files fail
- **Error Aggregation**: Collect all errors for comprehensive reporting (see `[Errors]` section in report)
- **User Notification**: Clear error messages with context, colored output for visibility

## Extensibility

### Adding New Validation Rules
1. Extend [`NeedsIndentationAttention`](../src/indentCheckerApp/Program.cs:142) method
2. Add new error message formats
3. Update documentation

### Supporting New File Encodings
1. Add mappings to [`encodingMap.json`](../src/indentCheckerApp/encodingMap.json)
2. Ensure encoding is registered in .NET
3. Test with sample files

### Custom Indentation Modes
1. Extend [`IndentMode`](../src/indentCheckerApp/IndentMode.cs) enumeration
2. Update [`ParseIndentMode`](../src/indentCheckerApp/Program.cs:190) method
3. Modify validation logic
4. Update configuration files

## Usage Examples

### Basic Usage
```bash
# Run with default configuration
indentCheckerApp.exe

# Configuration files must be present in application directory:
# - scanRoots.txt
# - ignoredFullPaths.txt
# - ignoredNames.txt
# - ignoredExtensions.txt
# - encodingMap.json
# - indentModeMap.json
```

### Configuration Examples

#### Scanning Multiple Projects
**scanRoots.txt**:
```
C:\Projects\WebApp
C:\Projects\API
D:\Legacy\OldProject
```

#### Custom Encoding Support
**encodingMap.json**:
```json
{
    ".bat": "shift_jis",
    ".cmd": "shift_jis",
    ".legacy": "iso-8859-1",
    "C:\\OldProject\\data.txt": "windows-1252"
}
```

#### Mixed Validation Modes
**indentModeMap.json**:
```json
{
    ".cs": "flex",
    ".js": "flex",
    ".json": "strict",
    ".xml": "strict",
    ".py": "strict"
}
```

## Technical Implementation Details

### Dependencies
- **System.Text.Json**: JSON configuration parsing
- **System.Text.Encoding.CodePages**: Extended encoding support
- **.NET 9.0 Base Class Library**: Core functionality

### Performance Characteristics
- **Memory Usage**: Minimal - processes files individually
- **I/O Optimization**: Sequential file reading
- **Scalability**: Handles large directory structures efficiently

### Thread Safety
- **Single-threaded**: Current implementation is not thread-safe; all collections are used in a single-threaded context
- **No locking required**: No shared mutable state or concurrency
- **Future Enhancement**: Could be parallelized for better performance

### Database/Network
- **No database or network dependencies**: All data is read from and written to local files only

---

**Document Version**: v0.1
**Last Updated**: 2025-06-17
**Generated By**: indentChecker Specifications Generator (auto-synced to code)