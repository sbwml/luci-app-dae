<h1 align="center">luci-app-dae</h1>
<p align="center">
  <img width="100" src="https://github.com/sbwml/luci-app-dae/assets/16485166/fbab0891-865e-4bd2-a5fc-c6bae05efeaa" />
</p>
<p align="center">
  <b>A Linux high-performance transparent proxy solution based on eBPF.</b>
</p>

-----------


## Build on OpenWrt Official 23.05/SNAPSHOT

### 1. Get Source

```bash
git clone https://github.com/sbwml/luci-app-dae package/dae
git clone https://github.com/sbwml/v2ray-geodata package/v2ray-geodata
```

### 2. Install clang-13, refer to https://apt.llvm.org

```bash
apt-get update
apt-get install -y clang-13
```

### 3. Change OpenWrt Source (Requirements for DAE to work)

- Enable eBPF support, add content to: `.config`
  ```
  CONFIG_DEVEL=y
  CONFIG_BPF_TOOLCHAIN_HOST=y
  # CONFIG_BPF_TOOLCHAIN_NONE is not set
  CONFIG_KERNEL_BPF_EVENTS=y
  CONFIG_KERNEL_CGROUP_BPF=y
  CONFIG_KERNEL_DEBUG_INFO=y
  CONFIG_KERNEL_DEBUG_INFO_BTF=y
  # CONFIG_KERNEL_DEBUG_INFO_REDUCED is not set
  CONFIG_KERNEL_XDP_SOCKETS=y
  ```

- Patch cgroupfs-mount: use cgroupfs2 (Required when docker is compiled.)
  ```bash
  # fix unmount hierarchical mount
  pushd feeds/packages
      curl -s https://raw.githubusercontent.com/sbwml/luci-app-dae/main/.cgroupfs/cgroupfs-mount.init.patch | patch -p1
  popd
  # cgroupfs v2
  mkdir -p feeds/packages/utils/cgroupfs-mount/patches
  curl -s https://raw.githubusercontent.com/sbwml/luci-app-dae/main/.cgroupfs/900-add-cgroupfs2.patch > feeds/packages/utils/cgroupfs-mount/patches/900-add-cgroupfs2.patch
  ```

### 4. Build luci-app-dae

```bash
make menuconfig # choose LUCI -> Applications -> luci-app-dae
make package/dae/luci-app-dae/compile V=s # build luci-app-dae
```

-----------

![dae](https://github.com/sbwml/luci-app-dae/assets/16485166/df81cd85-2957-4bc7-bb57-c561766d7e67)
