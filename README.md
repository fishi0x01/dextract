**Status**

[![CircleCI](https://circleci.com/gh/fishi0x01/docker-extract.svg?style=svg)](https://circleci.com/gh/fishi0x01/docker-extract)
[![crates.io badge](https://img.shields.io/crates/v/docker_extract.svg)](https://crates.io/crates/docker_extract)
[![docs.rs badge](https://docs.rs/docker_extract/badge.svg)](https://docs.rs/docker_extract)

# docker-extract

`docker-extract` is a rust library that extracts the filesystem from a docker image. 

Here is an example to extract `alpine:latest` to directory `./docker-fs`:
```rust
use docker_extract;
use std::path::Path;

fn main() {
    let image = "alpine";
    let tag = "latest";
    let to_dir = Path::new("./docker-fs");
    docker_extract::extract_image(image, tag, &to_dir);
}
```

## Security

This library relies on the [tar crate](https://github.com/alexcrichton/tar-rs), which is very conscious about security concerns. 
To prevent directory traversal issues, it will not unpack anything outside the specified output directory, i.e., paths with `..` in their name will not be unpacked.

Further, `docker-extract` does not extract symlinks to absolute paths, as they will point to wrong references anyways.

## Detailed extraction procedure

The following procedure describes what `docker-extract` does:

1. Run `docker save {image}:{tag} -o {tmp_dir}/image.tar`
2. Extract all layers from `${tmp_dir}/image.tar` to wanted result dir
3. Delete `${tmp_dir}`

It follows, that `docker-extract` needs access to `docker` and that `{image}:{tag}` is already pulled.

## crev

This crate has its author's [crev review](https://github.com/fishi0x01/crev-proofs).

It is recommended to always use [cargo-crev](https://github.com/crev-dev/cargo-crev)
to verify the trustworthiness of each of your dependencies, including this one.
