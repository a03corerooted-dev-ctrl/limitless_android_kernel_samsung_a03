# HyperBPF baseline audit — Linux 4.14.199

## Scope and method

Inventory of the current limitless/dev source tree and a03_cis_open_defconfig before any backport. This is not a claim of modern eBPF feature parity or Android loader compatibility.

Target: ARM64, Unisoc UMS9230 / qogirl6, Galaxy A03 family.

## Existing kernel implementation

The source tree already contains the principal 4.14 eBPF components:

- kernel/bpf/core.c — BPF program allocation, execution/JIT plumbing and kallsyms integration.
- kernel/bpf/syscall.c and kernel/bpf/verifier.c — BPF syscall and verifier.
- kernel/bpf/helpers.c plus map implementations: hashtab.c, arraymap.c, lpm_trie.c, map_in_map.c, devmap.c, sockmap.c and stackmap.c.
- arch/arm64/net/bpf_jit_comp.c — ARM64 eBPF JIT; ARM64 Kconfig selects HAVE_EBPF_JIT.
- net/core/filter.c — socket/packet filter integration.
- net/sched/act_bpf.c — BPF traffic-control action implementation.

The kernel Makefile identifies this tree as Linux 4.14.199.

## Current defconfig state

Enabled:
- CONFIG_BPF=y
- CONFIG_BPF_SYSCALL=y
- CONFIG_BPF_JIT=y
- CONFIG_BPF_JIT_ALWAYS_ON=y
- CONFIG_CGROUP_BPF=y
- CONFIG_BPF_EVENTS=y
- CONFIG_NET_CLS_BPF=y
- CONFIG_NET_CLS_ACT=y
- CONFIG_NET_SCH_INGRESS=y
- CONFIG_HAVE_EBPF_JIT=y (selected by ARM64)

Disabled:
- CONFIG_NET_ACT_BPF — Kconfig depends on NET_CLS_ACT, which is already enabled.
- CONFIG_BPF_STREAM_PARSER — Kconfig depends on BPF_SYSCALL, which is already enabled.
- CONFIG_NET_CLS_FLOWER — optional classifier, currently disabled.

## Gaps and constraints

- The presence of the 4.14 verifier and JIT is not equivalent to current upstream eBPF functionality.
- BTF/CO-RE support has not been established. Do not assume CONFIG_DEBUG_INFO_BTF or modern BTF infrastructure exists in this baseline.
- The exact helper, map-type, program-type, attach-type and verifier feature set still needs a symbol/API inventory against a selected upstream target.
- Android userspace loader compatibility, permissions, SELinux policy, bpffs mounting and required tooling are separate validation tasks.
- No BPF feature has been backported as part of this audit.

## Next implementation sequence

1. Produce and retain a reproducible, unmodified baseline build using the corrected builder.
2. Capture the generated .config and compiler/build metadata.
3. Enable only already-present optional Kconfig features with satisfied dependencies (starting with NET_ACT_BPF and BPF_STREAM_PARSER) as a separately testable change.
4. Select an upstream kernel/BPF target and map each candidate commit to 4.14 dependencies before backporting.
5. Backport in small groups, keeping verifier, helpers/maps, JIT, networking hooks and tracing independently reviewable.
6. Validate each stage with build tests first, then device/runtime tests. Do not claim CO-RE or modern libbpf compatibility without evidence.
