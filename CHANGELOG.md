# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - TBD

_This is the planned next release. A date will be inserted at publish time._

### Performance

- **Much faster picture compositing (`combine()`)** — replaced the per-pixel
  `putpixel`/`getpixel` loop with a single C-accelerated `qr.paste` driven by a
  reserved-region mask (pure Pillow, zero new dependencies), with pixel-identical
  output. Static compositing speeds up by roughly 3–5× for small versions and
  about 40–50× for large ones; GIF compositing
  benefits equally since each frame runs the same `combine()` path.

### Added

- Benchmark suite (`benchmarks/`) built on `pytest-benchmark`, covering pure QR
  generation, static picture compositing, and GIF multi-frame compositing, with
  saved local baselines for regression tracking.
- Tests, lint (ruff), formatting, and CI for the QR-spec pipeline and the
  layout coupling between `draw.py` and `combine()`.

### Changed

- Layout constants (`PIXELS_PER_MODULE`, `QUIET_ZONE_MODULES`, `PASTE_OFFSET_PX`,
  `FINDER_REGION_*`, `TIMING_MODULE`, …) extracted to `amzqr/mylibs/constant.py`
  and shared by `draw.py` and `combine()`, removing scattered magic numbers.
- Dependency cleanup: Pillow pinned to `>=8.0.0,<12`; dropped `numpy` and
  `imageio`; removed the legacy `requirements.txt`.
- CLI: added `-V, --version` to show the package version; the QR-version long
  option is now `--qr-version` (short `-v` unchanged).

### Fixed

- Single-character alphanumeric input crashing with `UnboundLocalError`.
- Oversized content silently generating a QR with a stale version; now raises a
  descriptive `ValueError`.
- GIF output flattening per-frame durations; durations are preserved and the
  `imageio` dependency is removed.
- Global shared temporary directory (`~/.myqr`) racing between processes; now a
  per-process `tempfile.TemporaryDirectory`.
- Non-square backgrounds distorted during compositing; aspect ratio is now
  preserved with a center-crop.
- Fragile `picture[-4:]` extension checks; replaced with `os.path.splitext`
  (case-insensitive, `.jpeg` accepted).
- Dark module flipped by the mask, violating ISO/IEC 18004 §7.3.4.
- Residual bare `except: raise` noise in the CLI entry point.

## [0.0.1] - 2021-04-06

_Initial published release on PyPI. Original MyQR name, all MyQR-era
development is in this release._

- Core QR-code generation (versions 1–40, error correction levels L/M/Q/H).
- Artistic QR codes: background picture synthesis with per-pixel alpha,
  contrast and brightness enhancement.
- Animated GIF QR codes.