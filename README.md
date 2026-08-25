# EDK2 Portable DevKit

Windows x64 portable development kit for building EDK II firmware without installing a system-wide EDK2 environment.

This package contains a fresh official EDK2 checkout, a self-contained Python and LLVM toolchain, the prebuilt Windows BaseTools runtime, and one-click build wrappers. It is not an official Tianocore binary release.

The development release artifact is `EDK2-Portable-DevKit-0.1.0-dev-windows-x64.zip`.

## Quick start

Run these commands from the extracted package directory:

```cmd
Check-Environment.cmd
Build-OVMF.cmd -Report -Threads 4
```

The default build produces:

```text
Workspace\Build\OvmfX64\DEBUG_CLANGPDB\FV\OVMF.fd
Workspace\Build\OvmfX64\DEBUG_CLANGPDB\FV\OVMF_CODE.fd
Workspace\Build\OvmfX64\DEBUG_CLANGPDB\FV\OVMF_VARS.fd
```

The package was validated with a clean `DEBUG / X64 / CLANGPDB` OVMF build.

## Package contents

| Directory or file | Purpose |
| --- | --- |
| `Sources\edk2` | Official EDK2 source at `edk2-stable202605` |
| `Sources\edk2-platforms` | Optional platform packages |
| `Sources\vendor-packages` | User or vendor packages |
| `Toolchain\BaseTools` | Packaged BaseTools runtime used by normal builds |
| `Toolchain\python` | Python 3.12.10 embeddable distribution |
| `Toolchain\llvm` | LLVM/Clang 21.1.0 tools and Clang runtime files |
| `Toolchain\nasm` | NASM 2.16.03 |
| `Toolchain\iasl` | ACPICA IASL 20200717 |
| `Toolchain\make` | GNU Make 4.4.1 |
| `Toolchain\qemu` | Optional location for QEMU x86_64 |
| `Workspace\Conf` | Package-local EDK2 configuration |
| `Workspace\Projects` | User projects and platform workspaces |
| `Config\devkit.json` | Relative-path package configuration |

## Build commands

### Check the environment

```cmd
Check-Environment.cmd
```

The check reports missing required tools as errors. QEMU is optional and is reported as a warning when absent.

### Build the configured platform

```cmd
Build-EDK2.cmd
```

Useful options:

```cmd
Build-EDK2.cmd -Report -Threads 4
Build-EDK2.cmd -Target RELEASE -Report
Build-EDK2.cmd -Platform OvmfPkg/OvmfPkgX64.dsc -Architecture X64 -Target DEBUG -Toolchain CLANGPDB
```

The wrapper invokes the explicit package-local entry point:

```text
Toolchain\BaseTools\BinWrappers\WindowsLike\build.bat
```

It does not depend on a repository-root `build.bat` selected by another package.

### Open an isolated shell

```cmd
Start-EDK2.cmd
```

The shell sets `WORKSPACE`, `CONF_PATH`, `PACKAGES_PATH`, `EDK_TOOLS_PATH`, `BASE_TOOLS_PATH`, and the bundled toolchain paths for that process only.

### Clean generated files

```cmd
Clean-EDK2.cmd
```

This removes `Workspace\Build`, the EDK2 `Workspace\Conf\.cache` directory, BaseTools C/Python build caches, and the generated `.AutoGenIdFile.txt` marker. It does not remove source code, the packaged toolchain binaries, configuration templates, or user projects.

Preview the cleanup without deleting anything:

```cmd
Clean-EDK2.cmd -WhatIf
```

### Rebuild BaseTools

Most users do not need this command because `Toolchain\BaseTools` is already included. Package developers can regenerate it with:

```cmd
Scripts\Build-BaseTools.cmd
```

This command requires a local Visual Studio C/C++ installation. It compiles the rebuildable source under `Sources\edk2\BaseTools\Source\C`, then synchronizes the generated binaries and required runtime support files into `Toolchain\BaseTools`.

## Default build configuration

| Setting | Value |
| --- | --- |
| Source revision | `edk2-stable202605` |
| Source commit | `b03a21a63e3bd001f52c527e5a57feddb53a690b` |
| Platform | `OvmfPkg/OvmfPkgX64.dsc` |
| Architecture | `X64` |
| Target | `DEBUG` |
| Toolchain | `CLANGPDB` |

## Optional QEMU

QEMU is not required to compile firmware. To launch OVMF locally, place `qemu-system-x86_64.exe` and its required runtime files under `Toolchain\qemu`, then run the environment check again.

The current release package is build-validated; it does not claim that every EDK2 platform is ready without additional platform-specific packages, submodules, or dependencies.

## Licenses and redistribution

EDK2 and every bundled third-party tool remain subject to their upstream licenses. See [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md) and the EDK2 license at `Sources\edk2\License.txt`. Before redistributing a modified package, retain the upstream notices and verify the applicable licenses for Python, LLVM, NASM, IASL/ACPICA, GNU Make, and the EDK2 submodules.

## Troubleshooting

If the environment check reports missing files, run it from the extracted package root rather than from `Scripts` or `Workspace`. If a build fails after changing source or platform packages, run `Clean-EDK2.cmd` and retry with `-Report` so the complete log and build report are saved under `Workspace\Build`.
