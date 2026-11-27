# Pinnacle-Client Build and Deployment

## Purpose and scope

Pinnacle-Client is the C++17 embedded-Linux validation daemon. It builds a native or ARM64 Docker/Ninja test application, links against the sibling `Pinnacle-HAL` protobuf and library sources, and can copy built `lib/` and `bin/` trees to configured target devices. The repository also contains a systemd unit, runtime configuration under `conf/`, and Doxygen/CMake tooling. This guide covers the commands implemented by the root `Makefile`; it does not claim live board or Server interoperability.

## 1. Prerequisites

- Linux host with GNU Make, CMake, Ninja, a C++17 compiler, `nproc`, and the libraries/tools required by `CMakeLists.txt`.
- A checkout layout with `Pinnacle-Client` beside `Pinnacle-HAL`; the Docker mount expects the parent `Pinnacle/` directory.
- For Docker targets: Docker with an ARM64-capable runtime/QEMU. `make docker-install` may use `sudo apt` when Docker is absent.
- For formatting/static analysis/docs: `clang-format`, `cmake-format`, `cppcheck`, `doxygen`, and `graphviz` as applicable.
- For deployment: `sshpass`, SSH/SCP access to the target, and a reachable target at `IP_A` or `IP_B`. The Makefile defaults to user `root` (key-based SSH, no password) and those two addresses; these are placeholders, not credentials to reuse in production.

## 2. Setup

From this repository directory, inspect available targets with:

```bash
make help
```

Native configuration creates `build/native`. Docker cross builds create `build/cross` and use the `pinnacle` image. Override parallelism with `make JOBS=4 build` if the host is resource constrained. `make setup-completion` appends this checkout's completion script to `~/.bashrc`.

## 3. Build and compile

Native CMake/Ninja build:

```bash
make configure
make build
```

`make build` configures with `-DBUILD_TESTS=ON` and then runs Ninja. The default `make` is an alias for native `build`.

ARM64 Docker build (conditional on Docker and the `Pinnacle/` sibling layout):

```bash
make docker-install       # conditional; may require sudo
make docker-image
make docker-configure
make docker-build
```

`docker-build` uses `--platform linux/arm64`, mounts the parent Pinnacle directory, and persists a ccache volume. `docker-shell` opens the same development image.

## 4. Test and lint

Native tests build first and run the focused CTest filter from the Makefile:

```bash
make test
```

The Docker/QEMU test path is:

```bash
make docker-test
```

Code quality and documentation targets are:

```bash
make format
make cppcheck
make doc
```

`format` modifies C++ and CMake files. `cppcheck` and `doc` fail early if their required host tools are missing. These commands are not substitutes for testing probes on a real board.

## 5. Packaging and artifacts

The Client Makefile does not define a package target. Build outputs are under `build/native/` or `build/cross/`, especially the `bin/` applications and `lib/` libraries. The Docker image is `pinnacle:latest` unless the subordinate Docker Makefile changes that default. The systemd unit is `systemd/vela-client.service` in the current tracked tree and must be reviewed before installing it for a Pinnacle deployment; this guide does not rename or alter it.

## 6. Deployment, install, and run

Deploy the cross-built output to both configured devices:

```bash
make deploy
```

Deploy one device:

```bash
make deploy-a IP_A=192.168.1.3 HAL_SSH_USER=root HAL_SSH_PASS='<target-password>'
make deploy-b IP_B=192.168.1.13 HAL_SSH_USER=root HAL_SSH_PASS='<target-password>'
```

These targets first run `docker-build`, remove `/tmp/acu-test` remotely, and copy `build/cross/lib` and `build/cross/bin` there. Do not put real passwords in shell history or source; use an appropriate secret mechanism and verify the Makefile's `sshpass` behavior before production use.

Run all copied applications or one named application with arguments:

```bash
make run-a
make run-a APP_NAME arg1 arg2
make run-b APP_NAME arg1 arg2
```

The positional application syntax is interpreted from `MAKECMDGOALS`; confirm the resulting binary exists with `make list`. Local execution uses `LD_LIBRARY_PATH=build/native/lib`:

```bash
make run
make run APP_NAME arg1 arg2
```

There is no root Makefile install target. Any systemd installation, configuration placement, permissions, `/var/lib/pinnacle` setup, and service enablement require a target-specific manual procedure.

## 7. Cleanup

```bash
make clean                 # removes build/
make docker-clean          # removes the cross build inside the Docker volume mount
```

`docker-clean` requires the Docker image. `make list` reports whether native or cross artifacts are present.

## 8. Hardware, cloud, and manual verification caveats

- Adapter probes depend on the target kernel, permissions, network, `/var/lib/pinnacle`, and available hardware; unsupported modules can intentionally return `SKIPPED`.
- `make docker-test` exercises ARM64 binaries under QEMU, not physical peripherals.
- `deploy*` and `run-*` require reachable devices, SSH credentials, and matching target libraries. The configured IPs and default credentials are not evidence of a production environment.
- A real acceptance check must install the intended configuration and service, start the daemon, connect it to a compatible Pinnacle-HQ/Server endpoint, and exercise representative hardware modules. Those steps are manual and are not automated by this Makefile.
