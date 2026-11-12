# djdojo

Turn your YouTube or SoundCloud playlist into lossless audio files for DJ practice. It is for
private practice with music you have the right to download. The whole playlist lands in one
folder, ready for a USB stick and the deck, or for rekordbox, Engine DJ, Serato and Traktor.

Here is a run with a playlist by [NoCopyrightSounds](https://ncs.io), a label that releases its
music for free use by creators:

```bash
djdojo PLRBp0Fe2GpgliIz-BjIwIBgysth8ZkE8c
```

```bash
# djdojo output:
# djdojo → Downloading the playlist "NCS - Instrumental Future House" (20 tracks) as AIFF with the YouTube login from Firefox
# djdojo ✓ Audio: opus 263k, Premium quality
# djdojo • The playlist goes into "~/Music/djdojo/NCS - Instrumental Future House"
# djdojo ✓ 01/20 Clarx - H.A.Y | Future House | NCS - Copyright Free Music
# djdojo ✓ 02/20 Clarx - Zig Zag | Future House | NCS - Copyright Free Music
# ...
# djdojo ✓ Done. 20 .aiff files downloaded. 0 failed.
# djdojo • The playlist is in "~/Music/djdojo/NCS - Instrumental Future House"
```

Every track gets one line: `✓` when it is downloaded, `•` when it was there already, and `✗` with
the reason when it failed. While a track downloads, its line shows how far it is. The name and the
mark are colored and tell the lines apart at a glance: `→` for what `djdojo` is doing, `•` for
information, `✓` for what went well, `!` for what needs your attention and `✗` for what failed. The
last three are colored all the way: green, yellow and red. `NO_COLOR=1` turns the colors off.
Output that is piped or saved to a file is plain text, every line starting with `djdojo:`.

`djdojo` runs [yt-dlp](https://github.com/yt-dlp/yt-dlp), which says a lot more. Add `verbose` to
see all of it instead. Either way, everything yt-dlp said in the last run is kept in
`~/.config/djdojo/last-run.log`, which is the file to send along when you report a problem.

Every file is decoded once from the best audio stream and stored lossless with title, artist,
album, track number and cover. MP3 would add another lossy step, and VBR MP3 can give imprecise cue
points on older CDJs. The audio cannot get better than the source, but on SoundCloud the source can
be the uploader's own WAV, see [SoundCloud](#soundcloud).

djdojo is for practice at home with music you have the right to download: your own uploads, tracks
released for free use such as the NoCopyrightSounds catalogue, or music you have bought. You are
responsible for complying with YouTube's Terms of Service and with copyright law where you live. For
a gig, a stream or any other public or professional use, buy the music. WAV, AIFF or FLAC from
Beatport, Bandcamp and the like sound better, and the people who made it get paid.

## Installation

Requires macOS and [Homebrew](https://brew.sh). Nothing else needs to be installed.

```bash
brew install ronnidc/tap/djdojo
```

Update with `brew update && brew upgrade`. YouTube changes things often, so when a download
suddenly fails, that is the first step. `brew upgrade` alone only looks for new versions once a
day, so it can miss a fix that just came out.

## Usage

```bash
djdojo PLxxxxxxxx             # playlist ID or full link
djdojo PLxxxxxxxx flac        # FLAC instead of the default format, this once
djdojo PLxxxxxxxx safari      # login from a specific browser
djdojo LM                     # YouTube Music's "Liked Music"
djdojo PLxxxxxxxx nologin     # without login, and without the Premium audio
djdojo PLxxxxxxxx verbose     # everything yt-dlp says, instead of one line per track
djdojo SOUNDCLOUD_LINK        # a SoundCloud playlist, album, profile or track
djdojo --help
```

A YouTube playlist is given as its full link or just its ID, the part after `list=` in the link. A
single video works the same way. If an ID is not recognized, use the full link. A SoundCloud
playlist is given as its link. Extra yt-dlp options can be passed along, for example
`--playlist-items 1-10` for the first ten tracks.

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
one that is logged in to YouTube, or to SoundCloud for a SoundCloud link. If none is, it explains
why and asks whether to download without login. A browser name (`chrome`, `safari`, `firefox`,
`edge`, `brave` and more) limits it to that browser, and `nologin` skips the login altogether.
Either can be made permanent with a line in `~/.config/djdojo/config`: `login=firefox` or
`login=no`.

On YouTube, login gives access to private playlists. The audio only gets better with YouTube
Premium: then there is a higher-quality stream, about 256k, which `djdojo` picks by itself and
shows in the "Audio" line. The line is for the first track, and a track that has no Premium stream
gets the best one it has. `djdojo` uses your login only towards YouTube and SoundCloud. Cookies
are not sent anywhere else.

A browser can hold several profiles, each with its own login, for example a private one and one
for school. `djdojo` looks through all of them. When more than one is logged in, it looks up the
first track with each and uses the first one that gets Premium audio, and the line about the
download names the profile. To settle it yourself, put the profile's folder after the browser
name: `djdojo PLxxxxxxxx "chrome:Profile 1"`, or `login=chrome:Profile 1` in the config. Chrome
shows the folder as the last part of "Profile Path" on the page `chrome://version`.

When no login gets Premium audio for the first track, `djdojo` downloads the YouTube playlist
without login, if it can be opened that way, and says so at the start and at the end. The audio is
the same, and logged-in downloads are the ones YouTube refuses first. To use the login anyway, name
the browser: `djdojo PLxxxxxxxx chrome`, or `login=chrome` in the config. A playlist that needs a
login keeps it, and with several logins that can open it, `djdojo` asks which one to use.

macOS blocks other programs from reading browser data, so Terminal needs **Full Disk Access**:
System Settings > Privacy & Security > Full Disk Access, add Terminal, quit Terminal with Cmd+Q and
open it again. Otherwise `djdojo` reports "cannot read cookies". Chrome also asks for access to the
keychain the first time: choose "Always Allow".

### SoundCloud

A SoundCloud link can be a playlist or album (`soundcloud.com/artist/sets/name`), a profile's
tracks (`soundcloud.com/artist/tracks`) or likes (`soundcloud.com/artist/likes`), or a single
track. The genre the uploader gave the track goes into the genre tag.

Without login SoundCloud usually streams AAC at 160k. Logged in to soundcloud.com, `djdojo` gets
the uploader's original file for every track whose uploader has enabled downloads, often a WAV,
so the audio stays lossless all the way. With SoundCloud Go+ the other tracks get the 256k stream.
The "Audio" line shows what the first track gets. A free download that sits behind a gate on
another site, such as follow or like first, is not reached this way: download it there and put the
file in the folder yourself.

## From computer to deck

- **The USB stick** must be FAT32 ("MS-DOS (FAT)" in Disk Utility, partition scheme Master Boot
  Record). Older players do not read exFAT or APFS. The file names are FAT32-safe.
- **rekordbox or Engine DJ**: drag the playlist's folder in, let it analyze the tracks, and export
  to the stick. The deck then has waveform, beatgrid, hot cues and the playlist ready. Without
  them most decks can browse the folders directly, but without waveform and beatgrid.
- **Serato, Traktor** and other DJ software read the folder straight from disk.
- If the tracks are already in rekordbox and the cover is missing, reload the tags (right-click
  the track).
- **Finder** shows the cover of an AIFF but not of a FLAC, because macOS only reads the tags in a
  FLAC. The cover is in the file all the same.

## When something fails

| Message                                   | Meaning                                                          |
|-------------------------------------------|------------------------------------------------------------------|
| `cannot read cookies`                     | Terminal lacks Full Disk Access, see Login                       |
| `not logged in to YouTube`                | Log in to music.youtube.com in the browser                       |
| `not logged in to SoundCloud`             | Log in to soundcloud.com in the browser                          |
| `Video unavailable`                       | The track is removed or blocked on YouTube, cannot be downloaded |
| `Requested format is not available`       | YouTube changed something, run `brew update && brew upgrade`     |
| `HTTP Error 403: Forbidden`               | YouTube refused the track, update as above or add `nologin`      |
| `YouTube Music is not directly supported` | Only a warning, everything works                                 |
| `only available for registered users`     | SoundCloud without login, the stream is used instead             |

A failed track's line ends with yt-dlp's reason. The last two messages are warnings, which only
show with `verbose` and in `~/.config/djdojo/last-run.log`.

Failed tracks are counted at the end. Run the same command again to retry them, the downloaded
ones are skipped. When YouTube refuses tracks with `HTTP Error 403`, `djdojo` says so and shows the
command that downloads without login. Without YouTube Premium the audio is the same either way.

Now and then YouTube offers a track only as a video. It is downloaded all the same, with the
video's sound, which is 128k AAC, about half of Premium audio. `djdojo` lists those tracks at the
end. Delete them and run the same command again to try for the usual audio.

## Terms

YouTube's terms of service do not allow downloading content outside YouTube's own apps,
SoundCloud's only allow it for tracks whose uploader has enabled downloads, and the music remains
the property of its rights holders. Whether a private copy for practicing is allowed
depends on the copyright law where you live. djdojo is made for practicing on your own playlists
at home and in the practice room, and you use it at your own risk. Music that is going to be
played for others is bought from the artist or a store.

## Development

`djdojo` is a bash script around [yt-dlp](https://github.com/yt-dlp/yt-dlp) and ffmpeg, and
`djdojo.conf` is yt-dlp's configuration. Run it straight from a checkout with `./djdojo --help`.
`djdojo` finds `djdojo.conf` next to itself. Bug reports and pull requests are welcome on GitHub.

## License

MIT, see `LICENSE`.
