# Copyright (C) 2012 OpenWrt.org
# Copyright (C) 2015-2016 Lantiq Beteiligungs GmbH & Co KG.
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=ltq-vdsl-vr9-mei
PKG_VERSION:=1.5.17.6
PKG_RELEASE:=7

PKG_BASE_NAME:=drv_mei_cpe
PKG_SOURCE:=$(PKG_BASE_NAME)-$(PKG_VERSION).tar.gz
PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_BASE_NAME)-$(PKG_VERSION)
PKG_SOURCE_URL:=@OPENWRT
PKG_HASH:=94f6904364348b7f74087e721968abc28b2564fb9bd8899aa930d36490387662
PKG_FIXUP:=autoreconf
PKG_FLAGS:=nonshared
PKG_MAINTAINER:=John Crispin <john@phrozen.org>
PKG_LICENSE:=GPL-2.0 BSD-2-Clause
PKG_LICENSE_FILES:=LICENSE
PKG_EXTMOD_SUBDIRS:=src

include $(INCLUDE_DIR)/package.mk

define KernelPackage/ltq-vdsl-vr9-mei
  TITLE:=mei driver for vdsl
  SECTION:=sys
  SUBMENU:=Network Devices
  DEPENDS:=@(TARGET_lantiq_xrx200||TARGET_lantiq_xrx200_legacy) +kmod-ltq-ifxos +kmod-ltq-vectoring
  FILES:=$(PKG_BUILD_DIR)/src/drv_mei_cpe.ko
  AUTOLOAD:=$(call AutoLoad,50,drv_mei_cpe)
endef

define KernelPackage/ltq-vdsl-vr9-mei/description
	Lantiq MEI CPE Kernel Module Driver
endef


define Package/ltq-vdsl-vr9-mei-test
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Lantiq mei driver test tool
  URL:=http://www.lantiq.com/
  DEPENDS:=@(TARGET_lantiq_xrx200||TARGET_lantiq_xrx200_legacy)
endef

define Package/ltq-vdsl-vr9-mei-test/description
	Userland tool to directly control the mei driver, this is only needed
	for test and development purposes.
endef

MAKE_FLAGS += \
	$(KERNEL_MAKE_FLAGS) \
	SHELL="$(BASH)"

# ltq-vdsl-vr9-app uses a header provided by the MEI driver which has some
# conditionals.
# Define the conditionals here to have the same view on both sides. If you
# change them, you need to change them for the ltq-vdsl-vr9-app as well
MEI_DRV_CFLAGS = \
	-DMEI_DRV_ATM_PTM_INTERFACE_ENABLE=1 \
	-DMEI_SUPPORT_DEBUG_STREAMS=1 \
	-DMEI_SUPPORT_OPTIMIZED_FW_DL=1

CONFIGURE_ARGS += \
	--enable-kernelincl="$(LINUX_DIR)/include" \
	--enable-device=vr9 \
	--with-max-device=1 \
	--with-lines-per-device=1 \
	--enable-debug \
	--enable-error_print \
	--enable-ifxos-include="-I$(STAGING_DIR)/usr/include/ifxos/" \
	--enable-ifxos-library="-L$(STAGING_DIR)/usr/lib" \
	--enable-add_drv_cflags="$(MEI_DRV_CFLAGS)" \
	--enable-linux-26 \
	--enable-kernelbuild="$(LINUX_DIR)" \
	--enable-drv_test_appl=yes \
	ARCH=$(LINUX_KARCH)

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/vdsl
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_api_intern.h $(1)/usr/include/vdsl/
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_api_atm_ptm_intern.h $(1)/usr/include/vdsl/
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_interface.h $(1)/usr/include/vdsl
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_config.h $(1)/usr/include/vdsl/
	$(CP) $(PKG_BUILD_DIR)/src/cmv_message_format.h $(1)/usr/include/vdsl/
endef

$(eval $(call KernelPackage,ltq-vdsl-vr9-mei))

define Package/ltq-vdsl-vr9-mei-test/install
	$(INSTALL_DIR) $(1)/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/mei_cpe_drv_test $(1)/bin
endef

$(eval $(call BuildPackage,ltq-vdsl-vr9-mei-test))
