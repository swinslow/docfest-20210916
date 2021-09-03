# Zephyr west spdx -- hello-world

Build analysis SBOM, created using `west spdx` command built into Zephyr as of v2.6.0

See: https://docs.zephyrproject.org/latest/guides/west/zephyr-cmds.html#software-bill-of-materials-west-spdx

## Process

Using the Zephyr west spdx tools requires building from source, because the
tools run as part of the Zephyr build process.

Built for reel-board-v2 from Zephyr source tree 2021-09-02 with tag v2.6.0
(modified as described in Caveats below)

```
# no effect on actual build; just initializes the CMake file-based APIs
# for post-build use with the SBOM generator in Zephyr
> west spdx --init -d build

# run the actual build, for reel_board_v2
> west build -b reel_board_v2 -d build samples/hello_world/

```

Then, run the west spdx tools to analyze and create SPDX documents:

```
# for standard Zephyr west spdx output
> west spdx -d build -n https://swinslow.net/docfest-20210916/zephyr-hello-world-reel-board-v2/spdx

# for output with header includes
> west spdx -d build -n https://swinslow.net/docfest-20210916/zephyr-hello-world-reel-board-v2/spdx-with-headers \
>   -s build/spdx-with-headers \
>   --analyze-includes \
>   --include-sdk
```

Redoing for QEMU, get rid of the `build/` directory, then re-run:

```
> west spdx --init -d build
> west build -b qemu_x86 -d build samples/hello_world/
> west spdx -d build -n https://swinslow.net/docfest-20210916/zephyr-hello-world-qemu/spdx-qemu

```

## Caveats

**NOTE**: the `west spdx` command as of v.2.6.0 release contains a bug, which
results in missing mandatory `PackageLicenseInfoFromFiles:` fields in Packages
when no licenses are detected. This bug was identified when validating the
documents for DocFest, so it has been fixed locally but has not yet been
pushed up to the Zephyr project.

**NOTE**: when building for QEMU, the Zephyr west spdx tools will build an
SPDX document with standard settings. The west spdx tools also include an
option which will determine which C header files are imported during the
build. For some reason, this feature does not appear to work when building for
QEMU at present. As a demonstration, I'm also including a build of the same
code for the reel-board-v2 board instead of QEMU, which includes both the
"standard" west spdx output and also the "spdx-with-headers" version.

## Notes

See the Zephyr docs linked above for details about the multiple SPDX SBOMs that
are created as part of the above process.

The second version ("output with header includes") for the reel-board-v2 build
also analyzes which C header files are included during the build process, as
described above. Some of these headers originate from the
[Zephyr SDK](https://github.com/zephyrproject-rtos/sdk-ng) and therefore
this one includes a fourth SBOM for those included files.
