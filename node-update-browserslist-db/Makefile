# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=update-browserslist-db
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=1.0.16
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=0864dd2e469aa5aa2f05cf1c3ecf051c876a1bfab54ae32817a1fb893742e4fc

PKG_MAINTAINER:=@esaaprillia, Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

HOST_BUILD_DEPENDS:=node/host
HOST_BUILD_PARALLEL:=1

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/node-update-browserslist-db
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Share target browsers between different front-end tools, like Autoprefixer, Stylelint and babel-preset-env
  URL:=https://www.npmjs.com/package/update-browserslist-db
  DEPENDS:=+node
endef

define Package/node-update-browserslist-db/description
 The config to share target browsers and Node.js versions between different front-end tools.
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)
HOSTTMPNPM:=$(shell mktemp -u XXXXXXXXXX)
NPM_CACHE_DIR:=$(if $(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(TMP_DIR))

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

define Build/Compile
	cd $(PKG_BUILD_DIR); \
	$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	$(if $(CONFIG_NODEJS_NPM_KEEP_CACHE), npm_config_cache=$(NPM_CACHE_DIR)/npm-cache-$(PKG_NPM_NAME),npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM)) \
	npm install --prefer-offline --no-audit --global-style --install-strategy=shallow --no-save --omit=dev --no-package-lock --ignore-scripts
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
	rm -f $(PKG_BUILD_DIR)/node_modules/.package-lock.json
	find $(PKG_BUILD_DIR)/node_modules -type d -empty -print0 | xargs -0 rmdir || true
	rm -rf $(PKG_BUILD_DIR)/node_modules/esbuild/node_modules/@esbuild
	rm -rf $(PKG_BUILD_DIR)/node_modules/rollup/node_modules/@rollup
endef

define Package/node-update-browserslist-db/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(CP) $(PKG_BUILD_DIR)/{package.json,LICENSE,*.js,*.ts,*.md} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_BUILD_DIR)/{node_modules} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node/$(PKG_NPM_NAME)/cli.js $(1)/usr/bin/update-browserslist-db
	$(INSTALL_DIR) $(1)/usr/lib/node_modules
	$(LN) ../node/$(PKG_NPM_NAME) $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)
endef

define Package/node-update-browserslist-db/postrm
#!/bin/sh
rm /usr/lib/node_modules/update-browserslist-db || true
rm -rf /usr/lib/node/update-browserslist-db || true
endef

define Host/Compile
	cd $(HOST_BUILD_DIR); \
	$(HOST_MAKE_VARS) \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	$(if $(CONFIG_NODEJS_NPM_KEEP_CACHE), npm_config_cache=$(NPM_CACHE_DIR)/host-npm-cache-$(PKG_NPM_NAME),npm_config_cache=$(TMP_DIR)/npm-cache-$(HOSTTMPNPM)) \
	npm install --prefer-offline --no-audit --global-style --install-strategy=shallow --no-save --omit=dev --no-package-lock --ignore-scripts
	rm -rf $(TMP_DIR)/npm-cache-$(HOSTTMPNPM)
	rm -f $(HOST_BUILD_DIR)/node_modules/.package-lock.json
	find $(HOST_BUILD_DIR)/node_modules -type d -empty -print0 | xargs -0 rmdir || true
endef

define Host/Install
	$(INSTALL_DIR) $(1)/lib/node_modules/$(PKG_NPM_NAME)
	$(CP) $(HOST_BUILD_DIR)/{package.json,LICENSE,*.js,*.ts,*.md} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(HOST_BUILD_DIR)/{node_modules} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/bin
	$(LN) ../lib/node_modules/$(PKG_NPM_NAME)/cli.js $(1)/bin/update-browserslist-db
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,node-update-browserslist-db))
