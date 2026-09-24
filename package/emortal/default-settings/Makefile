#
# Copyright (C) 2021 Lean <coolsnowwolf@gmail.com>
#
# Copyright (C) 2021 ImmortalWrt
# <https://immortalwrt.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=default-settings
PKG_VERSION:=1.1
PKG_RELEASE:=$(COMMITCOUNT)

PKG_LICENSE:=GPL-3.0

include $(INCLUDE_DIR)/package.mk

define Package/default-settings
  SECTION:=luci
  CATEGORY:=LuCI
  TITLE:=LuCI support for Default Settings
  DEPENDS:=+luci
  PKGARCH:=all
  DEPENDS:=+luci-lib-base
endef

define Package/default-settings-chn
  $(Package/default-settings)
  TITLE+= (Optimize for CHN users)
  DEPENDS:=+default-settings +@LUCI_LANG_zh_Hans +luci-i18n-base-zh-cn
endef

define Build/Compile
endef

define Package/default-settings/install
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/99-default-settings $(1)/etc/uci-defaults/
endef

define Package/default-settings-chn/install
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/99-default-settings-chinese $(1)/etc/uci-defaults/

	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/i18n
	po2lmo ./i18n/default.zh_Hans.po $(1)/usr/lib/lua/luci/i18n/default.zh-cn.lmo
	po2lmo ./i18n/more.zh_Hans.po $(1)/usr/lib/lua/luci/i18n/more.zh-cn.lmo
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh

HOST_SED="$(subst ${STAGING_DIR_HOST},$${STAGING_DIR_HOST},$(SED))"
HOST_LN="$(subst ${STAGING_DIR_HOST},$${STAGING_DIR_HOST},$(LN))"

[ -n "$${IPKG_INSTROOT}" ] && {
	$${HOST_SED} 's,^src/gz immortalwrt_,src/gz openwrt_,g' -e '/^src\/gz openwrt_\(core\|base\|kmods\|luci\|packages\|routing\|telephony\) /!d' "$${IPKG_INSTROOT}/etc/opkg/distfeeds.conf"

	$${HOST_SED} 's/"192.168.1.1"/"10.168.0.1"/' \
		"$${IPKG_INSTROOT}/bin/config_generate"

	$${HOST_SED} "s/'192\\.168\\.1\\.1'/'10.168.0.1'/; s/'openwrt\\.lan'/window.location.host/" "$${IPKG_INSTROOT}/www/luci-static/resources/view/system/flash.js"
	
	$${HOST_SED} '/<link rel="shortcut icon" href="<%=media%>\/favicon.ico">/a         <link rel="stylesheet" href="<%=resource%>/easepi/easeicon.css?t=1749538073338">' \
		"$${IPKG_INSTROOT}/usr/lib/lua/luci/view/themes/argon/header.htm" \
		"$${IPKG_INSTROOT}/usr/lib/lua/luci/view/themes/argon_dark/header.htm" \
		"$${IPKG_INSTROOT}/usr/lib/lua/luci/view/themes/argon_light/header.htm" \
		"$${IPKG_INSTROOT}/usr/lib/lua/luci/view/themes/argon_dark_purple/header.htm" \
		"$${IPKG_INSTROOT}/usr/lib/lua/luci/view/themes/argon_light_green/header.htm"

	rm -f "$${IPKG_INSTROOT}/etc/uci-defaults/luci-argon-config"

}
true

endef

$(eval $(call BuildPackage,default-settings))
$(eval $(call BuildPackage,default-settings-chn))
