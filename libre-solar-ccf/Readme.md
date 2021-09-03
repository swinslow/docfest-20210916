# Zephyr west spdx -- libre-solar-ccf

Build analysis SBOM, created using `west spdx` command built into Zephyr as of v2.6.0

Performed for Libre Solar Charge Controller Firmware

See: https://docs.zephyrproject.org/latest/guides/west/zephyr-cmds.html#software-bill-of-materials-west-spdx

## Process

Using the Zephyr west spdx tools requires building from source, because the
tools run as part of the Zephyr build process.

Built for MPPT charge controller from Libre Solar sources + Zephyr tag v2.6.0
(modified as described in Caveats below)

```
# no effect on actual build; just initializes the CMake file-based APIs
# for post-build use with the SBOM generator in Zephyr
> west spdx --init -d build

# run the actual build
> west build -b mppt_2420_hc@0.2 -d build /home/steve/programming/zephyr/prj/charge-controller-firmware/zephyr/

```

Then, run the west spdx tools to analyze and create SPDX documents:

```
# for standard Zephyr west spdx output
> west spdx -d build -n https://swinslow.net/docfest-20210916/charge-controller-firmware/spdx

# for output with header includes
> west spdx -d build -n https://swinslow.net/docfest-20210916/charge-controller-firmware/spdx-with-headers \
>   -s build/spdx-with-headers \
>   --analyze-includes \
>   --include-sdk
```

## Caveats

**NOTE**: the `west spdx` command as of v.2.6.0 release contains a bug, which
results in missing mandatory `PackageLicenseInfoFromFiles:` fields in Packages
when no licenses are detected. This bug was identified when validating the
documents for DocFest, so it has been fixed locally but has not yet been
pushed up to the Zephyr project.

**NOTE**: the `spdx-with-headers` version creates an SPDX document for the SDK,
`sdk.spdx`, which throws warnings when run through the SPDX online tools
validator. The warnings are about detecting an SPDX license identifier for a
license which is now deprecated. However, deprecated license identifiers are
still valid SPDX, so this should not be treated as a validation failure.

## Notes

See the Zephyr docs linked above for details about the multiple SPDX SBOMs that
are created as part of the above process.

The second version ("output with header includes") shown above also analyzes
which C header files are included during the build process. Some of these
headers originate from the [Zephyr SDK](https://github.com/zephyrproject-rtos/sdk-ng)
and therefore this one includes a fourth SBOM for those included files.
