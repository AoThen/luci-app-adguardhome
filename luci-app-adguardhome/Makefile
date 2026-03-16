# Copyright (C) 2018-2019 Lienol
#
# This is free software, licensed under the Apache License, Version 2.0 .
#

include $(TOPDIR)/rules.mk

PKG_NAME:=luci-app-adguardhome
PKG_VERSION:=1.9.2
PKG_RELEASE:=1
PKG_MAINTAINER:=<https://github.com/AoThen/luci-app-adguardhome>

LUCI_TITLE:=LuCI app for adguardhome
LUCI_LANG.zh_Hans:=简体中文
LUCI_DEPENDS:=+!wget&&!curl&&!wget-ssl:curl
LUCI_PKGARCH:=all
LUCI_DESCRIPTION:=LuCI support for adguardhome

define Package/luci-app-adguardhome/conffiles
/usr/share/AdGuardHome/links.txt
/etc/config/AdGuardHome
endef

define Package/luci-app-adguardhome/postinst
#!/bin/sh
	/etc/init.d/AdGuardHome enable >/dev/null 2>&1
	enable=$(uci get AdGuardHome.AdGuardHome.enabled 2>/dev/null)
	if [ "$enable" == "1" ]; then
		/etc/init.d/AdGuardHome reload >/dev/null 2>&1
	fi
	rm -f /tmp/luci-indexcache
	rm -f /tmp/luci-modulecache/*
exit 0
endef

define Package/luci-app-adguardhome/prerm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
     /etc/init.d/AdGuardHome disable
     /etc/init.d/AdGuardHome stop >/dev/null 2>&1
uci -q batch <<-EOF >/dev/null 2>&1
	delete ucitrack.@AdGuardHome[-1]
	commit ucitrack
EOF
fi
exit 0
endef

define Package/luci-app-adguardhome/postrm
#!/bin/sh
rm -rf /etc/AdGuardHome/
exit 0
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/controller
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/luasrc/controller/* $(1)/usr/lib/lua/luci/controller/
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/model/cbi/AdGuardHome
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/luasrc/model/cbi/AdGuardHome/* $(1)/usr/lib/lua/luci/model/cbi/AdGuardHome/
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/view/AdGuardHome
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/luasrc/view/AdGuardHome/* $(1)/usr/lib/lua/luci/view/AdGuardHome/
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/i18n
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/po/*/AdGuardHome.lmo $(1)/usr/lib/lua/luci/i18n/
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/root/etc/config/AdGuardHome $(1)/etc/config/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/root/etc/init.d/AdGuardHome $(1)/etc/init.d/
	$(INSTALL_DIR) $(1)/usr/share/AdGuardHome
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/root/usr/share/AdGuardHome/*.yaml $(1)/usr/share/AdGuardHome/
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/root/usr/share/AdGuardHome/*.txt $(1)/usr/share/AdGuardHome/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/root/usr/share/AdGuardHome/*.sh $(1)/usr/share/AdGuardHome/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/root/usr/share/AdGuardHome/firewall.start $(1)/usr/share/AdGuardHome/
	$(INSTALL_DIR) $(1)/www/luci-static/resources
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/root/www/luci-static/resources/* $(1)/www/luci-static/resources/
endef

include $(TOPDIR)/feeds/luci/luci.mk

# call BuildPackage - OpenWrt buildroot signature
