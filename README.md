# aegis-proto

Protocol Buffer definitions for the Aegis Kubernetes operational intelligence platform.

This repository is the single source of truth for the gRPC contract between all Aegis components. It is public — the protocol is an open contract. Anyone building on the Aegis platform can read exactly what data flows between the agent and the control plane.

## Contents

```
aegis/
  v1/
    aegis.proto     ← current stable protocol definition
```

## Versioning

Versions are directories. Breaking changes increment the version:

- `v1/` — current stable
- `v2/` — future, when breaking changes are required

Existing agents continue working on `v1/` when `v2/` is introduced. There is no forced migration.

A change is breaking if it:
- Removes a field
- Changes a field type
- Changes a field number
- Renames an RPC method

A change is non-breaking (safe to make in `v1/`) if it:
- Adds a new optional field
- Adds a new RPC method
- Adds a new enum value

## Consumers

| Repository | Language | How it consumes |
|---|---|---|
| aegis (agent) | Rust | prost + tonic-build, compiled at build time via build.rs |
| aegis-cp (control plane) | Go | protoc-gen-go + protoc-gen-go-grpc, generated code committed |

## Generating code

### Go (control plane)

Prerequisites:
```bash
brew install protobuf
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

Generate:
```bash
protoc \
  --go_out=. \
  --go_opt=paths=source_relative \
  --go-grpc_out=. \
  --go-grpc_opt=paths=source_relative \
  aegis/v1/aegis.proto
```

### Rust (agent)

The agent uses `tonic-build` in `build.rs` — no manual `protoc` invocation needed. The proto file path is configured in `build.rs` and code is generated automatically at compile time.

## Related Repositories

- [aegis](https://github.com/yourusername/aegis) — eBPF agent (Rust, open source)
- aegis-cp — control plane (Go, private)
- aegis-ios — iOS app (Swift, private)
