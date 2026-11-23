# Limitless — engineering roadmap

This file tracks project-specific work. The inherited kernel tree remains the baseline; no compatibility or performance claim is implied by this plan.

## Target

Samsung Galaxy A03 family (SM-A035F / SM-A035M / SM-A035G), Unisoc UMS9230, qogirl6, ARM64. The starting kernel is the upstreamed vendor 4.14 tree. Validate each hardware variant independently even if a common defconfig appears viable.

## Workstreams

1. **Baseline and reproducibility**
   - Record source provenance, branch ancestry, defconfig, toolchain versions and build targets.
   - Build an unmodified baseline before changing kernel behavior.
   - Compare DTB/DTBO, board config, boot image layout and partition assumptions per variant.
2. **Kernel maintenance**
   - Audit compiler compatibility and existing downstream patches.
   - Separate cleanups from functional changes; keep changes bisectable.
3. **HyperBPF**
   - Inventory existing BPF core, syscall, verifier, maps, helpers, ARM64 JIT, tracing, perf and cgroup/network hooks.
   - Map each desired upstream feature to its dependency chain and exact upstream commit range.
   - Backport incrementally with 4.14 API adaptations and focused compile/runtime tests.
   - Evaluate BTF/CO-RE and Android userspace loader compatibility separately; do not claim support until demonstrated.
4. **Compatibility research**
   - Establish the Android/vendor baseline: VINTF, HAL manifests, init, fstab, SELinux, Binder, properties, linker namespaces and VNDK.
   - Treat GSI boot, OEM ROM support and One UI ports as separate compatibility targets.
5. **Boot and packaging**
   - Keep kernel compilation separate from boot/DTBO repacking.
   - Require tester-provided, matching firmware images and preserve per-device metadata.
   - Never ship a boot image built from an assumed cross-variant stock image.

## Release gates

- **Baseline:** repeatable build and artifact validation.
- **Alpha:** boots on a specifically identified device/firmware, with logs and known limitations.
- **Beta:** broader tester coverage and regression checks.
- **Stable:** only after documented device/firmware matrix and recovery validation.

## Current status

Planning and repository scaffolding. No kernel feature backports, One UI compatibility work, boot image packaging, or device validation have been completed yet.
