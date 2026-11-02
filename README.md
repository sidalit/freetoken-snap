<div align="center">

<img src="snap/gui/freetoken.png" alt="FreeToken logo" width="128" />

# FreeToken

**Run large Mixture-of-Experts language models on a local NVIDIA GPU**

[![freetoken](https://snapcraft.io/freetoken/badge.svg)](https://snapcraft.io/freetoken)
[![Base: core24](https://img.shields.io/badge/Base-core24-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core24)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architectures: amd64](https://img.shields.io/badge/Architectures-amd64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for [FreeToken](https://github.com/FlashML-org/FreeToken), an edge-native Mixture-of-Experts serving engine with OpenAI and Anthropic-compatible HTTP APIs.

</div>

---

## Getting Started

<a href="https://snapcraft.io/freetoken"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

You need an NVIDIA GPU (RTX 30 series or newer) with driver 580 or newer. The CUDA runtime is included in the snap.

```bash
sudo snap install freetoken
freetoken serve --model Qwen/Qwen3.6-35B-A3B --port 1919
```

The server listens on `http://127.0.0.1:1919`. In a second terminal, chat with it or query it:

```bash
freetoken shell
freetoken ctl stats
```

Point any OpenAI or Anthropic-compatible client at `http://127.0.0.1:1919`. Stop the server with `Ctrl+C`.

To convert a Hugging Face checkpoint to FreeToken's FTW format:

```bash
freetoken checkpoint --model ~/models/<hf-dir> --out ~/models/<ftw-dir>
```

## Configure

Models downloaded from Hugging Face or ModelScope, and the Triton and CUDA caches, are kept in `~/snap/freetoken/common/.cache`, so they survive snap refreshes. Local models can be read from your home directory.

Some features need an interface that is not connected automatically:

| Feature | Command |
| --- | --- |
| Models on USB drives and other removable media (`/media`, `/mnt`) | `sudo snap connect freetoken:removable-media` |

### Background service

The snap includes an optional system service, disabled by default. Enable it once a model is set:

```bash
sudo snap set freetoken daemon.model=Qwen/Qwen3.6-35B-A3B daemon.enabled=true
```

| Key | Default | Description |
| --- | --- | --- |
| `daemon.enabled` | `false` | Start the service at boot and now |
| `daemon.model` | (unset) | Hugging Face model ID or local model directory |
| `daemon.host` | `127.0.0.1` | `127.0.0.1` or `0.0.0.0` |
| `daemon.port` | `1919` | Listening port |
| `daemon.api-key` | (unset) | API key required on every request; mandatory with `daemon.host=0.0.0.0` |

The service runs as root and keeps its downloads in `/var/snap/freetoken/common`. It cannot read home directories: put local models in `/var/snap/freetoken/common/models`, or on removable media once `removable-media` is connected. Follow its logs with `sudo snap logs -f freetoken.daemon`, and disable it with `sudo snap set freetoken daemon.enabled=false`.

## Limitations

- Only NVIDIA GPUs on amd64 are supported, as upstream publishes CUDA 13 wheels for x86_64 only.
- `freetoken launch` is unavailable: the strict snap cannot install or configure coding agents on the host. Start the server and point your agent at its HTTP API instead.
- Files outside your home directory and, once connected, removable media are inaccessible. Hidden folders directly under your home directory are not read either, so an existing `~/.cache/huggingface` cache is not reused; models are downloaded again into the snap's own cache.
- The snap is about 3 GB, mostly the bundled PyTorch and CUDA libraries.

## Build

```bash
git clone https://github.com/sidalit/freetoken-snap.git
cd freetoken-snap
sudo snap install snapcraft --classic
snapcraft pack
```

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/freetoken-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/FlashML-org/FreeToken/issues).

## License

Snap packaging: [MIT](https://github.com/sidalit/freetoken-snap/blob/main/LICENSE).
