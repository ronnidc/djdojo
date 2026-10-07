# Changelog

All notable changes to djdojo are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses [Semantic Versioning](https://semver.org/).

## [Unreleased]

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

[Unreleased]: https://github.com/ronnidc/djdojo/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/ronnidc/djdojo/releases/tag/v1.0.0
