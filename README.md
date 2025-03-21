<!-- Allow this file to not have a first line heading -->
<!-- markdownlint-disable-file MD041 no-emphasis-as-heading -->

<!-- inline html -->
<!-- markdownlint-disable-file MD033 -->

<div align="center">

# `💠 proto-gen`

**[Protobuf](https://protobuf.dev/) to [Rust](https://www.rust-lang.org/) code generation using [tonic-build](https://docs.rs/tonic-build/latest/tonic_build/)**

[![Embark](https://img.shields.io/badge/embark-open%20source-blueviolet.svg)](https://embark.dev)
[![Embark](https://img.shields.io/badge/discord-ark-%237289da.svg?logo=discord)](https://discord.gg/dAuKfZS)
[![Crates.io](https://img.shields.io/crates/v/proto-gen.svg)](https://crates.io/crates/proto-gen)
[![dependency status](https://deps.rs/repo/github/EmbarkStudios/proto-gen/status.svg)](https://deps.rs/repo/github/EmbarkStudios/proto-gen)
[![Build status](https://github.com/EmbarkStudios/proto-gen/workflows/CI/badge.svg)](https://github.com/EmbarkStudios/proto-gen/actions)
</div>

## What
The repo contains a cli tool that uses [tonic-build](https://docs.rs/tonic-build/latest/tonic_build/) to generate rust-code from protobuf-files.  
[tonic-build](https://docs.rs/tonic-build/latest/tonic_build/) already does this, the cli is a front-end to 
that with some added code to make sure that the generated files are placed in a valid path, and takes care of the 
module structuring.

## Why
[prost-build](https://docs.rs/prost-build/latest/prost_build/) used to ship with `cmake` which we would like to avoid.  
`cmake` was used to build `protoc` which was then used for the proto-to-rust codegen.  
The final decision from the prost maintainers side is that the user should provide their own `protoc` and check in the code
instead of building it in a `build.rs`, to make that process simpler, therefore this cli was created.  

## How
Uses [a local version of protoc on path](https://grpc.io/docs/protoc-installation/) to generate rust code from a 
workspace specification passed to the CLI. The CLI can also be used to validate the generated Rust code against source 
protobuf files, for example, in CI.  

## Considerations
There are quite a few problems with this tool, for now it remains the best alternative for our protobuf pipeline, 
some things to take into account follows.  

### Requires manual installation on the user's machine
Running this tool requires at minimum `protoc` to be installed on the user's machine, 
many protocol buffers found in the wild have dependencies in the form of other `.proto` files. Which also 
have to be sourced.  
Many `.proto` files that you might want to build have dependencies on the `google.protobuf` 
[types](https://protobuf.dev/reference/protobuf/google.protobuf/), 
many `.proto` authors assume that these are available to the user, which may or may not be true.  

There has been discussions about vendoring them here since they are so common but for now no vendoring will be done, 
since they are not mandatory in many cases. 

A middle-ground is to vendor the dependency protos yourself, and run this tool with the directory where you placed those 
`.proto` files specified (`-d my-dep-dir`).  

### Version incompatibility if tonic version has changed (even minor versions)
On tonic minor versions sometimes comments or other non-functional changes are made, these will cause the 
`validate` command to reject old protos generated by a lower version of this tool, requiring regeneration.  
It could be argued that therefore a minor version change in `tonic` becomes a major version change 
for this tool, although that may be a bit philosophical since the generated code runs the same.  


### Doc comments are executed
Tonic converts protobuf comments to doc comments, if these include code examples, cargo will attempt to execute them
when running `cargo test` (`doc-test` stage).  
This behaviour is different from using [tonic-build](https://docs.rs/tonic-build/latest/tonic_build/), since those doc-comments are generated where cargo doesn't try 
to run them.  
Running them is almost always guaranteed to fail in the best case (because they will be non-rust code or not code at all), 
and be a lurking security vulnerability in the worst case where someone have inserted malicious Rust-code in a doc-comment.  

Ideally it'd be easier to disable running doc-test on a per-module basis. Placing generated protos in a separate lib 
which doesn't run doc-comments is a safer choice, although doc-tests being opt-in would be significantly safer.  

## Usage 

### Install
To install `proto-gen` from source do the following. This assumed you have installed a recent [rust toolchain](https://www.rust-lang.org/tools/install).
```
cargo install --locked proto-gen
```

### Command-line options

Usage:

```
proto-gen [OPTIONS] <COMMAND>
```

#### Commands:

The top-level commands are:

- `validate` Generate new Rust code for proto files, checking current files for differences. Returns error code 1 on any found differences.
- `generate` Generate new Rust code for proto files, overwriting old files if present.
- `help` Print this message or the help of the given subcommand(s).

`validate` & `generate` share the following sub-options:

-  `-d`, `--proto-dirs` `<PROTO_DIRS>` Directories containing proto files to source (Ex. Dependencies), needs to include any directory containing files to be included in generation.
- `-f`, `--proto-files` `<PROTO_FILES>` The files to be included in generation.
- `-t`, `--tmp-dir` `<TMP_DIR>` Temporary working directory, if left blank, `tempfile` is used to create a temporary directory.
- `-o`, `--output-dir` `<OUTPUT_DIR>` Where to place output files. Will get cleaned up (all contents deleted). A module file will be placed in the parent of this directory.
- `-h`, `--help` Print help.

#### Options:
The top-level options are:

- `-s`, `--build-server` Whether to build server code.
- `-c`, `--build-client` Whether to build client code.
- `--generate-transport` Whether to generate the `::connect` and similar functions for tonic.
- `-d`, `--disable-comments` `<DISABLE_COMMENTS>` Disable comments based on proto path. Passing `'.'` disables all comments.
- `-b`, `--btree-map` `<BTREE_MAPS>` Output maps as `BTreeMap` instead of `HashMap`. Passing `'.'` makes all maps `BTreeMap`.
- `--type-attribute` `<TYPE_ATTRIBUTES>` Type attributes to add.
- `--enum-attribute` `<ENUM_ATTRIBUTES>` Enum attributes to add.
- `--client-attribute` `<CLIENT_ATTRIBUTES>` Client mod attributes to add.
- `--server-attribute` `<SERVER_ATTRIBUTES>` Server mod attributes to add.
- `-f`, `--format` Use `rustfmt` on the code after generation, `rustfmt` needs to be on the path.
- `-p`, `--prepend-header` Prepend header indicating tool version in generated source files.
- `--prepend-header-file` `<PREPEND_HEADER_FILE>` Prepend header file in generated source files.
- `--toplevel-attribute` `<TOPLEVEL_ATTRIBUTE>` Toplevel mod attribute to add.
- `-h`, `--help` Print help.
- `-V`, `--version` Print version.

### Examples in this project
This will generate Rust code from the proto specified in `examples/example-project/proto/my-proto.proto` and place it 
in `examples/example-project/src/proto_types`.

```bash
proto-gen generate \
  -d examples/example-project/proto \
  -f examples/example-project/proto/my-proto.proto \
  -o examples/example-project/src/proto_types
```

This will also generate Rust code (to a temporary directory) and the run a diff against the code contained in `examples/example-project/src/proto_types`. 
If it finds any diffs it will exit with error code 1 and a message.

```bash
proto-gen validate \
  -d examples/example-project/proto \
  -f examples/example-project/proto/my-proto.proto \
  -o examples/example-project/src/proto_types
```

If we want to use includes, the directory to include needs to be specified. Here we're passing `-d` twice, once to include the dependency protos, and one to include the protos we want to generate.  

```bash 
proto-gen generate \
  -d examples/example-project/dep_protos \
  -d examples/example-project/proto_with_deps \
  -f examples/example-project/proto_with_deps/my-proto.proto \
  -o examples/example-project/src/proto_types
```

The same applies for validate.

```bash 
proto-gen validate \
  -d examples/example-project/dep_protos \
  -d examples/example-project/proto_with_deps \
  -f examples/example-project/proto_with_deps/my-proto.proto \
  -o examples/example-project/src/proto_types
```

## Contributing

[![Contributor Covenant](https://img.shields.io/badge/contributor%20covenant-v1.4-ff69b4.svg)](CODE_OF_CONDUCT.md)

We welcome community contributions to this project.

Please read our [Contributor Guide](CONTRIBUTING.md) for more information on how to get started.
Please also read our [Contributor Terms](CONTRIBUTING.md#contributor-terms) before you make any contributions.

Any contribution intentionally submitted for inclusion in an Embark Studios project, shall comply with the Rust standard licensing model (MIT OR Apache 2.0) and therefore be dual licensed as described below, without any additional terms or conditions:

### License

This contribution is dual licensed under EITHER OF

- Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or <http://www.apache.org/licenses/LICENSE-2.0>)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or <http://opensource.org/licenses/MIT>)

at your option.

For clarity, "your" refers to Embark or any other licensee/user of the contribution.
