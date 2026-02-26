# crget

A Python script for downloading and extracting files from OCI/Docker container images directly via the registry API. It allows you to extract binaries or specific layers without installing a container runtime like Docker.

## Overview

This script interfaces with OCI-compliant registries to fetch image manifests, authenticate, and download layer blobs. It handles:
* **Direct API Extraction:** Downloads files using HTTP requests, bypassing the need for a local Docker daemon.
* **Endpoint Discovery:** Parses `Www-Authenticate` headers to dynamically locate authentication endpoints for standard registries (e.g., Docker Hub, GHCR, Quay).
* **Architecture Resolution:** Checks multi-architecture manifests and defaults to the host system's architecture (amd64/arm64).
* **Layer Selection:** Allows downloading specific layer indices.
* **Extraction Modes:** Extracts either a single binary file or unpacks an entire layer archive to a target directory.

## Prerequisites

* Python 3.6+
* `requests` package

```bash
pip install requests

```

## Usage

```bash
crget <image_url> [output_path] [options]

```

### Arguments

| Argument | Description |
| --- | --- |
| `image_url` | **Required.** The full OCI image reference (e.g., `docker.io/user/repo:tag` or `ghcr.io/user/repo`). Defaults to `latest` if the tag is omitted. |
| `output_path` | **Optional.** Target file path or directory. If omitted, the script saves to the current working directory using the repository name. |

### Options

| Flag | Description |
| --- | --- |
| `--ignore-arch` | Bypasses the host architecture check. If a multi-arch manifest is found, it defaults to the first available architecture in the list. |
| `--ignore-multi-layer` | Suppresses the error prompt when an image contains multiple layers. Useful when targeting a specific file in a multi-layered image. |
| `--layer <int>` | Specifies the layer index to download. Defaults to `-1` (the last layer). Use positive integers (e.g., `0`) to specify layers from the base up. |
| `--archive` | Extracts the entire contents of the target layer into the specified `output_path` directory, rather than extracting a single file. |
| `-h`, `--help` | Displays the help message and exits. |

## Examples

**Extract a single binary to the current directory:**

```bash
crget docker.io/angelkawaii/pills

```

**Extract a binary to a designated file path:**

```bash
crget ghcr.io/user/app:v1.2.3 /usr/local/bin/app

```

**Extract a complete layer archive to a directory:**
Requires the `--archive` flag if the layer contains multiple files (e.g., a base OS filesystem):

```bash
crget docker.io/alpine /tmp/alpine-rootfs --archive --ignore-multi-layer

```

**Download a specific layer from a multi-layer image:**

```bash
crget [registry.example.com/tool:latest](https://registry.example.com/tool:latest) /tmp/layer --layer 0 --archive

```

**Bypass architecture mismatch:**
Forces the download even if the host architecture (e.g., ARM64) does not match the requested image (e.g., AMD64):

```bash
crget docker.io/user/amd64-only-app /tmp/app --ignore-arch

```

## AI Disclosure

This project and its documentation were created with the assistance of AI autocompletion tools.