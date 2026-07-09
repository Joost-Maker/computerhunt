# Architecture — decided, don't re-litigate each run

The system design the hardware is bought to serve. Drives a couple of component choices (thread
count, RAM headroom, NIC). Revisit only if Joost changes intent.

## The design: one bare-metal Linux host + a network-isolated VM

**No hypervisor distro, no GPU passthrough.** A single Linux install (Pop!_OS / Ubuntu 24.04 —
also what ROCm officially supports) is the daily driver, the gaming machine, and the local-inference
machine, all natively. On top of it runs **one network-isolated KVM/QEMU guest** (libvirt /
virt-manager) for the Claude/agent worker.

Why this shape: the two AI roles split cleanly by what they need.
- **Claude/agent worker** (API-driven, longer unattended jobs) → the **isolated VM**. It needs
  CPU + RAM + network only, **never the GPU**. Keep it a real VM, not a container: the agent runs
  code and must stay off the LAN, and a KVM boundary is the defensible isolation for untrusted
  code execution.
- **Local inference jobs** → run **natively on the host**, full ROCm, full VRAM. Just another host
  process. It contends with gaming for the one GPU only in the trivial sense that you don't fire a
  heavy inference run mid-game (or you do, share VRAM, and eat the frames).

Because nothing but the host ever touches the discrete GPU, there is **no passthrough** and the
GPU-for-AI-vs-GPU-for-gaming question is not a virtualization problem at all — it's two host
processes sharing a card. This is the simplest shape that satisfies every requirement.

## LAN isolation
A libvirt **isolated network**: egress-only to the internet (for the Claude API), **no route to
LAN subnets / NAS**. Enforce via the host firewall or a tiny router VM. Hardware nudge: a board
with good VLAN support / a second NIC makes this clean, but it's a preference not a requirement.

## Memory policy (VM RAM ebb and flow)
- **virtio-balloon on**, VM capped at a **modest ceiling (~8–16 GB)**, min set low. The balloon
  hands idle/cache RAM back to the host automatically. (Guest needs the balloon driver — built into
  modern Linux; on a Windows guest install virtio-win or it does nothing.) Ballooning is
  cooperative and lazy: great for reclaiming idle memory, not instant under pressure.
- **Hard reclaim = shut the VM.** For a big local inference job that needs lots of *system* RAM,
  shutting the worker down returns **all** its RAM to the host immediately and deterministically —
  cheaper than it sounds, because the worker's real state lives in Notion / the repo / the API, not
  in guest RAM. Balloon handles the routine ebb; kill-the-VM is the guaranteed hard reclaim.
- Note most local-inference memory is **VRAM** (weights sit on the GPU), so system-RAM contention
  with the VM is rarer than it feels — mainly large KV-cache / RAG / CPU-offload cases.

## Consequences for the parts list
- **No passthrough → no IOMMU constraint.** Board is unconstrained on that axis; a solid **B850**
  is the default. The iGPU (free on every Ryzen 9000) is a nicety (host troubleshooting / a virtual
  display for the VM), not a requirement.
- **CPU: thread-heavy on purpose.** Host + games + local inference + the isolated worker want cores
  → 16c/32t class (see requirements.md). This is a settled direction, not a fork.
- Generous RAM (64 now → 128 later) so host + VM + inference don't contend. Reach 128 GB via a
  **2×64 GB** swap, not by filling all four slots (see the DDR note in requirements.md).
- Prefer ≥2.5GbE (a second NIC / VLANs a plus for the isolation net).
- Efficient PSU + air cooling — the host runs long and often.
