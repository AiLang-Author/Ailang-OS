# Ailang OS

Ailang OS is the userspace runtime for the Ailang operating system. This repository contains the system bootstrap and service layer: PID 1, login flow, service daemon, installer, schema/bootstrap logic, and the virtual filesystem/tree implementation.

**Design principles:** minimal abstraction, direct kernel interaction, straightforward execution paths. The codebase prioritizes clarity and efficiency over generality. Every function does one thing. Every module owns one piece of the system.

## Scope

This repository owns the OS runtime and platform primitives:

- `Init.ailang`: PID 1 bootstrap and early system startup
- `Login.ailang`: login flow and authentication entrypoints
- `ServiceDaemon.ailang`: service registry and orchestration
- `Installer.ailang`: system installation and provisioning
- `Schema.ailang`: database schema and system metadata model
- `FileTree.ailang`: virtual file tree implementation
- `UUIDStore.ailang`: object/blob storage layer
- `UI/`: early desktop and presentation assets

**Note on UI/desktop:** Desktop shell and window clients are currently transitioning. As this moves to the self-hosting compiler tree (Ailang-Self-Hosting-), the `UI/` directory and related components will be relocated. The OS runtime remains the stable core.

## Design model

- **Low abstraction**: direct syscalls, minimal wrapper layers
- **Single responsibility**: each component owns one subsystem
- **Database-backed state**: runtime configuration lives in PostgreSQL, not ad hoc config files
- **Virtual filesystem as primitive**: the file tree is a first-class OS data structure
- **Kernel-minimal**: the kernel is borrowed; this repo owns userspace orchestration
- **Direct execution**: boot sequence is straightforward: init → services → desktop

## Repository layout

```
Ailang-OS/
├── Init.ailang              — PID 1 bootstrap
├── Login.ailang             — Authentication entrypoint
├── ServiceDaemon.ailang     — Service registry and orchestration
├── Installer.ailang         — System provisioning
├── Schema.ailang            — Database schema and metadata
├── FileTree.ailang          — Virtual file tree
├── UUIDStore.ailang         — Blob storage and identity
├── Test*.ailang             — Component unit tests
├── UI/                      — Desktop/presentation (transitioning)
├── board/                   — Board-specific configs (Buildroot overlay)
├── docs/
├── BUILD.md                 — Build and deployment architecture
├── BUILD_REQUIREMENTS.md    — Toolchain, QEMU, platform config
├── CODE_STATUS.md           — Implementation status (current, not planned)
└── LICENSE
```

## Documentation

Read these in order:

1. **`CODE_STATUS.md`** — What is implemented right now. Start here if you're evaluating the codebase.
2. **`BUILD.md`** — Architecture, disk layout, boot sequence, deployment options
3. **`BUILD_REQUIREMENTS.md`** — Toolchain dependencies, QEMU/EFI setup, PostgreSQL config
4. **`docs/aos/`** — Deep dives on specific subsystems:
   - `DEVICE_INTERFACES.md` — Hardware assumptions and I/O handling
   - `FIRMWARE.md` — Firmware loading and boot requirements
   - `SANDBOX_JAIL.md` — Sandbox and application confinement (v0/v1/v2)
   - `emergency.md` — Recovery and emergency procedures
   - `phase1-rls-pgcrypto-login.md` — Login and encryption milestone
   - `phase2-luks-secure-boot.md` — Disk encryption and secure boot roadmap
   - `PORTING_FOREIGN.md` — Integration with non-Ailang userspace

## Build and boot

```bash
./build_image.sh           # Build disk image (16 GB)
./run_aos.sh               # Boot in QEMU with KVM
```

QEMU environment:
- EFI boot (OVMF, no bootloader needed)
- 2 GB RAM, 2 vCPU
- bochs-display framebuffer (1152×864)
- SSH forwarded to localhost:2222
- PostgreSQL forwarded to localhost:15432

See `BUILD_REQUIREMENTS.md` for detailed QEMU/KVM configuration and platform assumptions.

## Current implementation status

**Implemented and working:**
- PID 1 bootstrap (filesystem mounts, device init, PostgreSQL startup)
- Login flow (evdev keyboard, credential validation, session init)
- Service daemon (registry, autostart, basic lifecycle)
- Database schema and bootstrap
- Virtual file tree and blob storage (operations layer)
- Framebuffer display and basic UI rendering

**Deferred:**
- Sandbox v1 and v2 (user namespaces, overlayfs, FUSE — kernel config pending)
- Full disk encryption (LUKS integration in progress)
- Secure boot (signed kernel + TPM)
- Application privilege separation (currently all services run as root)
- Package manager (schema exists; seeding incomplete)

See `CODE_STATUS.md` for comprehensive status and open issues by priority.

## Architecture snapshot

**Boot flow:**
1. UEFI firmware loads `EFI/BOOT/BOOTX64.EFI` (Linux bzImage with EFI stub)
2. Kernel mounts rootfs and starts PID 1
3. Init mounts early filesystems, loads kernel modules, starts PostgreSQL
4. Service daemon bootstraps schema and launches autostart services
5. Display server initializes and waits for IPC connections
6. Desktop ready

**Data model:**
- Services registered in `services` table with binary path, autostart flag, priority
- Virtual filesystem in `files` table (parent_id tree structure) with blob storage
- User accounts in `users` table with hashed credentials
- Runtime state in `service_status`, `sessions`, `settings`
- UI configuration in `themes` and theme_values

**Subsystem boundaries:**
- `Init` owns boot sequence and early init; once services start, it becomes a reaper
- `Login` is standalone auth entry point (can run before desktop)
- `ServiceDaemon` owns registry and lifecycle; applications are black boxes to it
- `FileTree` provides virtual FS abstraction; storage is PostgreSQL-backed blob store
- UI components talk to services via Unix socket IPC

## Development workflow

Typically:
1. Edit `.ailang` source in this repo
2. Compile with the self-hosting compiler from Ailang-Self-Hosting-
3. Deploy to rootfs overlay and rebuild disk image
4. Test in QEMU or on physical hardware

All binaries are cross-compiled; nothing runs natively on the host. The AILang compiler is the only upstream dependency.

## Repository maintainability

- No magic. Every function is explicit; no implicit behavior in macros or metaprogramming.
- Tests are co-located with source: `Init.ailang` + `TestInit.ailang`
- Documentation is versioned alongside code (not in a wiki or separate repo)
- Status is canonical in `CODE_STATUS.md`, not issues or roadmaps

## License

Sean Collins Software License (SCSL). See `LICENSE`.

---

The goal is to make this repo a reference for how to build a minimal, direct operating system userspace. No layers of abstraction. No "framework" overhead. Just the primitives you need, organized cleanly.
