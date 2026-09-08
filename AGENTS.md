# ClipsAI Agent Guide

## Project shape

- This is a Python package for transcript-driven video clipping and aspect-ratio resizing; Python 3.9+ is required.
- The supported public API is re-exported from [`clipsai/__init__.py`](clipsai/__init__.py). Prefer those exports for user-facing changes.
- Keep implementation changes in the owning subpackage:
  - `clipsai/clip/`: transcript-based clip discovery.
  - `clipsai/transcribe/`: WhisperX transcription and timestamped text elements.
  - `clipsai/resize/`: speaker-aware crop generation and resizing.
  - `clipsai/diarize/`: Pyannote speaker diarization.
  - `clipsai/media/`: media types, ffprobe inspection, and ffmpeg editing.
  - `clipsai/filesys/` and `clipsai/utils/`: filesystem and shared support code.

## Development commands

- Create and activate a virtual environment before installing dependencies.
- Install the package and development tools with `python -m pip install -e ".[dev]"`.
- Install WhisperX separately as documented in [`README.md`](README.md).
- Run tests with `pytest`.
- Check formatting and lint with `black --check .` and `flake8 .`; format with `black .` when needed.
- Black and Flake8 use an 88-column limit. Project-specific exclusions and ignored rules are in [`setup.cfg`](setup.cfg); pytest options are in [`pytest.ini`](pytest.ini).

## Runtime and test prerequisites

- Media operations require `ffmpeg`, `ffprobe`, and `libmagic` to be installed and available to the process. On Windows, verify the executables are on `PATH`.
- Resizing requires a Hugging Face token accepted by Pyannote and can require substantial GPU or memory resources.
- `test_files/` is ignored and must be present for the file and media tests that use its fixtures. Do not assume those tests are runnable in a fresh checkout without the assets.
- Importing `clipsai` loads heavyweight ML dependencies. `transcription` code may also trigger an NLTK `punkt` download, so keep optional environment failures distinct from algorithm failures.

## Change and test guidance

- Preserve the root public API unless a change explicitly requires a public contract update.
- Before changing module import paths, inspect [`conftest.py`](conftest.py) and related tests: it adds `clipsai/` to `sys.path`, and some tests patch legacy top-level module names.
- Prefer focused unit tests for algorithms and mocked external processes; use real media fixtures only when testing integration with the media toolchain.
- Read the relevant package implementation and neighboring tests before changing behavior. Keep unrelated formatting and refactors out of the change.

For installation, usage, and external service setup, refer to [`README.md`](README.md) rather than duplicating that documentation here.