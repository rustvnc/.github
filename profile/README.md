# RustVNC

> **High-performance, memory-safe VNC solutions built with Rust**

[![Rust](https://img.shields.io/badge/rust-1.90%2B-orange.svg)](https://www.rust-lang.org/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![License: GPL v2](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)

RustVNC provides a complete VNC (Virtual Network Computing) ecosystem implemented in Rust, offering superior memory safety, modern async architecture, and exceptional performance compared to traditional C/C++ implementations.

## Why RustVNC?

Traditional VNC servers are plagued by memory vulnerabilities (buffer overflows, use-after-free) and blocking I/O architectures. RustVNC solves these problems with:

- **Memory Safety**: Zero buffer overflows, use-after-free bugs, or data races thanks to Rust's ownership system
- **Modern Architecture**: Built on Tokio's async runtime for efficient concurrent connection handling
- **Performance**: Zero-copy framebuffer updates, SIMD-optimized encoding, and persistent compression streams
- **RFC Compliance**: Full RFC 6143 protocol implementation with 11 encoding types

## Our Projects

### [RustVNC](https://github.com/rustvnc/RustVNC) - Android VNC Server

**Android VNC remote desktop server** with a pure Rust VNC implementation.

- Screen sharing with configurable scaling
- Reverse VNC and UltraVNC repeater support
- Built-in noVNC web client for browser access
- Intent-based API for automation (Tasker, MacroDroid, etc.)
- Zeroconf/Bonjour auto-discovery
- Password protection and access control

**[Download →](https://github.com/rustvnc/RustVNC/releases)** | **[Documentation →](https://github.com/rustvnc/RustVNC#readme)**

---

### [rustvncserver](https://github.com/rustvnc/rustvncserver) - VNC Server Library

**Pure Rust VNC server library** for building custom VNC solutions.

```rust
use rustvncserver::VncServer;

let mut server = VncServer::new(1920, 1080);
server.listen("0.0.0.0:5900").await?;
server.update_framebuffer(&framebuffer_data).await?;
```

- Full RFC 6143 protocol support
- 11 encoding types (Raw, Tight, ZRLE, ZYWRLE, and more)
- Async/await API with Tokio
- Zero-copy architecture
- Available on [crates.io](https://crates.io/crates/rustvncserver)

**[Documentation →](https://github.com/rustvnc/rustvncserver#readme)** | **[Technical Details →](https://github.com/rustvnc/rustvncserver/blob/main/TECHNICAL.md)**

---

### [rfb-encodings](https://github.com/rustvnc/rfb-encodings) - RFB Encoding Library

**Reusable RFB protocol encoding implementations** for VNC clients, servers, and proxies.

- 10 encoding types including Tight (5 modes), ZRLE, ZYWRLE
- Persistent compression stream management
- Optional TurboJPEG hardware acceleration
- Pixel format translation (8/16/24/32-bit)
- Available on [crates.io](https://crates.io/crates/rfb-encodings)

**[Documentation →](https://github.com/rustvnc/rfb-encodings#readme)** | **[Technical Details →](https://github.com/rustvnc/rfb-encodings/blob/main/TECHNICAL.md)**

## Architecture

```
┌─────────────────────────────────────────┐
│   RustVNC (Android App)                 │
│   ├─ Kotlin UI                          │
│   └─ JNI Bridge                         │
└─────────────┬───────────────────────────┘
              │ uses
┌─────────────▼───────────────────────────┐
│   rustvncserver (Server Library)        │
│   ├─ Protocol Handling (RFC 6143)       │
│   ├─ Client Connection Management       │
│   └─ Async I/O (Tokio)                  │
└─────────────┬───────────────────────────┘
              │ uses
┌─────────────▼───────────────────────────┐
│   rfb-encodings (Encoding Library)      │
│   ├─ 10 Encoding Implementations        │
│   ├─ Compression Stream Management      │
│   └─ Pixel Format Translation           │
└─────────────────────────────────────────┘
```

Each layer is independently versioned, tested, and reusable across different VNC implementations.

## Performance Comparison

For a 1920×1080 framebuffer update:

| Encoding | Compression Ratio | Use Case |
|----------|------------------|----------|
| **Raw** | 1:1 (~8.3 MB) | LAN with unlimited bandwidth |
| **Tight JPEG** | 16-83:1 (100-500 KB) | General purpose, balanced quality |
| **ZYWRLE** | 10-55:1 (150-800 KB) | Smooth animations, lossy |
| **TightPng** | 8-40:1 (200-1 MB) | Lossless text/diagrams |
| **CopyRect** | ∞:1 (8 bytes) | Scrolling/dragging windows |

## Encoding Support

| Encoding | Type | RustVNC | rustvncserver | rfb-encodings | Client Support |
|----------|------|---------|---------------|---------------|----------------|
| Raw | 0 | ✅ | ✅ | ✅ | Universal |
| CopyRect | 1 | ✅ | ✅ | N/A | Universal |
| RRE | 2 | ✅ | ✅ | ✅ | Common |
| CoRRE | 4 | ✅ | ✅ | ✅ | Rare |
| Hextile | 5 | ✅ | ✅ | ✅ | Universal |
| Zlib | 6 | ✅ | ✅ | ✅ | Common |
| Tight | 7 | ✅ | ✅ | ✅ | Universal |
| ZlibHex | 8 | ✅ | ✅ | ✅ | Rare |
| ZRLE | 16 | ✅ | ✅ | ✅ | Universal |
| ZYWRLE | 17 | ✅ | ✅ | ✅ | TightVNC |
| TightPng | -260 | ✅ | ✅ | ✅ | Modern clients |

## Getting Started

### For Android Users
1. Download [RustVNC](https://github.com/rustvnc/RustVNC/releases) from releases
2. Grant screen capture permissions
3. Connect using any VNC client (or the built-in web client)

### For Rust Developers
```toml
[dependencies]
rustvncserver = "2.0"
rfb-encodings = "0.1"
```

See [rustvncserver documentation](https://github.com/rustvnc/rustvncserver#readme) for examples.

## Contributing

We welcome contributions! Each repository has detailed contributing guidelines:

- [RustVNC Contributing Guide](https://github.com/rustvnc/RustVNC/blob/master/CONTRIBUTING.md)
- [rustvncserver Contributing Guide](https://github.com/rustvnc/rustvncserver/blob/main/CONTRIBUTING.md)
- [rfb-encodings Contributing Guide](https://github.com/rustvnc/rfb-encodings/blob/main/CONTRIBUTING.md)

All projects follow the [Contributor Covenant Code of Conduct](https://github.com/rustvnc/rustvncserver/blob/main/CODE_OF_CONDUCT.md).

## Security

Security is a top priority. We have security policies in place:

- [rustvncserver Security Policy](https://github.com/rustvnc/rustvncserver/blob/main/SECURITY.md)
- [rfb-encodings Security Policy](https://github.com/rustvnc/rfb-encodings/blob/main/SECURITY.md)

**To report vulnerabilities**, email: dustin.mcafee@my.maryvillecollege.edu with subject "RustVNC Security"

## Roadmap

- [ ] iOS support via rustvncserver
- [ ] VNC client implementation
- [ ] VNC proxy/load balancer
- [ ] VNC session recording and playback
- [ ] Additional encoding optimizations (SIMD for ZRLE)
- [ ] WebRTC transport layer
- [ ] Wayland support improvements

## License

- **RustVNC**: GPL-2.0 (inherited from droidVNC-NG)
- **rustvncserver**: Apache-2.0
- **rfb-encodings**: Apache-2.0

## Community & Support

- **Issues**: Report bugs in the respective repository
- **Discussions**: [GitHub Discussions](https://github.com/orgs/rustvnc/discussions) (coming soon)
- **Email**: dustin.mcafee@my.maryvillecollege.edu

---

**Built with ❤️ in Rust** | [Website](https://rustvnc.github.io) | [Documentation](https://github.com/rustvnc)
