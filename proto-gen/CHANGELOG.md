<!-- markdownlint-disable blanks-around-headings blanks-around-lists no-duplicate-heading -->

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!-- next-header -->
## [Unreleased] - ReleaseDate
## [0.3.0] - 2025-02-21
### Changed
- [PR#31](https://github.com/EmbarkStudios/proto-gen/pull/31) changed the `--format` flag to take a rust edition as an argument, as previously the 2021 edition was hardcoded.

## [0.2.11] - 2025-01-23
### Fixed
- [PR#30](https://github.com/EmbarkStudios/proto-gen/pull/30) Improvements and fixes to the readme.

## [0.2.10] - 2025-01-16
### Fixed
- Update dependency `anstream` to `0.6.15` for advisory: <https://rustsec.org/advisories/RUSTSEC-2024-0404>
- Build fixes that prevented v `0.2.9` from being fully released

## [0.2.9] - 2025-01-16
### Added
- [PR#27](https://github.com/EmbarkStudios/proto-gen/pull/27) Added `--prepend-header-file` option for specifying a file which will be prepended as a header in generated source files.
## [0.2.8] - 2024-10-24
### Fixed
- [PR#26](https://github.com/EmbarkStudios/proto-gen/pull/26) Include top module file when formatting
## [0.2.7] - 2024-08-05
### Added
- [PR#25](https://github.com/EmbarkStudios/proto-gen/pull/25) Added `--btree-map` option to output BTreeMaps instead of HashMaps.
## [0.2.6] - 2024-04-25
### Added
- [PR#23](https://github.com/EmbarkStudios/proto-gen/pull/23) Added `--enum-attribute` option to set enum attributes.
## [0.2.5] - 2024-03-13
### Fixed
- [PR#22](https://github.com/EmbarkStudios/proto-gen/pull/22) Various fixes for the `--prepend-header` option and some cases for escaped doc-tests.
## [0.2.4] - 2024-03-12
### Fixed
- [PR#21](https://github.com/EmbarkStudios/proto-gen/pull/21) Fix for handling multiline code blocks in comments and make them ignored for doc tests.
## [0.2.3] - 2024-03-12
### Added
- [PR#19](https://github.com/EmbarkStudios/proto-gen/pull/19) Added `-p, --prepend-header` option (default false) to prepend header indicating tool version in generated source file.
- [PR#20](https://github.com/EmbarkStudios/proto-gen/pull/20) Added `--toplevel-attribute` option to set toplevel module attribute.
## [0.2.2] - 2024-03-11
### Fixed
- [PR#17](https://github.com/EmbarkStudios/proto-gen/pull/17) Make errors slightly easier to read.
- [PR#18](https://github.com/EmbarkStudios/proto-gen/pull/18) Fix handling of filename for target .rs file when it's a keyword.
## [0.2.1] - 2024-03-01
### Added
- [PR#15](https://github.com/EmbarkStudios/proto-gen/pull/15) added the `-d, --disable-comments <path>` option to code generation, allowing comments to be [disabled](https://docs.rs/prost-build/latest/prost_build/struct.Config.html#method.disable_comments) for one or more proto paths.
## [0.2.0] - 2023-04-12
### Changed
- Tonic build upgraded to 0.10.2
## [0.1.2] - 2023-04-11
### Changed
- Tonic build upgraded to 0.9.1
### Fixed
- A bug where when multiple protos were part of the same chain
of packages they would not be put properly into modules, see <https://github.com/EmbarkStudios/proto-gen/issues/10>
### Fixed
- Escape most doc-tests that tonic generates as that is probably not valid Rust code
and will lead to failed cargo test, and if it is rust code, we definitely don't want to run it.
## [0.1.1] - 2023-04-04
### Added
- Correct cargo metadata
## [0.1.0] - 2023-04-04
### Added
- Initial creation of the proto-gen lib and proto-gen-cli

<!-- next-url -->
[Unreleased]: https://github.com/EmbarkStudios/proto-gen/compare/0.3.0...HEAD
[0.3.0]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.11...0.3.0
[0.2.11]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.10...0.2.11
[0.2.10]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.9...0.2.10
[0.2.9]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.8...0.2.9
[0.2.8]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.7...0.2.8
[0.2.7]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.6...0.2.7
[0.2.6]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.5...0.2.6
[0.2.5]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.4...0.2.5
[0.2.4]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.3...0.2.4
[0.2.3]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.2...0.2.3
[0.2.2]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.1...0.2.2
[0.2.1]: https://github.com/EmbarkStudios/proto-gen/compare/0.2.0...0.2.1
[0.2.0]: https://github.com/EmbarkStudios/proto-gen/compare/0.1.2...0.2.0
[0.1.2]: https://github.com/EmbarkStudios/proto-gen/compare/0.1.1...0.1.2
[0.1.1]: https://github.com/EmbarkStudios/proto-gen/compare/0.1.0...0.1.1
[0.1.0]: https://github.com/EmbarkStudios/proto-gen/releases/tag/0.1.0
