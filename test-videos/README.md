# Test videos (generated, not committed)

The MP4 samples in this directory are **generated at test setup time** by
`openreel-video/scripts/generate-test-videos.mjs` and are gitignored (the root
`.gitignore` ignores `*.mp4` globally; the local `.gitignore` here also covers
`manifest.json`). Do not commit binaries — regenerate them.

## Why generated, not committed

- Repo convention: generated media and golden frames are run artifacts and
  must not be committed (see the `tests/fixtures/canonical_parity/` block in
  the root `.gitignore`).
- The samples are deterministic: `testsrc2` and the fixed `geq` expression
  have no seeds or time-of-day inputs, and libx264 output is deterministic
  for a fixed build + settings.
- Samples are generated **on the test machine**, so any cross-machine encoder
  drift never crosses a comparison boundary — the parity suite decodes the
  same local file through both render paths.

## Regenerating

```bash
cd openreel-video
node scripts/generate-test-videos.mjs          # generate missing files
node scripts/generate-test-videos.mjs --force  # regenerate all
node scripts/generate-test-videos.mjs --check  # verify presence (CI gate)
# or: pnpm test-videos:generate
```

Requires `ffmpeg` and `ffprobe` on PATH. Playwright runs the generator
automatically from `tests/rendering-parity/global-setup.ts` when samples are
missing.

## Samples

| File | Content |
|---|---|
| `testsrc2-640x360-30fps-baseline.mp4` | testsrc2, H.264 baseline profile, no audio |
| `testsrc2-640x360-30fps-high.mp4` | testsrc2, H.264 high profile, no audio |
| `testsrc2-640x360-30fps-high-audio.mp4` | testsrc2 + 440Hz sine, H.264 high + AAC |
| `moving-gradient-640x360-30fps-high.mp4` | moving RGB gradient (fixed `geq` expression), H.264 high |

All are 640x360 @ 30fps, ~4s, yuv420p, faststart. `manifest.json` records
sha256 + size per file for debugging.
