# Changelog

All notable changes to djdojo are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added

- A browser with several logged-in profiles, for example a private and a school profile in Chrome: `djdojo` looks up the first track with each and uses the first one that gets Premium audio, or Go+ audio or the uploader's original file on SoundCloud. The line about the download names the profile. When none does and the playlist needs a login, it asks which one to use.
- One profile of a browser can be named: `"chrome:Profile 1"` on the command line, or `login=chrome:Profile 1` in `~/.config/djdojo/config`.
- Tracks that YouTube only offers as a video are listed at the end, since their audio is the video's 128k AAC. Deleting them and running the same command again tries for the usual audio.
- In a terminal `djdojo`'s own lines start with the name and a mark in the color of their kind: `→` doing, `•` information, `✓` went well, `!` needs attention, `✗` failed. The last three are colored all the way, green, yellow and red. `NO_COLOR` turns the colors off, and output that is piped or saved stays plain `djdojo: text`.

### Changed

- When no login gets Premium audio for the first track, a YouTube playlist that can be opened without login is downloaded without it, and `djdojo` says so at the start and at the end. Naming the browser, on the command line or as `login=` in the config, keeps the login.
- A browser named without a profile has its profiles looked through in the same way. Before, yt-dlp took the profile that was used last, which with two browser windows open could be either.
- yt-dlp's errors are only colored in a terminal, so saved output is free of color codes.
- When YouTube or SoundCloud refuses tracks with `HTTP Error 403`, `djdojo` says so at the end and shows the command that downloads without login, instead of only suggesting the same command again.

### Fixed

- The summary no longer shows a negative number of downloaded files when files are removed from the folder during the run.
- The hint about removed or blocked tracks names SoundCloud for a SoundCloud link.

## [1.2.0] - 2026-11-10

### Added

- SoundCloud links: a playlist or album, a profile's tracks or likes, or a single track (#1). Logged in, `djdojo` gets the uploader's original file, often a WAV, for tracks whose uploader has enabled downloads, and the 256k stream with SoundCloud Go+. An AIFF is 16 bit/44.1 kHz also when the original is not.
- For a SoundCloud link the login search looks for a SoundCloud login, and the messages name SoundCloud instead of YouTube.
- SoundCloud's genre goes into the genre tag.

### Changed

- Covers wider than 1000 px are scaled down. SoundCloud's original artwork is up to 3000x3000 and added several MB to every file.
- `djdojo` embeds the FLAC cover itself, as it already did for AIFF, so FLAC files without a cover get one on the next run. Covers are marked as front cover in both formats.
- The "Audio" line says aac instead of mp4a.40.2.

### Fixed

- When yt-dlp's Python is not found, the hint to name a browser now shows the command that was run instead of the list of browsers.

## [1.1.1] - 2026-10-27

### Changed

- The README and `--help` say what `djdojo` is for: private practice with music you have the right to download, and buying the music for a gig or any other public or professional use.

## [1.1.0] - 2026-10-07

### Added

- `nologin` on the command line downloads without a YouTube login. yt-dlp's own `--no-cookies-from-browser` still works.
- `login=no` or `login=<browser>` in `~/.config/djdojo/config` makes that the default, like `format` does.
- `--help` marks the format that is the default, the one stored in `~/.config/djdojo/config`.

### Changed

- When no browser is logged in to YouTube, `djdojo` asks whether to download without login instead of only explaining how. From a script, without a terminal, it still stops.

## [1.0.0] - 2026-10-07

### Added

- `djdojo` downloads a YouTube playlist as lossless AIFF or FLAC, the whole playlist in one folder, with title, artist, album, track number and square cover art in every file.
- First run asks for format and folder and stores the answers in `~/.config/djdojo/config`. `aiff` or `flac` on the command line overrides the format for one run.
- Uses the YouTube login of the first browser that has one (Firefox, Safari, Chrome), or of a browser named on the command line. With YouTube Premium the higher-quality audio stream (about 256k) is picked.
- A playlist or a video can be given as its bare ID instead of the full link, including the IDs YouTube Music shows with a VL prefix.
- Reports playlist name, track count, format, audio stream and folder before the download, and the number of downloaded, already present and failed tracks after.

[Unreleased]: https://github.com/ronnidc/djdojo/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/ronnidc/djdojo/compare/v1.1.1...v1.2.0
[1.1.1]: https://github.com/ronnidc/djdojo/compare/v1.1.0...v1.1.1
[1.1.0]: https://github.com/ronnidc/djdojo/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/ronnidc/djdojo/releases/tag/v1.0.0
