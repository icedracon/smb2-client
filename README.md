# smb2-client

[![crates.io](https://img.shields.io/crates/v/smb2-client.svg)](https://crates.io/crates/smb2-client)
[![docs.rs](https://img.shields.io/docsrs/smb2-client)](https://docs.rs/smb2-client)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A minimal, async, pure-Rust **SMB2 client** — **no FFI**, no `windows` crate — so it connects
to Windows file servers from any platform. Built on [`ntlmssp`](https://crates.io/crates/ntlmssp)
for authentication.

## Features

- SMB2 NEGOTIATE (multi-dialect: 2.0.2 → 3.x) and **NTLMv2 session setup** wrapped in SPNEGO,
  from a password or an NT hash (**pass-the-hash**).
- Message **signing**: HMAC-SHA256 (SMB 2.x) and AES-CMAC (SMB 3.0.x), with the SP800-108 KDF.
- TREE_CONNECT, named-pipe open/read/write (the transport under DCE/RPC-over-SMB), and
  disk-file read (for pulling command output back over `C$`).
- A small SMB2 **server** side (`server`) sufficient to stand up an NTLM capture endpoint.

## Example

```rust
use smb2_client::SmbClient;

# async fn run() -> Result<(), Box<dyn std::error::Error>> {
let mut c = SmbClient::connect("fileserver:445").await?;
c.login("fileserver", "CORP", "alice", "P@ssw0rd").await?;
c.tree_connect(r"\\fileserver\IPC$").await?;
let pipe = c.open_pipe("srvsvc").await?;   // now drive DCE/RPC over the pipe
# Ok(()) }
```

Pairs with [`dcerpc`](https://crates.io/crates/dcerpc) for SAMR / LSAT / DRSUAPI etc. over the
named-pipe transport — together they're the "impacket for Rust" that didn't previously exist.

## Scope

Client-focused SMB2 for automation/tooling (auth, signing, pipes, file read). Not a general
file-server or a full SMB3 encryption implementation. NTLMv2 only (no NTLMv1/LM).

## License

MIT © icedracon. Extracted from [ADhammer](https://github.com/icedracon/adhammer).
