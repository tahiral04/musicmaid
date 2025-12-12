# AI Coding Agent Instructions: musicmaid

## Project Overview

Shell script toolkit for audio library management: CD ripping, format conversion (WAV→FLAC/AAC), file organization (artist/album structure), playlist format adaptation (iTunes↔RockBox), and cover art processing.

## Architecture

**Three independent executables:**
- `musicMaid` - Main orchestrator for CD ripping, multi-threaded conversion, file organization
- `playlistAdaptor` - Bidirectional playlist format converter (iTunes m3u ↔ iPod m3u8)
- `ytdlp2mp3` - Audio trimming and MP3 conversion wrapper around ffmpeg

**No shared modules** - each script is self-contained with inline configuration and helper functions.

## Critical Patterns

### Shell Script Conventions

All scripts use **POSIX sh** (except `playlistAdaptor` and `ytdlp2mp3` which use zsh for specific features):
- `musicMaid`: Pure POSIX sh - no bashisms/zshisms (runs on `/bin/sh`)
- Function definitions: `function_name() { ... }` not `function function_name()`
- Test with `[ ]` not `[[ ]]` in POSIX scripts
- Variable expansion: Use `"$var"` always, not `$var`

### musicMaid Workflow Architecture

**Sequential execution pattern:**
1. CD ripping (`rip_cd()`) → writes to `$SRC_DIR/mm_cd_rip/`
2. Parallel conversion (`convert_ipod()`, `convert_flac()`) → uses GNU Parallel with `$THREAD_NUM` workers
3. File organization (`copy_files()`) → "artist/album" hierarchy, moves conversions, copies originals
4. Cover processing (`cover_processor()`) → resizes/converts images in-place

**Critical: Source directory structure**
Scripts expect `SRC_DIR` with subdirectories in format: `artist/album/*.{wav,flac,m4a,mp3}`
- "Various Artists" → "Compilations" (disable with `keep-va`)
- Target paths extracted via `get_target_path()` function

### Parallel Processing

`musicMaid` uses GNU Parallel for conversions:
```bash
find ... | parallel -0 -N2 -j"$THREAD_NUM" --colsep '\0' '...'
```
- Null-separated input (`-0`) for filenames with spaces
- Processes pairs (`-N2`): destination + source
- Exports required env vars before parallel block
- Inline script string for worker execution

### Encoder Configuration

**Wine detection for Windows executables:**
```bash
check_dependency() detects PE executables (MZ header)
→ Auto-sets ENCODER_*_USE_WINE=1
→ Wraps commands: WINEDEBUG=-all wine "$ENCODER"
```

**Current encoder paths:**
- AAC: `$HOME/qaac/qaac.exe` (TVBR 100, quality 2)
- FLAC: `ffmpeg` (compression level 12)

### Completion System

Zsh completions in `completions/_musicMaid`:
- Uses `_arguments -C` framework
- Tracks used options to prevent duplicates
- File completion for directory arguments (`_files -/`)
- Install via `install-completions` script → copies to `~/.zsh/completions`

## Development Workflows

### Testing musicMaid

**Test directory structure:**
```
test/
├── src/           # Source directory with test files
│   └── Artist/
│       └── Album/
│           └── *.{wav,flac,mp3,m4a}  # Real files from backups or empty files with correct extensions
├── flac/          # FLAC destination directory
└── ipod/          # AAC destination directory
```

Create test files manually to verify both conversion and copy/move operations:
```bash
mkdir -p test/{src,flac,ipod}/Artist/Album
touch test/src/Artist/Album/01\ Track.wav  # Empty files work for logic testing
```

**Dry-run simulation** - check logic without actual encoding:
```bash
ENCODER_AAC="echo" ENCODER_FLAC="echo" ./musicMaid src-dir test/src ipod-dir test/ipod flac-dir test/flac
```

### Adding New Options

1. Add option parsing in `parse_parameters()` case statement
2. Add flag variable in CONFIGURATION section (default value)
3. Update `print_help()` with description
4. Update `completions/_musicMaid` opts array
5. Implement feature in appropriate function (convert_*, copy_files, etc.)

### Modifying Conversion Logic

**Don't modify parallel execution strings directly** - test commands manually first:
```bash
# Extract a sample file path, test encoder command
find /test -name "*.wav" | head -1 | xargs -I{} echo qaac --tvbr 100 {}
```

## External Dependencies

**Required tools (checked via `check_dependency()`):**
- `parallel` - GNU Parallel for multi-threaded conversions
- `abcde` - CD ripper (only if using `rip` option)
- `ffmpeg` - FLAC encoding
- `qaac` - AAC encoding (Windows binary, needs Wine on Linux)
- `magick` (ImageMagick) - Cover processing

**Missing dependency behavior:** Scripts print error and skip that operation (graceful degradation)

## Error Handling Strategy

Scripts use **partial failure tolerance**:
- `check_dependency()` returns non-zero if deps missing → caller skips operation with `log_info`
- CD rip failures exit immediately (`exit 1`) - can't continue without source files
- Conversion/copy failures don't halt execution - other operations proceed
- Each major function (`convert_ipod()`, `convert_flac()`, etc.) handles its own dependency checks

## Common Pitfalls

1. **Path handling**: Always use `realpath` before processing (see `parse_parameters()`)
2. **Parallel env vars**: Must `export` variables before parallel block - they run in subshells
3. **File extension matching**: Use `-iname` for case-insensitive matching (m4a/M4A both valid)
4. **POSIX compliance**: `musicMaid` must run on non-bash shells - avoid `[[`, `${var^^}`, etc.
5. **Color codes in parallel**: Export `COLOR_*` variables so parallel workers can use them
6. **Temp files**: `cleanup_tmp_files()` registered with `trap` - always use `/tmp/mm_*` pattern

## File References

- Main conversion logic: `musicMaid` lines 213-257 (convert_ipod, convert_flac)
- Directory structure handling: `musicMaid` lines 199-212 (get_target_path)
- File organization: `musicMaid` lines 255-304 (copy_files)
- CD ripping: `musicMaid` lines 305-317 (rip_cd)
- Cover processing: `musicMaid` lines 344-375 (cover_processor)
- ABCDE configuration: `musicMaid` lines 318-343 (write_abcde_conf)
- Playlist path transformations: `playlistAdaptor` lines 24-41 (process_playlist)
