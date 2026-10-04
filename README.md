# Ailang OS

Userspace runtime environment for Ailang, a Linux-based operating system. This repository contains core system components including process initialization (PID 1), authentication, service management, system installation, and the virtual file system implementation.

## Architecture Overview

### Core Components

- **PID 1 (Init System)**: System initialization and process lifecycle management
- **Authentication**: User login and credential management  
- **Service Daemon**: Background service orchestration and lifecycle
- **Installer**: System installation and configuration tooling
- **Virtual File Tree**: VFS abstraction layer for system resources

### Related Components

Desktop and windowing clients are maintained separately in [Ailang-Self-Hosting-](https://github.com/AiLang-Author/Ailang-Self-Hosting-):
- Desktop environment (HTML-based UI parsed by Auckland renderer)
- Window manager and IPC clients (`Applications/*_ipc.ailang`)

## Development Setup

### Repository Layout

Clone this repository alongside the Ailang compiler:

```
/home/bob/Ailang-OS
/home/bob/Ailang-Self-Hosting-
```

The compiler repository references this repository via symlinks:

```
OS                  -> ../Ailang-OS
docs/aos            -> ../../Ailang-OS/docs/aos
board/ailang_os     -> ../../Ailang-OS/board/ailang_os
```

### Building and Running

From the compiler tree:

```bash
./build_image.sh    # Build OS image
./run_aos.sh        # Boot system
```

### Status

For current implementation status and feature coverage, see [CODE_STATUS.md](CODE_STATUS.md).

## Documentation

Complete technical documentation is available in the `docs/` directory.

## License

Sean Collins Software License (SCSL) — see LICENSE file for details.
