<p align="center">
  <img src="dumpsiftlogo.jpg" alt="DumpSift logo" width="72" />
</p>

# DumpSift

Read-only string search for multi-GB memory dumps and Windows kernel dumps.

[![Latest Release](https://img.shields.io/github/v/release/coodz10/dumpsift?label=release)](https://github.com/coodz10/dumpsift/releases/latest)

DumpSift is a read-only desktop tool for memory forensics, incident response, and crash analysis. Open multi-GB memory dump and Windows DMP files instantly without loading them into RAM, index every printable string in one pass, and search or filter across millions of results without altering the evidence file.

![DumpSift searching strings in a kernel dump](docs/screenshot.png)

## Why DumpSift

- **No extraction step**: opens multi-GB dumps directly from disk via read-only memory-mapped windows.
- **Instant searches after one-time indexing**: printable strings are indexed once on load so queries and filters run in memory over a compact index.
- **Never modifies the file**: strictly read-only file handles with bounds-checked reads on every window.

## Features

### String search

- Builds a string index once on load for printable ASCII/UTF-8 and UTF-16LE strings of 4 or more characters.
- Streams all indexed strings immediately while scanning, with live progress and match counts.
- Searches literal text (case-insensitive) or JIT-accelerated PCRE2 regular expressions (`UTF`, `UCP`, `DOTALL`).
- Opens a separate Full-String window on row click with line wrapping, in-string find, previous/next navigation, and clipboard copy.

### Refine filters

- Stacks non-destructive filter passes over the current result list using `Contains`, `Contains (no case)`, `Exclude`, `Regex`, and `Regex (no case)` modes.
- Supports inline `Contains` syntax including `-exclude`, `"exact phrase"`, and `re:pattern` (for example: `java -jar -minecraft re:\.exe$`).
- Filters by encoding (`Any`, `ASCII`, `UTF-16`), character length (`Min` / `Max`), hex offset range (`From` / `To`), and exact duplicate hiding.
- Displays active passes as removable chips alongside `Undo` and `Reset` controls, caching pass snapshots so removing filters restores results instantly without re-scanning the dump.
- **Regex presets**: includes built-in forensic starting points (Fileless, Obfuscation, Network, LOLBins, Paths) with `Copy` and `Filter` actions plus an operator `Regex Presets` rail for adding custom in-memory patterns.

### Interface

- Clean, high-contrast UI with Dark and Light mode toggle and `A-` / `A+` interface scaling.
- Supports 10 languages with a startup language selector and a top-bar language switcher.
- Zero disk footprint: creates no config or state files in `%APPDATA%` or on the host system.

### Formats

- Detects 64-bit and 32-bit Windows kernel dumps (`PAGEDU64`, `PAGEDUMP`), Windows Minidumps (`MDMP`), ELF Core dumps, and Linux KDUMP files by magic bytes, and works on any raw binary file.

## How it works

```text
Open -> Map -> Index -> Search -> Refine -> View
```

**Open & Map**: DumpSift opens the dump file with read-only Win32 file mapping and slides bounded `MapViewOfFile` windows across the file, so multi-GB images are never loaded into RAM.

**Index**: A single multithreaded pass scans the file once and stores only the offset, length, and encoding for each printable string, keeping the index compact.

**Search & Refine**: Searches and stacked filters run over that compact index (with UTF-16 queries encoded as patterns rather than converting the file), and non-destructive filters never alter the original results so clearing them restores everything.

**View**: String text is read from the file mapping only for the rows currently visible on screen.

## Usage

1. Launch DumpSift, choose your language, and drop a `.dmp` or memory dump file onto the window (or click `browse`).
2. Wait for the one-time string index to finish, then type a query in the top search bar and press `Enter`.
3. Stack filters in the refine bar (`Contains`, `Exclude`, `Regex`, encoding, length, or hex offset) to narrow down the list.
4. Click any row to inspect the full string in a separate window.

## Shortcuts

| Shortcut | Action |
| --- | --- |
| `Ctrl+O` | Open dump |
| `Ctrl+F` | Focus search (or find in the full-string window) |
| `Ctrl+C` | Copy selected row or string |
| `Ctrl+S` | Save results to `.txt` |
| `Esc` | Cancel scan or close full-string window |

## Download

Download `DumpSift.exe` from the [latest release](https://github.com/coodz10/dumpsift/releases/latest).

Requires Windows 10/11 x64. Single `.exe`, no installation.

## Verify the download

Each release publishes the SHA-256 hash of `DumpSift.exe`. Run the command below and compare the output with the hash in the release notes:

```powershell
Get-FileHash .\DumpSift.exe -Algorithm SHA256
```

## Privacy

DumpSift runs fully offline and leaves no files on the host machine: your dumps never leave your PC and no configuration files are written to disk.

## Source code

DumpSift is closed source. The source code is private and is not published on GitHub. If you need to review it for security reasons, contact me on Discord: `_1samu1_`.
