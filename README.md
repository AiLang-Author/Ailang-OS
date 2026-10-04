# Ailang OS

Userspace for a Linux machine. The kernel is borrowed. PID 1, login, the service daemon, the installer, and the virtual file tree are in this repository.

The desktop is HTML tags parsed by Auckland, and the window clients are `Applications/*_ipc.ailang`. Those stay in [Ailang-Self-Hosting-](https://github.com/AiLang-Author/Ailang-Self-Hosting-) because they import the compiler libraries. This tree does not build by itself.

## Layout next to the compiler

Clone both repositories as siblings:

```
/home/bob/Ailang-OS
/home/bob/Ailang-Self-Hosting-
```

The compiler tree points here with symlinks:

```
OS                  -> ../Ailang-OS
docs/aos            -> ../../Ailang-OS/docs/aos
board/ailang_os     -> ../../Ailang-OS/board/ailang_os
```

Build and boot from the compiler tree: `./build_image.sh`, `./run_aos.sh`. What the code actually does today is `CODE_STATUS.md`.

License: Sean Collins Software License (SCSL), same file as the compiler repository.
