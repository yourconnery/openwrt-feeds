#
# Copyright (C) 2010-2013 OpenWrt.org
# Copyright (C) 2010 Alfred E. Heggestad
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=baresip
PKG_VERSION:=0.4.11
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/yourconnery/baresip-1
PKG_SOURCE_VERSION:=master
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)

baresip-mods:= \
	alsa \
	avcodec \
	avformat \
	cons \
	evdev \
	g711 \
	g722 \
	gst \
	h264packet \
	oss \
	opus \
	sdl \
	speex \
	speex_aec \
	speex_pp \
	stdio \
	uuid \
	vumeter \
	v4l \
	v4l2 \

PKG_CONFIG_DEPENDS:= \
	$(patsubst %,CONFIG_PACKAGE_baresip-mod-%,$(baresip-mods)) \

include $(INCLUDE_DIR)/package.mk

define Package/baresip/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Telephony
  URL:=http://www.creytiv.com/
endef

define Package/baresip
$(call Package/baresip/Default)
  TITLE:=Portable and modular SIP User-Agent with A/V support
  DEPENDS:=+libre +librem +libpthread 
endef


baresip-mod-alsa      := USE_ALSA
baresip-mod-avcodec   := USE_FFMPEG
baresip-mod-avformat  := USE_FFMPEG_AVFORMAT
baresip-mod-cons      := USE_CONS
baresip-mod-evdev     := USE_EVDEV
baresip-mod-g711      := USE_G711
baresip-mod-g722      := USE_G722
baresip-mod-gst       := USE_GST
baresip-mod-h264packet:= USE_H264PACKET
baresip-mod-oss       := USE_OSS
baresip-mod-opus      := USE_OPUS
baresip-mod-sdl       := USE_SDL
baresip-mod-speex     := USE_SPEEX
baresip-mod-speex_aec := USE_SPEEX_AEC
baresip-mod-speex_pp  := USE_SPEEX_PP
baresip-mod-stdio     := USE_STDIO
baresip-mod-uuid      := USE_UUID
baresip-mod-vumeter   := USE_VUMETER
baresip-mod-v4l       := USE_V4L
baresip-mod-v4l2      := USE_V4L2

BARESIP_MOD_OPTIONS:= \
	MOD_AUTODETECT= \
	$(foreach m,$(baresip-mods),$(baresip-mod-$(m))=$(if $(CONFIG_PACKAGE_baresip-mod-$(m)),1))

TARGET_CFLAGS += $(FPIC)

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		LIBRE_MK="$(STAGING_DIR)/usr/share/re/re.mk" \
		LIBRE_INC="$(STAGING_DIR)/usr/include/re" \
		LIBRE_SO="$(STAGING_DIR)/usr/lib" \
		LIBREM_PATH="../rem" \
		CC="$(TARGET_CC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		EXTRA_CFLAGS="$(TARGET_CFLAGS) -DOPENWRT" \
		SYSROOT="$$$$($(FIND) $(TOOLCHAIN_DIR) -path '*/include/pthread.h' | sed -ne '1s#/include/pthread.h##p')" \
		SYSROOT_ALT="$(STAGING_DIR)/usr" \
		RELEASE=1 \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		OS="linux" \
		$(BARESIP_MOD_OPTIONS) \
		HAVE_SPEEXDSP=1 \
		USE_VIDEO=1 \
		all install
endef

define Package/baresip/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/baresip $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib/baresip/modules
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/ice.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/stun.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/turn.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/account.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/auloop.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/contact.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/menu.so \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/vidloop.so \
		$(1)/usr/lib/baresip/modules/.
	$(INSTALL_DIR) $(1)/usr/share/baresip
	$(CP) $(PKG_INSTALL_DIR)/usr/share/baresip/* $(1)/usr/share/baresip/.
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/baresip.init $(1)/etc/init.d/baresip
endef


#
# 1. Name
# 2. Title
# 3. Files
# 4. Depends
#
define BuildPlugin

  define Package/baresip-mod-$(1)
    $$(call Package/baresip/Default)
    TITLE:= baresip $(2) module
    DEPENDS:= baresip $(4)
  endef

  define Package/baresip-mod-$(1)/install
	[ -z "$(3)" ] || $(INSTALL_DIR) $$(1)/usr/lib/baresip/modules
	for f in $(3); do \
		$(CP) \
			$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/$$$$$$$${f}.so \
			$$(1)/usr/lib/baresip/modules/ ; \
	done
  endef

  $$(eval $$(call BuildPackage,baresip-mod-$(1)))
endef


$(eval $(call BuildPackage,baresip))

$(eval $(call BuildPlugin,alsa,ALSA audio driver,alsa,+PACKAGE_baresip-mod-alsa:alsa-lib))
$(eval $(call BuildPlugin,avcodec,FFmpeg video codecs,avcodec,+PACKAGE_baresip-mod-avcodec:libffmpeg-full))
$(eval $(call BuildPlugin,avformat,FFmpeg video source,avformat,+PACKAGE_baresip-mod-avformat:libffmpeg-full))
$(eval $(call BuildPlugin,cons,console UI,cons,))
$(eval $(call BuildPlugin,evdev,input event device UI,evdev,))
$(eval $(call BuildPlugin,g711,G.711 audio codec,g711,))
$(eval $(call BuildPlugin,g722,G.722 audio codec,g722,+PACKAGE_baresip-mod-g722:libspandsp))
$(eval $(call BuildPlugin,gst, gstreamer framework,gst, +gst-mod-alsa  +gst-mod-audioconvert +gst-mod-audioresample))
$(eval $(call BuildPlugin,h264packet, H.264 stream packetize,h264packet, ))
$(eval $(call BuildPlugin,sdl,SDL video display,sdl,+libsdl))
$(eval $(call BuildPlugin,oss,OSS audio driver,oss,))
$(eval $(call BuildPlugin,opus,OPUS codec, opus, +PACKAGE_baresip-mod-opus:opus))
$(eval $(call BuildPlugin,speex,Speex audio codec,speex,+PACKAGE_baresip-mod-speex:libspeex))
$(eval $(call BuildPlugin,speex_aec,Speex audio dsp - echo cancellation,speex_aec,+PACKAGE_baresip-mod-speex_aec:libspeexdsp))
$(eval $(call BuildPlugin,speex_pp, Speex audio dsp - preprocessor,speex_pp,+PACKAGE_baresip-mod-speex_pp:libspeexdsp))
$(eval $(call BuildPlugin,stdio,standard I/O UI,stdio,))
$(eval $(call BuildPlugin,uuid,UUID,uuid,+libuuid))
$(eval $(call BuildPlugin,vumeter,vumeter,vumeter))
$(eval $(call BuildPlugin,v4l,Video4Linux video source,v4l,+PACKAGE_baresip-mod-v4l:libv4l))
$(eval $(call BuildPlugin,v4l2,Video4Linux2 video source,v4l2,+PACKAGE_baresip-mod-v4l2:libv4l))
