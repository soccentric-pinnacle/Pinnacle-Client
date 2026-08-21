# Pinnacle-Client

## Project Summary

Pinnacle-Client is a C++17 embedded-Linux daemon that coordinates hardware validation between Pinnacle-HAL and Pinnacle-Server.

The application wiring in `src/app.cpp` assembles:

- Configuration and logging.
- The adapter registry and run orchestrator.
- The local result spool and gRPC services.

The client accepts server commands, invokes module tests, serializes results, retains results locally in SQLite, uploads results and logs, and exposes a local HAL gRPC service for SDK callers.

The orchestrator separates single-shot, long-running, monitoring, and event-based execution. The registry maps module identifiers to adapters and falls back to a stub result for unsupported hardware.

The source contains adapters for the module families listed in `protos/registry/modules.yaml`, including compute, memory, thermal, networking, connectivity, peripherals, sensors, audio/video, system, safety, debug, and specialized hardware.

It also contains configuration parsing, durable spool handling, log collection, result conversion, server streaming, and a systemd unit. CMake builds the daemon and links the shared HAL contract; the Makefile provides native, Docker, and cross-build routes.

Focused tests include `tests/test_run_orchestrator.cc` for execution sequencing and `tests/test_result_serializer.cc` for result serialization.

The repository does not prove:

- Live board coverage.
- Real Server interoperability.
- Successful execution for every adapter.

Unsupported modules intentionally return `SKIPPED`. Probes depend on kernel interfaces, permissions, network access, and `/var/lib/pinnacle`.

Use `make build`, `make test`, and CTest locally. Then validate the installed daemon and systemd service on a Pinnacle target.

## Feature Status

| # | Name / Description | Implemented? | Test Coverage? | Manual Test |
|---:|---|---|---|---|
| 1 | C++17 daemon startup, configuration, and logging | Yes | Build and source wiring; focused runtime tests are limited | [ ] Manual verification required |
| 2 | Adapter registry for platform hardware modules | Yes | Registry and orchestrator paths exist; probes are not comprehensive | [ ] Manual verification required |
| 3 | Single-shot and streamed run orchestration | Yes | `test_run_orchestrator.cc` | [ ] Manual verification required |
| 4 | Result serialization and durable spool handling | Partial | `test_result_serializer.cc`; recovery is not fully covered | [ ] Manual verification required |
| 5 | Bidirectional gRPC communication with Server | Yes | Service code builds; live interoperability is unproven | [ ] Manual verification required |
| 6 | Stub results for unsupported modules | Yes | Fallback is implemented; no dedicated test identified | [ ] Manual verification required |
| 7 | Systemd, Docker, and cross-build packaging | Partial | Metadata exists; target installation is unverified | [ ] Manual verification required |

## Tests and Commands

```bash
make build
make test
ctest --test-dir build --output-on-failure
```

## Limitations

Meaningful validation requires all of the following:

- Pinnacle hardware.
- Deployed permissions.
- A reachable Server.
- The matching HAL installation.
