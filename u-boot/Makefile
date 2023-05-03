include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/version.mk

PKG_NAME:=u-boot
PKG_SOURCE_PROTO:=git
PKG_BRANCH:=master
PKG_RELEASE:=1

include $(INCLUDE_DIR)/local-development.mk
ifeq ($(DUMP)$(PKG_VERSION),)
  PKG_REV:=$(shell git ls-remote $(PKG_SOURCE_URL) $(PKG_BRANCH) | cut -b -7)
  PKG_VERSION:=g$(PKG_REV)
endif

PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)
CONFIG_NAME=$(shell echo $(BUILD_VARIANT) | sed 's/.*-//')

include $(INCLUDE_DIR)/package.mk

define Package/uboot-ipq5332
  SECTION:=boot
  CATEGORY:=Boot Loaders
  DEPENDS:=@TARGET_ipq53xx
  TITLE:=U-boot for IPQ53xx based platforms
  URL:=http://www.denx.de/wiki/U-Boot
  VARIANT:=ipq5332_mmc
endef

define Package/uboot-ipq9574
  SECTION:=boot
  CATEGORY:=Boot Loaders
  DEPENDS:=@TARGET_ipq95xx
  TITLE:=U-boot for IPQ9574 based platforms
  URL:=http://www.denx.de/wiki/U-Boot
  VARIANT:=ipq9574_mmc
endef

ifeq ($(CONFIG_LINUX_6_1),y)
KCPPFLAGS += \
	-Wno-error=maybe-uninitialized \
	-Wno-error=address-of-packed-member \
	-Wno-error=attributes \
	-Wno-error=misleading-indentation
endif

define Build/Configure
	$(MAKE) -C $(PKG_BUILD_DIR) $(CONFIG_NAME)_defconfig CROSS_COMPILE=$(TARGET_CROSS) TARGETCC=$(TARGET_CC)
endef

define Build/Compile
	VERSION_CODE='$(VERSION_NICK) $(VERSION_NUMBER)' \
	REVISION='$(REVISION)' \
	$(MAKE) -C $(PKG_BUILD_DIR) HOSTSTRIP=true CROSS_COMPILE=$(TARGET_CROSS) \
		TARGETCC=$(TARGET_CC) \
		KCPPFLAGS="$(KCPPFLAGS)" \
		HOSTLDFLAGS="$(HOST_LDFLAGS) -pthread" \
		HOSTCFLAGS="$(HOST_CFLAGS)" \
		DTC=${LINUX_DIR}/scripts/dtc/dtc \
		all
endef

define Package/uboot-ipq5332/install
	$(INSTALL_DIR) $(1) $(BIN_DIR)
	$(CP) $(PKG_BUILD_DIR)/u-boot $(BIN_DIR)/openwrt-ipq5332-u-boot.elf
	$(CP) $(PKG_BUILD_DIR)/u-boot $(BIN_DIR)/openwrt-ipq5332-u-boot-stripped.elf
	$(STRIP) $(BIN_DIR)/openwrt-ipq5332-u-boot-stripped.elf
	$(CP) $(PKG_BUILD_DIR)/u-boot.bin $(BIN_DIR)/openwrt-ipq5332-u-boot.img
endef

define Package/uboot-ipq9574/install
	$(INSTALL_DIR) $(1) $(BIN_DIR)
	$(CP) $(PKG_BUILD_DIR)/u-boot $(BIN_DIR)/openwrt-ipq9574-u-boot.elf
	$(CP) $(PKG_BUILD_DIR)/u-boot $(BIN_DIR)/openwrt-ipq9574-u-boot-stripped.elf
	$(STRIP) $(BIN_DIR)/openwrt-ipq9574-u-boot-stripped.elf
	$(CP) $(PKG_BUILD_DIR)/u-boot.bin $(BIN_DIR)/openwrt-ipq9574-u-boot.img
endef

$(eval $(call BuildPackage,uboot-ipq5332))
$(eval $(call BuildPackage,uboot-ipq9574))
