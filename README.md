# djdojo

Turn your YouTube playlist into lossless audio files for DJ practice. The whole playlist lands in
one folder, ready for a USB stick and the deck, or for rekordbox, Engine DJ, Serato and Traktor.

Here is a run with a playlist by [NoCopyrightSounds](https://ncs.io), a label that releases its
music for free use by creators. One command, and `djdojo` reports what it does:

```console
$ djdojo PLRBp0Fe2GpgliIz-BjIwIBgysth8ZkE8c
djdojo: Downloading the playlist "NCS - Instrumental Future House" (20 tracks) as AIFF with the YouTube login from Firefox
djdojo: Audio: opus 263k, Premium quality
djdojo: The playlist goes into "~/Music/djdojo/NCS - Instrumental Future House"
...
djdojo: Done. 20 .aiff files downloaded. 0 failed.
djdojo: The playlist is in "~/Music/djdojo/NCS - Instrumental Future House"
```

Every file is decoded once from YouTube's best audio stream and stored lossless with title, artist,
album, track number and cover. MP3 would add another lossy step, and VBR MP3 can give imprecise cue
points on older CDJs. The audio cannot get better than YouTube's source. For a real gig, purchased
WAV, AIFF or FLAC from Beatport, Bandcamp and the like are still better.

## Installation

Requires macOS and [Homebrew](https://brew.sh). Nothing else needs to be installed.

```bash
brew install ronnidc/tap/djdojo
```

Update with `brew upgrade`. YouTube changes things often, so when a download suddenly fails, that
is the first step.

## Usage

```bash
djdojo PLxxxxxxxx             # playlist ID or full link
djdojo PLxxxxxxxx flac        # FLAC instead of the default format, this once
djdojo PLxxxxxxxx safari      # login from a specific browser
djdojo LM                     # YouTube Music's "Liked Music"
djdojo PLxxxxxxxx nologin     # without login, and without the Premium audio
djdojo --help
```

A playlist is given as its full link or just its ID, the part after `list=` in the link. A single
video works the same way. If an ID is not recognized, use the full link. Extra yt-dlp options can
be passed along, for example `--playlist-items 1-10` for the first ten tracks.

The playlist can be run again later. Files already in the folder are skipped, so only new tracks
are downloaded.

### First run

The first time `djdojo` runs, it asks two questions and stores the answers in
`~/.config/djdojo/config`:

| Format | Plays on                                         | Size               |
|--------|--------------------------------------------------|--------------------|
| AIFF   | Every Pioneer CDJ/XDJ with USB (16 bit/44.1 kHz) | about 10 MB/minute |
| FLAC   | CDJ-2000NXS2, XDJ-1000MK2 and newer (48 kHz)     | about half         |

Denon DJ players and DJ software play both formats.

AIFF is the default because it works everywhere. Choose FLAC if you know you only play on newer
decks. The folder is `~/Music/djdojo` by default. The file can be edited freely, and if it is
deleted, `djdojo` asks again. A format on the command line (`aiff` or `flac`) applies to that run
only.

### Login

Without a browser name, `djdojo` tries Firefox, Safari and Chrome in that order and uses the first
one that is logged in to YouTube. If none is, it explains why and asks whether to download without
login. A browser name (`chrome`, `safari`, `firefox`, `edge`, `brave` and more) skips the search,
and `nologin` skips the login altogether. Either can be made permanent with a line in
`~/.config/djdojo/config`: `login=firefox` or `login=no`.

Login gives access to private playlists. The audio only gets better with YouTube Premium: then
there is a higher-quality stream, about 256k, which `djdojo` picks by itself and shows in the
"Audio" line. The line is for the first track, and a track that has no Premium stream gets the
best one it has. `djdojo` uses your login only towards YouTube. Cookies are not sent anywhere else.

macOS blocks other programs from reading browser data, so Terminal needs **Full Disk Access**:
System Settings > Privacy & Security > Full Disk Access, add Terminal, quit Terminal with Cmd+Q and
open it again. Otherwise `djdojo` reports "cannot read cookies". Chrome also asks for access to the
keychain the first time: choose "Always Allow".

## From computer to deck

- **The USB stick** must be FAT32 ("MS-DOS (FAT)" in Disk Utility, partition scheme Master Boot
  Record). Older players do not read exFAT or APFS. The file names are FAT32-safe.
- **rekordbox or Engine DJ**: drag the playlist's folder in, let it analyze the tracks, and export
  to the stick. The deck then has waveform, beatgrid, hot cues and the playlist ready. Without
  them most decks can browse the folders directly, but without waveform and beatgrid.
- **Serato, Traktor** and other DJ software read the folder straight from disk.
- If the tracks are already in rekordbox and the cover is missing, reload the tags (right-click
  the track).

## When something fails

| Message                                   | Meaning                                                          |
|-------------------------------------------|------------------------------------------------------------------|
| `cannot read cookies`                     | Terminal lacks Full Disk Access, see Login                       |
| `not logged in to YouTube`                | Log in to music.youtube.com in the browser                       |
| `Video unavailable`                       | The track is removed or blocked on YouTube, cannot be downloaded |
| `Requested format is not available`       | YouTube changed something, run `brew upgrade`                    |
| `YouTube Music is not directly supported` | Only a warning, everything works                                 |

Failed tracks are counted at the end. Run the same command again to retry them, the downloaded
ones are skipped.

## Terms

YouTube's terms of service do not allow downloading content outside YouTube's own apps, and the
music remains the property of its rights holders. Whether a private copy for practicing is allowed
depends on the copyright law where you live. djdojo is made for practicing on your own playlists
at home and in the practice room, and you use it at your own risk. Music that is going to be
played for others is bought from the artist or a store.

## Development

`djdojo` is a bash script around [yt-dlp](https://github.com/yt-dlp/yt-dlp) and ffmpeg, and
`djdojo.conf` is yt-dlp's configuration. Run it straight from a checkout with `./djdojo --help`.
`djdojo` finds `djdojo.conf` next to itself. Bug reports and pull requests are welcome on GitHub.

## License

MIT, see `LICENSE`.
