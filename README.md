# lamu-axion-apps-patches

Device-specific patches for AxionOS' own apps/SDK, extracted out of the
full-history "backup fork" repos so those don't need to exist anymore.
Each of these apps is 100% Axion's own project - the patches just carry
the handful of commits that are actually ours (device bugfixes / features
for `motorola/lamu`), on top of the real upstream.

## AxionFx

Base: [AxionAOSP/android_packages_apps_AxionFx](https://github.com/AxionAOSP/android_packages_apps_AxionFx)
commit `ef06e57`. **Applies clean onto current upstream HEAD** (`6dff5e2`,
checked 01/10/2026) - upstream only moved 1 commit since our fork point and
it doesn't touch the same files.

```bash
git am /path/to/lamu-axion-apps-patches/AxionFx/*.patch
```

4 patches: dynamic audio session attachments (fixes AxionAOSP/issue_tracker#375),
preset/manual-toggle interaction fix, binder leak fix, limiter threshold tune.

## axion_sdk

Base: [AxionAOSP/android_axion_sdk](https://github.com/AxionAOSP/android_axion_sdk)
commit `7ffbade`. **Does NOT apply clean onto current upstream** (`af5241a`,
checked 01/10/2026) - upstream moved 34 commits ahead and both sides touch
`ax_km_server/.../AxKernelConfigLoader.java` (upstream kept developing the
Kernel Manager independently). Applying on top of `7ffbade` itself works;
catching up to current upstream needs a real manual merge of that file, not
done yet.

```bash
git checkout 7ffbade  # or whatever commit you're building against
git am /path/to/lamu-axion-apps-patches/axion_sdk/*.patch
```

7 patches: the whole `LIMIT_AXION_USER` GPU range-limiter backend - governor
control type, the `ConcurrentModificationException` crash fix, OPP-index
freq lock (replacing governor min/max), slider direction fix, diagnostic
logging, wiring to the real kernel node.

## AxionParts

Base: [AxionAOSP/android_packages_apps_AxionParts](https://github.com/AxionAOSP/android_axion_sdk)
commit `71a0995`. **Does NOT apply clean onto current upstream** (`751c9b9`,
checked 01/10/2026) - same reason as axion_sdk: upstream kept developing the
Kernel Manager screen independently, conflicts in `PerformanceComponents.kt`
and `KernelManagerScreen.kt`. Applies clean on top of `71a0995` itself.

```bash
git checkout 71a0995
git am /path/to/lamu-axion-apps-patches/AxionParts/*.patch
```

4 patches: the Kernel Manager UI side of the same GPU range-limiter work -
Hz/MHz display fix, governor picker, swap governor+min/max for a single
freq-lock slider, real min/max range sliders + battery warning.

## Launcher3

Base: [AxionAOSP/android_packages_apps_Launcher3](https://github.com/AxionAOSP/android_packages_apps_Launcher3)
**current upstream HEAD** `6ce5e9d` (checked 01/10/2026) - **applies clean**.

Note: AxionAOSP rebased their own Launcher3 history at some point (their
tree and our old fork's tree have nearly the same commit count but mostly
different hashes for the same content), so the only commit that's actually
ours is this one - everything else in the old "backup fork" repo was just a
stale copy of Axion's own history, not local work.

```bash
git am /path/to/lamu-axion-apps-patches/Launcher3/*.patch
```

1 patch: disable Live Tile in Recents on this device (MT6768's HWC can't
give a hardware overlay plane to a live-tile video layer, forces full GPU
composition - measured 75-92% janky frames with a live tile playing video).
