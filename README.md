# RK3399-NanoPi-NEO4-Build

> This markdown explains RK3399 NanoPi NEO4 build from scratch.

# Build Flows Indexes

## U-Boot Build

> To begin first we got to clone from [Das-U-Boot](https://github.com/u-boot/u-boot).

### U-Boot Prerequisite 

> In order to build the U-Boot, ARM based device require a "arm-trusted-firmware" (AFT)
> 
> This will generates the "bl31.elf"

<details>
<summary>Explained by Chat-GPT: Rockchip SoCs typically follow a **multi-stage boot process**</summary>

### 1. ROM (Mask ROM)
- Built into the SoC; runs first.  
- Loads the next-stage bootloader from storage (SPI flash, eMMC, etc.).

### 2. BL1 (Boot Loader Stage 1)
- Minimal loader; sets up DRAM and CPU state.  
- Loads **BL2**.

### 3. BL2 (Boot Loader Stage 2 / U-Boot SPL)
- More complex loader; initializes peripherals and DRAM fully.  
- Loads **BL31** and/or **BL33** depending on secure boot.

### 4. BL31 (EL3 Runtime Firmware / Secure Monitor)
- Runs at **EL3** (highest ARM exception level).  
- Implements **Secure Monitor** (SMC calls, switching between secure and non-secure worlds).  
- Responsible for setting up the **TrustZone environment**.  
- Loads **BL33** (non-secure bootloader, usually U-Boot) and passes control to it.

### 5. BL33 (Non-secure world / U-Boot)
- Standard bootloader that eventually loads the OS (Linux, Android, etc.).
</details>

```bash session
sudo apt update
sudo apt install -y git make gcc-aarch64-linux-gnu gcc-arm-linux-gnueabihf
sudo apt-get -y install git

git clone https://github.com/ARM-software/arm-trusted-firmware.git
cd arm-trusted-firmware
export CROSS_COMPILE_AARCH64=aarch64-linux-gnu-
make PLAT=rk3399 ARCH=aarch64 CROSS_COMPILE=aarch64-linux-gnu- BL31=build/bl31/bl31.elf DEBUG=0

# You should see this is successfully built.
ls build/rk3399/release/bl31/bl31.elf
```

### U-Boot Build Operations

```bash session
git clone https://github.com/u-boot/u-boot
cd u-boot
git pull

export ARCH=arm64
export CROSS_COMPILE_AARCH64=aarch64-linux-gnu-
export BL31=arm-trusted-firmware/build/rk3399/release/bl31/bl31.elf

# Nanopi NEO4 default configurations are provided
make clean
make mrproper
make nanopi-neo4-rk3399_defconfig
make prepare
# use 8 CPUs to build
make -j 8
```

## Kernel Build

### MIPI CSI-2 Build

## RootFS Distro (Ubuntu etc.)

## Packup (SDMMC or EMMC)

