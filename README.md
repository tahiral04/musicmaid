# Audio Management Scripts

A collection of shell scripts I use to manage my music library.
<br>
Completions are also available (currently only for zsh). You can install them by running `install-completions`.
<br>
Run `install-completions help` to get more information.

## Scripts

### musicMaid

Feature rich script that does most of the jobs.

**Features:**
- Rip CDs to WAV format
- Convert WAV files to FLAC
- Convert WAV/FLAC files to AAC
- Copy or move files to destination directories organized by artist/album structure
- Process cover images
- Multi-threaded conversions

**Usage:** `musicMaid [options] src-dir <SRC> ipod-dir <IPOD_DIR> flac-dir <FLAC_DIR>`

**Options:**
- `no-ipod` - Skip AAC conversions
- `no-flac` - Skip FLAC conversions
- `no-copy-ipod` - Don't copy or move files to _IPOD_DIR_
- `no-copy-flac` - Don't copy or move files to _FLAC_DIR_
- `rip` - Rip CD into "_SRC/mm_cd_rip_" before other operations
- `keep-va` - Don't rename "_Various Artists_" folder to "_Compilations_"
- `cover` - Process cover images in the source directory
- `help` - Show help message

**Dependencies:** GNU Parallel, abcde, ffmpeg, qaac, ImageMagick

---

### playlistAdaptor

Adapts playlists created by iTunes for RockBox compatibility and viceversa.

**Usage:** `playlistAdaptor mode file`

**Options:**
- `mode` - Conversion mode, accepts values _itunes_ (converts for iPod) or _ipod_ (converts for iTunes)
- `file` - Path to a playlist file or a directory containing playlist files

---

### ytdlp2mp3

Converts audio files downloaded with yt-dlp to MP3 format. It can also cut audio in the beginning and/or the end.

**Usage:** `ytdlp2mp3 <file> [start_seconds] [end_seconds]`

**Options:**
- `file` - Path to the audio file
- `start_seconds` - Start time in seconds
- `end_seconds` - End time in seconds
