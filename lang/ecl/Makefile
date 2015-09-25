#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=ecl
PKG_VERSION:=16.0.0
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://common-lisp.net/project/ecl/files/
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tgz
PKG_MD5SUM:=f7a7e5236af170f24dadeb427d5cf0d3

PKG_LICENSE:=LGPL-2
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Attila Lendvai <attila@lendvai.name>

# Build settings
PKG_INSTALL:=1
PKG_BUILD_DEPENDS:=libgmp libffi libreadline libpthread ecl/host
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk

define Package/ecl
  SUBMENU:=Common Lisp
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Embeddable Common-Lisp
  URL:=https://common-lisp.net/project/ecl/
  DEPENDS:=+libffi +libgmp +libreadline +libpthread
endef

define Package/ecl/description
  A Common Lisp implementation that compiles to C.
endef

define Package/ecl/config
	source "$(SOURCE)/Config.in"
endef

# Host
HOST_CONFIGURE_ARGS += \
	--prefix=$(STAGING_DIR_HOST) \
	--enable-longdouble=no

HOST_CONFIGURE_VARS += \
	CFLAGS=-m32 \
	LDFLAGS=-m32 \
	ABI=32

# Target
CONFIGURE_ARGS += \
	--disable-longdouble \
	--with-libffi-prefix=${STAGING_DIR} \
	--with-system-gmp \
	--with-cmp=$(if $(CONFIG_ECL_INCLUDE_C_BACKEND),yes,no) \
	--with-bytecmp=$(if $(CONFIG_ECL_INCLUDE_C_BACKEND),yes,builtin)

EXTRA_LDFLAGS+="--sysroot=${STAGING_DIR}"

define Build/Configure
	mkdir -p $(PKG_BUILD_DIR)/build/
	sed \
	  -e 's!%%_BIGENDIAN_%%!$(if $(CONFIG_BIG_ENDIAN),yes,no)!g' \
	  -e 's!%%_HOST_ECL_%%!$(STAGING_DIR_HOST)/bin/ecl!g' \
	  cross_config >$(PKG_BUILD_DIR)/build/cross_config
	$(call Build/Configure/Default,)
endef

define Package/ecl/install
	$(INSTALL_DIR) $(1)/usr/
	$(CP) -r $(PKG_INSTALL_DIR)/usr/bin $(1)/usr/
	$(CP) -r $(PKG_INSTALL_DIR)/usr/lib $(1)/usr/
	if [ -n "$(CONFIG_ECL_INCLUDE_C_BACKEND)" ]; then \
		$(CP) -r $(PKG_INSTALL_DIR)/usr/include $(1)/usr/; \
	fi
endef

$(eval $(call BuildPackage,ecl))
$(eval $(call HostBuild))
