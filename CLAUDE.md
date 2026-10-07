# djdojo

`djdojo` downloads a YouTube playlist as lossless AIFF or FLAC for DJ practice. Pioneer DJ decks
are the reference, not the only target: AIFF at 44.1 kHz because the oldest CDJs need it, FLAC
only on newer models, FAT32 sticks. One bash script (`djdojo`) and one yt-dlp config
(`djdojo.conf`). `README.md` is the user documentation and is updated with every feature.

## Where things live

- GitHub `ronnidc/djdojo`. The Homebrew formula is in the tap checkout next to this repo,
  `~/Sites/tools/homebrew-tap/Formula/djdojo.rb` (GitHub `ronnidc/homebrew-tap`). Users install
  with `brew install ronnidc/tap/djdojo`. The formula puts `djdojo` and `djdojo.conf` in `libexec`
  and symlinks `djdojo` into `bin`, because `djdojo` looks for `djdojo.conf` next to its own
  resolved path (`readlink -f "$0"`).
- The private publish guide (accounts, the tap token, private test material) is
  `.claude/plans/djdojo-publish-guide.md`, gitignored. It is a symlink into the private dotfiles
  repo, so edits go to the link's target.

## Conventions

- Everything is English: comments, README, CHANGELOG and every message `djdojo` prints. The tool
  is for the global market.
- `djdojo` runs under macOS `/bin/bash` 3.2: no `${var^}`, no `mapfile`, guard empty arrays under
  `set -u` with `${arr[@]+"${arr[@]}"}`, put heredocs in functions rather than inside `$( )`.
  CI runs shellcheck, so quote everything, including `exit "$status"`.
- `djdojo.conf` starts from `--ignore-config` and is passed with `--config-locations`, so a user's
  own yt-dlp config never interferes. Arguments on the command line come after the config and win.
- A config option cannot be switched off from the command line, so everything that differs between
  AIFF and FLAC lives in `djdojo` (`format_args`), and `djdojo.conf` holds only what both share.
- `djdojo` is for one playlist per run: the pre-flight lookup and the final count use the first URL.

## Release

1. Move the `[Unreleased]` entries in `CHANGELOG.md` to `## [X.Y.Z] - YYYY-MM-DD`, add the link
   reference at the bottom, and set `version=X.Y.Z` in `djdojo`. Commit.
2. Tag and push by hand: `git tag vX.Y.Z && git push origin main vX.Y.Z`.
3. `release.yml` checks tag, version and changelog, creates the GitHub release and commits the new
   URL and sha256 to the tap with the `TAP_GITHUB_TOKEN` secret. Nothing else to do.

## yt-dlp facts that cost time to find

- `/` must be literal in an output template. A `/` produced inside a field is sanitized away.
- A `--parse-metadata` group that does not match leaves the field unset, so `%(a,b|x)s`
  fallbacks keep working. `album_dir` relies on this.
- `--audio-format` has no AIFF, so AIFF goes through `--recode-video aiff`.
- `--embed-thumbnail` cannot do AIFF either. `djdojo` passes `--write-thumbnail` for AIFF and embeds
  the .jpg with ffmpeg (`-write_id3v2 1`, attached pic). `--write-thumbnail` fetches thumbnails
  again for files that already exist, which is how older files get their cover on a rerun.
  `--output "pl_thumbnail:"` stops the playlist's own thumbnail from being written. FLAC uses
  `--embed-thumbnail`, which yt-dlp handles itself (mutagen).
- ffmpeg output options in `--ppa` use the `NAME+ffmpeg_o:` form.
- The default format sort ranks `quality` before codec. Without login `ba` picks Opus 251, which
  reaches 20 kHz. AAC 140 (128k) is cut at 16 kHz.
- With cookies, yt-dlp only adds the `web_music` client (Premium 256k audio) for
  `music.youtube.com` URLs. That is why `djdojo` expands bare IDs to music.youtube.com, and why the
  "YouTube Music is not directly supported" warning is expected.
- yt-dlp's "logged in" rule is `LOGIN_INFO` plus one of `SAPISID`, `__Secure-1PAPISID`,
  `__Secure-3PAPISID` on youtube.com. `find_login` in `djdojo` reuses `yt_dlp.cookies` through the
  Python in yt-dlp's own shebang, which is the one that has yt-dlp's modules.
- `--print` output is pre-postprocessing, so `%(filename)s` ends in `.webm`. Only its directory
  is used.
- `djdojo` reads `~/.config/djdojo/config` (`format=`, `folder=`, `login=`), written by its
  first-run questions. The questions, and the "download without login?" question, only run when
  stdin is a tty.

## Testing

- Tests never read browser cookies: macOS privacy protection blocks the browser folders for a
  non-interactive shell, and the permission system denies it anyway. Pass `nologin` in every
  test, or `djdojo` starts its login search.
- Test the login search (`find_login`) with fake data: a scratch copy of `djdojo` where the Python
  line points to a fake script and `yt-dlp` in `PATH` is a stub that prints its arguments.
- Run `djdojo` with `HOME=<scratch>` to test its config and first-run questions: the config, the
  default folder and `~` in messages then all land in the scratchpad. The questions need a tty, so
  drive them with `expect` (ships with macOS). Match prompts with `expect -ex {[1]: }`, in
  braces: double quotes make Tcl treat `[1]` as a command.
- Real test downloads go to the session scratchpad with `--paths <scratch>` and
  `--playlist-items 1`. Path checks without downloading use `--print filename`.
- Public test material: video `EIlaJ6VdbqM` (YouTube Music track with artist/album tags),
  playlist `UU97SO7kenN4qKp-_6u6FSLQ` (a channel's uploads) and the README example
  `PLRBp0Fe2GpgliIz-BjIwIBgysth8ZkE8c` (NoCopyrightSounds, 20 tracks, no artist/album tags, so
  the tags fall back to channel and playlist title). A playlist with an unavailable track, for
  the failure count, is in the private publish guide.
- To test the formula before a tag exists, make a tarball with `git archive`, copy the formula
  to the scratchpad with a `file://` URL and its sha256, and `brew install --formula <copy>`.
  Whatever `/opt/homebrew/bin/djdojo` is at the time must go first: `mv` the dev symlink aside,
  or `brew uninstall djdojo`. Put it back afterwards.
