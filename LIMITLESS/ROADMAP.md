# Limitless — engineering roadmap

This file tracks project-specific work. The inherited kernel tree remains the baseline; no compatibility or performance claim is implied by this plan.

## Target

Samsung Galaxy A03 family (SM-A035F / SM-A035M / SM-A035G), Unisoc UMS9230, qogirl6, ARM64. The starting kernel is Linux 4.14.199. Validate each hardware variant independently even if a common defconfig appears viable.

## Workstreams

1. **Baseline and reproducibility**
   - Record source provenance, branch ancestry, defconfig, toolchain versions and build targets.
   - Build an unmodified baseline before changing kernel behavior.
   - Compare DTB/DTBO, board config, boot image layout and partition assumptions per variant.
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
   - The source archive identifies its stock image as A035FXXS7CXF3. The tester device is A035FXXS9CYG1; cross-binary equivalence is not yet independently verified and the resulting image remains a test artifact until device validation.
   - Preserve per-device firmware provenance. DTBO packaging and SM-A035M/SM-A035G packaging remain separate work.

## Release gates

- **Baseline:** reproducible kernel build and structural validation of raw kernel and repacked boot artifacts.
- **Alpha:** boots on a specifically identified device/firmware, with logs and known limitations.
- **Beta:** broader tester coverage and regression checks.
- **Stable:** only after documented device/firmware matrix and recovery validation.

## Current status

- Builder: BSP build flags corrected; raw ARM64 kernel build, source-stock boot repackaging, provenance report, kernel-byte verification and release artifact collection are implemented.
- Kernel: initial HyperBPF audit completed; NET_ACT_BPF and BPF_STREAM_PARSER are enabled in defconfig as the first isolated feature delta. No source backports yet.
- No build has been launched and no device boot validation has been claimed.
