#
# Copyright (C) 2009-2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=netatalk
PKG_VERSION:=4.2.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@SF/netatalk
PKG_HASH:=8f772804ce4322a4e8fb159528209450927e9df067daa5cc02ab59b35f4b450c

PKG_MAINTAINER:=Antonio Pastor <antonio.pastor@gmail.com>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:netatalk:netatalk

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/meson.mk

define Package/netatalk/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Filesystem
  TITLE:=Apple Filing Protocol
  PROVIDES:=netatalk
  URL:=http://netatalk.sourceforge.net
  DEPENDS:=+libevent2 +libdb47 +libgcrypt +iniparser
endef

define Package/netatalk-small
  $(call Package/netatalk/Default)
  TITLE+= (afpd only)
  VARIANT:=small
endef

define Package/netatalk
  $(call Package/netatalk/Default)
  TITLE+= with AppleTalk support
  VARIANT:=appletalk
  DEPENDS+=+kmod-appletalk
endef

define Package/netatalk-full
  $(call Package/netatalk/Default)
  TITLE+= (configurable)
  VARIANT:=full
  DEPENDS+= \
    +PACKAGE_NETATALK_APPLETALK:kmod-appletalk \
    +PACKAGE_NETATALK_ZEROCONF:libavahi-client \
    +PACKAGE_NETATALK_ACLS:libacl \
    +PACKAGE_NETATALK_PAM:libpam \
    +PACKAGE_NETATALK_LDAP:libopenldap \
    +PACKAGE_NETATALK_ICONV:libiconv-full \
    +PACKAGE_NETATALK_KERBEROS:krb5-libs \
    +PACKAGE_NETATALK_LIBWRAP:libwrap
  MENU:=1
endef

define Package/netatalk/description/default
  Netatalk is an Open Source Apple Filing Protocol (AFP) fileserver.
  Implements a service that allows Macintosh file sharing
  and Time Machine backups. Modern MacOS prefers Samba network shares
  but AFP has shown superior throughput. High Sierra and older MacOS
  only support AFP for Time Machine backups.
endef

define Package/netatalk-small/description
  $(call Package/netatalk/description/default)
  This package includes only the AFPD service.
endef

define Package/netatalk/description
  $(call Package/netatalk/description/default)
  This package includes the AFPD service and AppleTalk binaries.
endef

define Package/netatalk-full/description
  $(call Package/netatalk/description/default)
  This package includes all OpenWrt supported features (customizable).
endef

define Package/netatalk-full/config
        source "$(SOURCE)/Config.in"
endef

MESON_ARGS += \
	-Dwith-cnid-default-backend=dbd \
	-Dwith-cnid-dbd-backend=true \
	-Dwith-cnid-mysql-backend=false \
	-Dwith-bdb-path="$(STAGING_DIR)/usr/" \
	-Dwith-unicode-data-path="$(STAGING_DIR_HOST)/share/gnulib/tests/uniname/" \
	-Dwith-libgcrypt-path="$(STAGING_DIR)/usr/" \
	-Dwith-afpstats=false \
	-Dwith-cups=false \
	-Dwith-quota=false \
	-Dwith-spotlight=false \
	-Dwith-cracklib=false \
	-Dwith-tests=false \
	-Dwith-testsuite=false \
	-Dwith-dtrace=false \
	-Dwith-debug=false \
	-Dwith-shell-check=false \
	-Dwith-init-style=none \

ifeq ($(BUILD_VARIANT),small)
MESON_ARGS += \
	-Dwith-appletalk=false \
	-Dwith-zeroconf=false \
	-Dwith-acls=false \
	-Dwith-ldap=false \
	-Dwith-pam=false \
	-Dwith-libiconv=false \
	-Dwith-kerberos=false \
	-Dwith-krbV-uam=false \
	-Dwith-tcp-wrappers=false \

endif

ifeq ($(BUILD_VARIANT),appletalk)
MESON_ARGS += \
	-Dwith-appletalk=true \
	-Dwith-zeroconf=false \
	-Dwith-acls=false \
	-Dwith-ldap=false \
	-Dwith-pam=false \
	-Dwith-libiconv=false \
	-Dwith-kerberos=false \
	-Dwith-krbV-uam=false \
	-Dwith-tcp-wrappers=false \

endif

