# GitHub Release Checklist — CAU OS

Use this checklist before publishing or replacing a GitHub Release asset.

## Repository

- [ ] `VERSION` matches the application binary version.
- [ ] `PACKAGE-VERSION` identifies the packaging revision without pretending the binary was rebuilt.
- [ ] `README.md`, `SECURITY.md`, release notes and deployment docs agree on the default bind address.
- [ ] No production documents, logs, databases, backups, TLS keys, passwords or tokens are present.
- [ ] `.gitignore` protects runtime content and local secrets.
- [ ] GitHub Actions `Release integrity` passes.

## Binary

- [ ] SHA-256 of `CAU-OS.exe` matches `SHA256SUMS.txt`.
- [ ] Build provenance/toolchain is documented.
- [ ] Authenticode status is documented truthfully.
- [ ] Defender/EDR/SmartScreen has been tested in the target Windows environment.

## Production gate

A public GitHub package can be publishable while still being unsuitable for corporate production.
For production, additionally require:

- [ ] rebuild from the original source with a currently supported Go toolchain;
- [ ] `go test ./...`, `go test -race ./...`, `go vet ./...` and smoke tests repeated on that source/build;
- [ ] Windows runtime validation;
- [ ] TLS + firewall/VLAN configuration;
- [ ] ACL/service-account review;
- [ ] code signing or formal allowlisting where required.

The current v2.0.0 hardened package does **not** satisfy the rebuild/source gate because the source tree is not part of this distribution.
