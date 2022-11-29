# Build instructions
1. Create a new folder somewhere on your computer, we'll name it `lineage-kernel`.
2. `cd` into the folder and clone this repository.
3. Clone google's gcc toolchain `git clone https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9 -b pie-release`
4. Create a new directory to store our built kernel, we'll name it `out`.
5. Symlink all the toolchain libraries inside the bin folder. `cd aarch64-linux-android-4.9/bin && find ../lib -type f -exec ln -s {} . \; && cd -`
6. `cd` into the kernel folder `android_kernel_samsung_universal7420_custom`.
7. Run the following commands in order, line by line (replace zerofltexx with zeroltexx if compiling for S6 Edge):
```
export ARCH=arm64 CROSS_COMPILE=../aarch64-linux-android-4.9/bin/aarch64-linux-android- ANDROID_MAJOR_VERSION=o
make O=../out lineageos_zerofltexx_defconfig
make O=../out -j"$(nproc)"
```

# Installation instructions
Once the build has finished, proceed with the installation steps.
1. Create a new folder in which we'll extract the ROM's boot.img file, we'll name it `bootimg`
2. `cd` into the `bootimg` folder.
3. Copy the newly built kernel `Image.gz-dtb` file to the `bootimg` folder: `cp ../out/arch/arm64/boot/Image.gz-dtb .`
4. Grab `boot.img` from the [full ROM zip](https://github.com/fakemanoan/LineageOS-Releases/releases) and place it inside the `bootimg` folder: `7z x lineage-19*.zip boot.img`
5. Using [Android Image Kitchen](https://github.com/osm0sis/Android-Image-Kitchen/tree/AIK-Linux), extract the boot.img file. On Archlinux: `aik-unpack boot.img`.
6. Extract the Image file: `mv Image.gz-dtb Image.gz && gunzip Image.gz`. This will give you a new file named simply `Image`.
7. Rename the newly extracted file to `boot.img-kernel`.
8. Place your new `boot.img-kernel` file inside the `split_img` folder, replacing the already existing file.
9. While inside the `bootimg` folder, recreate the boot.img file with Android Image Kitchen. On Archlinux: `aik-repack`. This will give you a new file `image-new.img`.
10. **(OPTIONAL)** Patch `image-new.img` with Magisk (https://topjohnwu.github.io/Magisk/install.html) before flashing it with TWRP. This avoids flashing Magisk on step 11.
11. Copy `image-new.img` to your phone, reboot into TWRP, Install the new img file and reflash Magisk.
