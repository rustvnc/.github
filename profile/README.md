# RustVNC

> **Production-ready Rust libraries for VNC protocol implementation**

[![Rust](https://img.shields.io/badge/rust-1.90%2B-orange.svg)](https://www.rust-lang.org/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

RustVNC is an ecosystem of Rust libraries for implementing the VNC (Virtual Network Computing) protocol. Build VNC servers, clients, proxies, and recording tools with memory safety, modern async I/O, and full RFC 6143 compliance.

## Why Rust for VNC?

Traditional VNC implementations in C/C++ suffer from memory vulnerabilities and blocking I/O. Our Rust libraries provide:

- **Memory Safety**: Zero buffer overflows, use-after-free bugs, or data races thanks to Rust's ownership system
- **Modern Architecture**: Built on Tokio's async runtime for efficient concurrent connection handling
- **Performance**: Zero-copy framebuffer updates, SIMD-optimized encoding, and persistent compression streams
- **RFC 6143 Compliance**: Full protocol implementation with 11 encoding types
- **Reusability**: Modular design - use what you need, from encoding algorithms to complete server implementations

## Core Libraries

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

---

## Example Implementation

### [RustVNC](https://github.com/rustvnc/RustVNC) - Android VNC Server

A production Android app demonstrating rustvncserver in action:

- Screen sharing with configurable scaling
- Reverse VNC and UltraVNC repeater support
- Built-in noVNC web client for browser access
- Intent-based API for automation (Tasker, MacroDroid, etc.)
- Zeroconf/Bonjour auto-discovery
- Password protection and access control

**[Download →](https://github.com/rustvnc/RustVNC/releases)** | **[Documentation →](https://github.com/rustvnc/RustVNC#readme)**

---

## Getting Started

### For Rust Developers
```toml
[dependencies]
rustvncserver = "2.0"
rfb-encodings = "0.1"
```

See [rustvncserver documentation](https://github.com/rustvnc/rustvncserver#readme) for API examples and usage.

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

Library enhancements:
- [ ] VNC client library implementation
- [ ] VNC proxy/load balancer library
- [ ] VNC session recording and playback utilities
- [ ] Additional encoding optimizations (SIMD for ZRLE)
- [ ] H.264/H.265 encoding support

Example implementations:
- [ ] Desktop VNC client
- [ ] Web-based VNC proxy

## License

**Core Libraries**: Apache-2.0
- rustvncserver
- rfb-encodings

**Example Implementations**: GPL-2.0
- RustVNC Android app (inherited from droidVNC-NG)

## Community & Support

- **Issues**: Report bugs in the respective repository
- **Discussions**: [GitHub Discussions](https://github.com/orgs/rustvnc/discussions) (coming soon)
- **Email**: dustin.mcafee@my.maryvillecollege.edu

---

**Built with ❤️ in Rust** | [Website](https://rustvnc.github.io) | [Documentation](https://github.com/rustvnc)
