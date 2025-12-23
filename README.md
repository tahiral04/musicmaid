# Audio Management Scripts

A collection of shell scripts I use to manage my music library.

Completions are also available (currently only for zsh). You can install them by running `install-completions`.

Run `install-completions help` to get more information.

## Scripts

### musicmaid

Feature rich script that does most of the jobs.

**Features:**

- Rip CDs to WAV format
- Convert WAV files to FLAC
- Convert WAV/FLAC files to AAC
- Multi-threaded conversions
- Copy or move files to directories organized by artist/album structure
- Resize, convert and compress cover images

**Usage:** `musicmaid [options] <source> [ipod-dir] [flac-dir]`

**Options:**

- `-i, --ipod` -- Skip AAC conversions
- `-f, --flac` -- Skip FLAC conversions
- `-p, --cp-ipod` -- Don't copy or move files to _ipod-dir_
- `-l, --cp-flac` -- Don't copy or move files to _flac-dir_
- `-r, --rip` -- Rip CD into "_source/mm_cd_rip_" before operations
- `-p, --rip-multi` -- Rip multiple CDs into "_source/mm_cd_rip_" before operations
- `-v, --keep-va` -- Don't rename "_Various Artists_" folder to "_Compilations_"
- `-c, --cover` -- Process cover images in the _source_ directory
- `-h, --help` -- Show help message

**Dependencies:** GNU Parallel, abcde, ffmpeg, qaac, ImageMagick

---

### playlist-adaptor

Adapts playlists created by iTunes for RockBox compatibility and viceversa.

**Usage:** `playlist-adaptor mode file`

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
