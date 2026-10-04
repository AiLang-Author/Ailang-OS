# Ailang OS

Ailang OS is the userspace runtime for the Ailang operating system. This repository contains the system bootstrap and service layer: PID 1, login flow, service daemon, installer, schema/bootstrap logic, and the virtual filesystem/tree implementation.

This is not a generic desktop environment repository. The goal is to provide the OS runtime and service substrate on top of a Linux kernel, with the graphical desktop and application clients kept in the separate self-hosting compiler/runtime repository.

## Scope

The repository covers the OS runtime and platform primitives:

- `Init.ailang`: PID 1 bootstrap and early system startup
- `Login.ailang`: login flow and authentication entrypoints
- `ServiceDaemon.ailang`: service registry / orchestration daemon
- `Installer.ailang`: system installation and provisioning workflow
- `Schema.ailang`: database schema and system metadata model
- `FileTree.ailang`: virtual file tree representation
- `UUIDStore.ailang`: object/blob storage and identity model
- `UI/`: desktop and presentation layer assets

The desktop shell and IPC client applications live in the companion repository, [Ailang-Self-Hosting-](https://github.com/AiLang-Author/Ailang-Self-Hosting-), as separate user-interface concerns.

## Design model

Ailang OS follows a small set of operating principles:

- Bootstrap-first: system services are initialized from PID 1 and then brought up in dependency order.
- Database-backed configuration: runtime state, services, and settings are represented in a PostgreSQL-backed model rather than ad hoc config files alone.
- Virtual filesystem as a first-class OS primitive: the file tree is modeled as structured objects with blob-backed storage.
- Minimal kernel dependency: the kernel is borrowed; this repo owns the userspace runtime and system orchestration.
- UI separated from core OS: window clients and HTML-based desktop behavior live outside this repository.

## Repository layout

```text
Ailang-OS/
├── Init.ailang
├── Login.ailang
├── ServiceDaemon.ailang
├── Installer.ailang
├── Schema.ailang
├── FileTree.ailang
├── UUIDStore.ailang
├── TestInit.ailang
├── TestLogin.ailang
├── TestSchema.ailang
├── TestFileTree.ailang
├── TestUUIDStore.ailang
├── UI/
├── board/
├── docs/
├── BUILD.md
├── BUILD_REQUIREMENTS.md
├── CODE_STATUS.md
├── LICENSE
├── README.md
└── FileTree.ailang
```

## Documentation map

The repository includes focused engineering documentation alongside the runtime source:

- `BUILD.md` — build and deployment architecture, disk image layout, and boot flow
- `BUILD_REQUIREMENTS.md` — QEMU/EFI requirements, toolchain assumptions, and platform configuration
- `CODE_STATUS.md` — current implementation status and what is working vs. deferred work
- `docs/aos/DEVICE_INTERFACES.md` — device and interface assumptions
- `docs/aos/FIRMWARE.md` — firmware expectations and hardware integration notes
- `docs/aos/PORTING_FOREIGN.md` — portability and foreign-system integration notes
- `docs/aos/SANDBOX_JAIL.md` — sandbox and confinement design notes
- `docs/aos/emergency.md` — emergency/recovery guidance
- `docs/aos/phase1-rls-pgcrypto-login.md` — login and crypto milestone notes
- `docs/aos/phase2-luks-secure-boot.md` — future secure boot and disk protection direction

## Build and execution

The project is designed to be built from the compiler/self-hosting environment alongside the Ailang toolchain. The build and boot workflow is documented in `BUILD.md` and `BUILD_REQUIREMENTS.md`.

Typical flow:

```bash
./build_image.sh
./run_aos.sh
```

For QEMU and EFI boot requirements, see:

```bash
./build_image.sh --qemu
```

## Current status

This repository is still an active engineering project. Implementation status is intentionally tracked in `CODE_STATUS.md` rather than implied by the README. If you are evaluating the repo, treat that file as the source of truth for what is implemented today.

## License

This project is licensed under the Sean Collins Software License (SCSL). The exact license file is `LICENSE`.

## Relationship to the wider Ailang system

Ailang OS provides the operating system runtime and platform services. The UI, application clients, and self-hosting toolchain live separately. This split keeps the OS runtime independent from the presentation layer while preserving a single system architecture.

If you are looking for the desktop environment or window app definitions, refer to [Ailang-Self-Hosting-](https://github.com/AiLang-Author/Ailang-Self-Hosting-).
