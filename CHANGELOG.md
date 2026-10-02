# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

### Changed

### Deprecated

### Removed

### Fixed

## [v1.1.0] - 2026-10-02

### Added

- `product:status`
- `product:quality_status`
- Guidance on when to use `product:status` and when to use `order:status`

### Deprecated

- `qualitydegraded` value for `product:status`. Use `product:quality_status` with the value `degraded` instead.

### Fixed

- JSON Schema: `product:timeliness_category` and `product:acquisition_type` are now validated in Collection summaries
- Invalid schema references in the examples and the validation scripts ([#14](https://github.com/stac-extensions/product/issues/14))
- Broken link to the Sentinel-2 Cal/Val documentation in the README

[v1.0.0] - 2025-06-22

### Added

- `product:acquisition_type`

### Changed

- Added `minLength: 1` constraint for `product:timeliness_category`

[v0.1.0] - 2024-05-22

- First release

[Unreleased]: <https://github.com/stac-extensions/product/compare/v1.1.0...HEAD>
[v1.1.0]: <https://github.com/stac-extensions/product/compare/v1.0.0...v1.1.0>
[v1.0.0]: <https://github.com/stac-extensions/product/compare/v0.1.0...v1.0.0>
[v0.1.0]: <https://github.com/stac-extensions/product/compare/v0.1.0>
