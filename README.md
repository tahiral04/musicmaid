# Audio Management Scripts

A collection of shell scripts I use to manage my music library.

Completions are also available (currently only for zsh). You can install them by running `install-completions`.

Run `install-completions help` to get more information.

## Scripts

### musicmaid

Feature rich script that does most of the jobs.

**Features:**

- Convert WAV files to lossless format (by default flac)
- Convert WAV/FLAC files to lossy format (by default aac)
- Multi-threaded conversions
- Copy or move files to directories organized by artist/album structure
- Rip CDs to WAV format
- Resize, convert and compress cover images

**Usage:** `musicmaid [options] <source> [lossy-dir] [lossless-dir]`

**Encoders:**

Edit these variables in the _musicmaid.env_ file to fine tune the encoders options or to set different encoders. By default the lossless encoder is `flac` and the lossy encoder is `qaac`.

Use the wildcard `':output'` and `':input'` (including quotes) in the options to specify the output and input file (or directory) paths respectively. musicmaid will replace them with the actual paths when running the encoders.

```bash
# lossy encoder
ENCODER_LOSSY="$HOME/.wine/drive_c/qaac/qaac.exe"
ENCODER_LOSSY_OPTIONS="--tvbr 100 --quality 2 --gapless-mode 2 --silent -d ':output' ':input'"
ENCODER_LOSSY_EXT="m4a" # extension without dot

# lossless encoder
ENCODER_LOSSLESS="flac"
ENCODER_LOSSLESS_OPTIONS="-V --best -j1 --silent -o ':output' -- ':input'"
ENCODER_LOSSLESS_EXT="flac" # extension without dot

# wine and its flags
ENCODER_WINE="WINEDEBUG=-all wine"
```

**Options:**

- `-a, --lossy` -- Do LOSSY conversions
- `-f, --lossless` -- Skip LOSSLESS conversions
- `-A, --cp-lossy` -- Do copy or move files to _lossy-dir_
- `-F, --cp-lossless` -- Don't copy or move files to _lossless-dir_
- `-r, --rip` -- Rip CD into "_source/mm_cd_rip_" before operations
- `-m, --rip-multi` -- Rip multiple CDs into "_source/mm_cd_rip_" before operations
- `-v, --keep-va` -- Don't rename "_Various Artists_" folder to "_Compilations_"
- `-c, --cover` -- Process cover images in the _source_ directory
- `-e, --env` -- Output default encoders configuration to _~/.config/musicmaid.env_
- `-s, --silent` -- Suppress non-error output. Also uses _xargs_ instead of _GNU Parallel_ for parallel processing
- `-h, --help` -- Show help message

**Dependencies:** GNU Parallel, abcde, ImageMagick

**NOTE:** _xargs_ will be used instead of _GNU Parallel_ if the dependency is not found!

---

### playlist-adaptor

Adapts playlists created by iTunes for RockBox compatibility and viceversa.

**Usage:** `playlist-adaptor <from> <to> <file>`

**Options:**

- `from` -- The type of playlist to convert from
- `to` -- The type of playlist to convert to
- `file` -- Path to a playlist file or a directory containing playlist files

**Valid types:**  `ipod`  `itunes`

---

### ytdlp2mp3

Converts audio files downloaded with yt-dlp to MP3 format. It can also cut audio in the beginning and/or the end.

**Usage:** `ytdlp2mp3 <file> [start_seconds] [end_seconds]`

**Options:**

- `file` -- Path to the audio file
- `start_seconds` -- Start time in seconds
- `end_seconds` -- End time in seconds

---

### ipodsync

Syncs the music library with the iPod and generates the updated Rockbox database.

**Usage:** `ipodsync`
