# Docker Publish Action

Build and publish Docker images to **Docker Hub** with a **safe, opinionated tagging strategy**, **multi-platform support**, and **zero boilerplate**.

> This action is designed to prevent common release mistakes such as accidentally pushing `latest` for prereleases.

## ✨ Features

- 🧱 **Build once, push multiple tags**
- 🏷 **Smart SemVer-based tagging**
- 🧬 **Multi-platform images**

  - `linux/amd64`
  - `linux/arm64`

- 🔄 Supports versions without `v` prefix (`v1.2.3` → `1.2.3`)
- ⚡ Uses Docker **Buildx + QEMU**
- 🧩 Composite action (transparent & easy to audit)
- 📦 Docker Hub compatible

> [!IMPORTANT]
>
> ## CI/CD Runner Requirement
>
> Recommended Runner: `ubuntu-latest`
>
> This action must be executed on a Linux GitHub Actions runner.
>
> ```yaml
> runs-on: ubuntu-latest
> ```
>
> **Why Linux runners?**
>
> - Docker image builds require **Linux kernel features**.
> - GitHub-hosted **macOS** and **Windows runners do not provide Docker Desktop**.
> - Docker Desktop (used locally on macOS/Windows) cannot run inside CI runners.
> - Linux runners provide a **native Docker daemon** required by **Buildx**.
>
> **What this means**
>
> - Builds run on Linux CI runners.
> - Built images run on Linux, macOS, and Windows via Docker Desktop or WSL2.
> - macOS / Windows runners are not supported for building.
>
> This is the standard and recommended setup for Docker-based CI/CD workflows.

## 🚀 Quick Start

```yaml
- uses: teneplaysofficial/docker-publish@v1
  with:
    image_repo: tenedev/release-hub
    version: v1.2.4
    docker_username: ${{ secrets.DOCKERHUB_USERNAME }}
    docker_password: ${{ secrets.DOCKERHUB_TOKEN }}
```

## 🏷 Tagging Strategy (Important)

The action determines tags **only from the version string**.

### 🟢 Stable Release (no `-`)

**Example**

```text
1.2.3
v1.2.4
```

**Tags pushed**

```text
:1.2.4
:latest
:1
```

### 🔴 Numeric Prerelease → `next`

**Example**

```text
1.2.3-1
1.2.3-34
```

**Tags pushed**

```text
:1.2.3-34
:next
```

### 🟡 Labeled Prerelease → label tag

**Example**

```text
1.2.3-beta.2
1.2.3-alpha
1.2.3-rc.1
```

**Tags pushed**

```text
:1.2.3-beta.2
:beta
```

```text
:1.2.3-alpha
:alpha
```

```text
:1.2.3-rc.1
:rc
```

❌ `latest` is **never** pushed for prereleases
❌ Major tags are **only for stable releases**

## 🧬 Multi-Platform Support

By default, images are built for:

```text
linux/amd64
linux/arm64
```

## Image runtime support

These images run on:

- Linux servers (native)
- macOS (Docker Desktop)
- Windows (Docker Desktop / WSL2)

## ⚙️ Inputs

| Name              | Required | Default        | Description                            |
| ----------------- | -------- | -------------- | -------------------------------------- |
| `image_repo`      | ✅       | —              | Docker image repo (`username/repo`)    |
| `version`         | ✅       | —              | App version (`1.2.3`, `v1.2.3-beta.2`) |
| `docker_username` | ✅       | —              | Docker Hub username                    |
| `docker_password` | ✅       | —              | Docker Hub token/password              |
| `context_path`    | ❌       | `.`            | Docker build context                   |
| `dockerfile_path` | ❌       | `./Dockerfile` | Path to Dockerfile                     |

## 🧪 Full Example Workflow

```yaml
name: Docker Release

on:
  push:
    tags:
      - "v*"

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: teneplaysofficial/docker-publish@v1
        with:
          image_repo: tenedev/release-hub
          version: ${{ github.ref_name }}
          docker_username: ${{ secrets.DOCKERHUB_USERNAME }}
          docker_password: ${{ secrets.DOCKERHUB_TOKEN }}
```

> The action automatically strips the leading `v` from Git tags.

## 🛡 Why This Action?

Most Docker workflows:

- Push `latest` accidentally
- Rebuild per tag
- Don’t support ARM
- Copy-paste huge YAML blocks

This action:

- Encodes **safe defaults**
- Keeps workflows **short**
- Follows **real SemVer rules**
- Scales cleanly across projects

## 🔍 Security & Transparency

- Uses **official Docker GitHub Actions**
- No bundled binaries
- No Node.js runtime
- No compiled artifacts
- Fully auditable YAML + Bash

## 🙌 Contributing

Issues and PRs are welcome.
This action is intentionally **small, focused, and predictable**.
