# Changelog

All notable changes to `smb2-client` will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
project adheres to [SemVer](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.2.1] — 2026-08-06

### Added
- `TCP_NODELAY` on the SMB2 transport socket — reduces round-trip
  latency for the many-small-PDU workloads dcerpc drives.

### Changed
- Trimmed the `reg save` polling timeout to 3 s (was longer) — SMB
  `C$` hive-file read polls fail faster on hardened DCs where the
  save silently never completes.

## [0.2.0] — 2026-08-02

### Added
- **Optional SOCKS5 egress** for all SMB dials. `SmbClient::connect`
  routes through an upstream proxy when the `SOCKS5_PROXY` env is
  set, so downstream `dcerpc` clients (RPC over TCP + SMB named
  pipes) pivot cleanly through a compromised jump host.

## [0.1.0] — 2026-07-28

Initial release: minimal async SMB2 client (dialect 2.1.0) in pure Rust.

### Added
- SMB2 negotiate + NTLMv2 (SPNEGO / GSSAPI) session setup.
- Tree connect to `IPC$`.
- Named-pipe open / read / write / close on IPC$ pipes (`\lsarpc`,
  `\samr`, `\srvsvc`, `\wkssvc`, `\atsvc`, `\winreg`, ...).
- rustls default (no OpenSSL dependency) for fully-static musl builds.
- Live-validated against Server 2016 / 2019 / 2022 / 2025.