ifeq ($(BUILD_VARIANT),full)
MESON_ARGS += \
	-Dwith-appletalk=$(if $(CONFIG_PACKAGE_NETATALK_APPLETALK),true,false) \
	-Dwith-zeroconf=$(if $(CONFIG_PACKAGE_NETATALK_ZEROCONF),true,false) \
	-Dwith-acls=$(if $(CONFIG_PACKAGE_NETATALK_ACLS),true,false) \
	-Dwith-ldap=$(if $(CONFIG_PACKAGE_NETATALK_LDAP),true,false) \
	-Dwith-pam=$(if $(CONFIG_PACKAGE_NETATALK_PAM),true,false) \
	-Dwith-libiconv=$(if $(CONFIG_PACKAGE_NETATALK_ICONV),true,false) \
	$(if $(CONFIG_PACKAGE_NETATALK_ICONV),-Dwith-libiconv-path=$(STAGING_DIR)/usr/lib/libiconv-full,) \
	-Dwith-kerberos=$(if $(CONFIG_PACKAGE_NETATALK_KERBEROS),true,false) \
	-Dwith-krbV-uam=$(if $(CONFIG_PACKAGE_NETATALK_KERBEROS),true,false) \
	-Dwith-tcp-wrappers=$(if $(CONFIG_PACKAGE_NETATALK_LIBWRAP),true,false) \

endif

define Package/netatalk/conffiles
/etc/afp.conf
/etc/extmap.conf
/etc/netatalk/
/etc/atalkd.conf
/etc/macipgw.conf
endef

define Package/netatalk-small/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_DIR) $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/lib/netatalk
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libatalk.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netatalk/*.so $(1)/usr/lib/netatalk/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/* $(1)/usr/sbin/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/afp.conf $(1)/etc/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/extmap.conf $(1)/etc/
	$(INSTALL_BIN) ./files/afpd.init $(1)/etc/init.d/afpd
	$(INSTALL_DATA) ./files/afpd.conf $(1)/etc/config/afpd
endef

define Package/netatalk/install
        $(call Package/netatalk-small/install,$(1))
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/atalkd.conf $(1)/etc/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/macipgw.conf $(1)/etc/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/papd.conf $(1)/etc/
	$(INSTALL_BIN) ./files/atalkd.init $(1)/etc/init.d/atalkd
	$(INSTALL_BIN) ./files/macipgw.init $(1)/etc/init.d/macipgw
	$(INSTALL_BIN) ./files/timelord.init $(1)/etc/init.d/timelord
	$(INSTALL_BIN) ./files/a2boot.init $(1)/etc/init.d/a2boot
	$(INSTALL_BIN) ./files/papd.init $(1)/etc/init.d/papd
	$(INSTALL_DATA) ./files/atalkd.conf $(1)/etc/config/atalkd
	$(INSTALL_DATA) ./files/macipgw.conf $(1)/etc/config/macipgw
	$(INSTALL_DATA) ./files/timelord.conf $(1)/etc/config/timelord
	$(INSTALL_DATA) ./files/a2boot.conf $(1)/etc/config/a2boot
	$(INSTALL_DATA) ./files/papd.conf $(1)/etc/config/papd
endef

define Package/netatalk-full/install
        $(call Package/netatalk-small/install,$(1))
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/atalkd.conf $(1)/etc/,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/macipgw.conf $(1)/etc/,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) $(PKG_INSTALL_DIR)/etc/papd.conf $(1)/etc/,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_BIN) ./files/atalkd.init $(1)/etc/init.d/atalkd,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_BIN) ./files/macipgw.init $(1)/etc/init.d/macipgw,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_BIN) ./files/timelord.init $(1)/etc/init.d/timelord,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_BIN) ./files/a2boot.init $(1)/etc/init.d/a2boot,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_BIN) ./files/papd.init $(1)/etc/init.d/papd,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) ./files/atalkd.conf $(1)/etc/config/atalkd,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) ./files/macipgw.conf $(1)/etc/config/macipgw,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) ./files/timelord.conf $(1)/etc/config/timelord,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) ./files/a2boot.conf $(1)/etc/config/a2boot,)
	$(if $(PACKAGE_NETATALK_APPLETALK),$(INSTALL_DATA) ./files/papd.conf $(1)/etc/config/papd,)
endef

$(eval $(call BuildPackage,netatalk-small))
$(eval $(call BuildPackage,netatalk))
$(eval $(call BuildPackage,netatalk-full))
