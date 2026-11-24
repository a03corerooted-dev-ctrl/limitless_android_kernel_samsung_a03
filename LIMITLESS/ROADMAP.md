# Limitless — engineering roadmap

This file tracks project-specific work. The inherited kernel tree remains the baseline; no compatibility or performance claim is implied by this plan.

## Target

Samsung Galaxy A03 family (SM-A035F / SM-A035M / SM-A035G), Unisoc UMS9230, qogirl6, ARM64. The starting kernel is Linux 4.14.199. Validate each hardware variant independently even if a common defconfig appears viable.

## Workstreams

1. **Baseline and reproducibility**
   - Record source provenance, branch ancestry, defconfig, toolchain versions and build targets.
   - Build an unmodified stock-config baseline before changing kernel behavior.
   - Compare the embedded stock config, module release/ABI, DTB/DTBO, boot image layout and partition assumptions per variant.
2. **Kernel maintenance**
   - Audit compiler compatibility and existing downstream patches.
   - Separate cleanups from functional changes; keep changes bisectable.
3. **HyperBPF**
   - Initial source/config inventory is recorded in LIMITLESS/HYPERBPF-BASELINE.md.
   - Existing 4.14 BPF syscall, verifier, maps/helpers, ARM64 JIT, cgroup, tracing and packet-filter paths are present; this is not modern eBPF feature parity.
   - Complete the helper/map/program/attach-type inventory and map candidate upstream changes to exact 4.14 dependencies.
   - Backport incrementally with 4.14 API adaptations and focused compile/runtime tests.
   - Evaluate BTF/CO-RE and Android userspace loader compatibility separately; do not claim support until demonstrated.
4. **Compatibility research**
   - Establish the Android/vendor baseline: VINTF, HAL manifests, init, fstab, SELinux, Binder, properties, linker namespaces and VNDK.
   - Treat GSI boot, OEM ROM support and One UI ports as separate compatibility targets.
5. **Boot and packaging**
   - Builder now repacks the source tree's Publish/a03-A035FXXS7CXF3.tar.xz stock boot member using the bundled magiskboot, replacing only the kernel payload.
   - The source archive identifies its stock image as A035FXXS7CXF3; the supplied tester image is A035FXXS9CYG1. Their decompressed ramdisk CPIO and embedded DTB table were compared and are byte-identical. The boot header name and Android patch metadata differ (SRPUH31A007 / 2024-07 versus SRPUH31A009 / 2025-08).
   - Keep the device's existing CYG1 DTBO partition unchanged for the first boot test. The source builds the qogirl6 DTB/DTBO targets, but this is not a request to flash newly built overlays.
   - Preserve per-device firmware provenance. SM-A035M/SM-A035G packaging remains separate work.

## Release gates

- **Baseline:** reproducible kernel build and structural validation of raw kernel and repacked boot artifacts.
- **Alpha:** boots on a specifically identified device/firmware, with logs and known limitations.
- **Beta:** broader tester coverage and regression checks.
- **Stable:** only after documented device/firmware matrix and recovery validation.

## Current status

- Builder: Build #6 compiled Linux 4.14.199 with pinned GCC 4.9 / Clang 11.0.1 and produced a structurally unpackable boot image. It has not been validated by a device boot.
- Build #6's resolved configuration differed from the supplied CYG1 stock kernel configuration in only four entries: CONFIG_LOCALVERSION, CONFIG_NET_ACT_BPF, CONFIG_BPF_STREAM_PARSER and the selected CONFIG_STREAM_PARSER. The module_layout CRC matched; the complete 580-symbol vendor-module ABI comparison remains pending.
- Kernel: the defconfig is restored to the stock settings for the first baseline. The DTC host-link fix is retained. No kernel source backports or performance changes are in scope.
- Current work is limited to the normal boot baseline and compatibility diagnostics. HyperBPF, governors and other optimizations are deferred until boot validation.
