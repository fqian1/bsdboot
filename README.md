FREEBSD:
```cd /usr/src && make -C stand```
find loader.kboot in /usr/obj/usr/src/amd64.amd64/stand/kboot/kboot/loader.kboot

DEBIAN:
git clone https://codeberg.org/libreboot/lbmk.git
cd lbmk
./mk dependencies debian
git config .... 
wget rsync.libreboot stable 26.01rev1 roms libreboot- whatever.tar.xz
sudo chown -R user:user .
./mk inject tar.xz setmac (leave empty for random mac)
produces libreboot-26.01rev1_....tar.xz

linuxboot:
wget https://cdn.kernel.org/pub/linux/kernel/v7.x/linux-7.1.2.tar.xz
... make tinyconfig

coreboot:
git clone --depth 1 --branch 26.06 https://review.coreboot.org/coreboot.git
sed -i 's/\t\t&me_state,/\t\t\&power_on_after_fail,\n\t\t\&me_state,/' \
coreboot/src/mainboard/lenovo/sklkbl_thinkpad/cfr.c && \
sed -i 's/\.default_value\t= CONFIG(CSE_DEFAULT_CFR_OPTION_STATE_DISABLED),/.default_value\t= 1,/' \
coreboot/src/soc/intel/common/block/include/intelblocks/cfr.h

make -C util/ifdtool && \
util/ifdtool/ifdtool -x -p sklkbl /opt/libreboot.rom && \
mv flashregion_0_flashdescriptor.bin binaries/ifd.bin && \
mv flashregion_2_intel_me.bin binaries/me.bin && \
mv flashregion_3_gbe.bin binaries/gbe.bin

cd /opt/coreboot && \
cp defconfig .config && \
make olddefconfig

make -j$(nproc)

defconfig:
CONFIG_VENDOR_LENOVO=y
CONFIG_BOARD_LENOVO_T480=y
CONFIG_IFD_BIN_PATH="binaries/ifd.bin"
CONFIG_ME_BIN_PATH="binaries/me.bin"
CONFIG_GBE_BIN_PATH="binaries/gbe.bin"
CONFIG_HAVE_IFD_BIN=y
CONFIG_HAVE_ME_BIN=y
CONFIG_HAVE_GBE_BIN=y
CONFIG_EDK2_FOLLOW_BGRT_SPEC=y
CONFIG_MAINBOARD_SMBIOS_PRODUCT_NAME="whatever"
CONFIG_CBFS_SIZE=0xE0C000
CONFIG_POWER_STATE_ON_AFTER_FAILURE=y
