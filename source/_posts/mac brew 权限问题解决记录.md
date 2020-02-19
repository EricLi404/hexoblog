---
title: mac brew 权限问题解决记录
date: 2018-01-18
tags: [brew,macOS]
description: 曾经，brew 是使用 root 权限运行的，如今，没有了 root 权限，brew 安装软件的时候经常会遇到缺少权限的问题。本文记录了赋予 brew 权限的方案。
---

曾经，`brew` 是使用`root` 权限运行的，如今，没有了`root` 权限，`brew`安装软件的时候经常会遇到缺少权限的问题。

## 更改权限
```
sudo chown -R $(whoami):admin /usr/local
// 第一条运行失败了一半
sudo chown -R $(whoami):admin /usr/local/Cellar
sudo chown -R $(whoami):admin /usr/local/Homebrew
sudo chown -R $(whoami):admin /usr/local/lib
```

## 使用「医生」诊断因更改权限造成的问题
```
brew doctor
```
运行 `doctor` 后，输出如下：

```
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry or file an issue; just ignore this. Thanks!

Warning: Suspicious Homebrew/brew git origin remote found.

With a non-standard origin, Homebrew won't pull updates from
the main repository. The current git origin is:
  git://mirrors.ustc.edu.cn/brew.git

Unless you have compelling reasons, consider setting the
origin remote to point at the main repository by running:
//执行这句
  git -C "/usr/local/Homebrew" remote set-url origin https://github.com/Homebrew/brew.git

Warning: Unbrewed header files were found in /usr/local/include.
If you didn't put them there on purpose they could cause problems when
building Homebrew formulae, and may need to be deleted.

//删除 /usr/local/include/node 文件夹
Unexpected header files:
  /usr/local/include/node/android-ifaddrs.h
  /usr/local/include/node/ares.h
  /usr/local/include/node/ares_build.h
  /usr/local/include/node/ares_rules.h
  /usr/local/include/node/ares_version.h
  /usr/local/include/node/libplatform/libplatform.h
  /usr/local/include/node/libplatform/v8-tracing.h
  /usr/local/include/node/nameser.h
  /usr/local/include/node/node.h
  /usr/local/include/node/node_buffer.h
  /usr/local/include/node/node_object_wrap.h
  /usr/local/include/node/node_version.h
  /usr/local/include/node/openssl/aes.h
  /usr/local/include/node/openssl/archs/BSD-x86/opensslconf.h
  /usr/local/include/node/openssl/archs/BSD-x86_64/opensslconf.h
  /usr/local/include/node/openssl/archs/VC-WIN32/opensslconf.h
  /usr/local/include/node/openssl/archs/VC-WIN64A/opensslconf.h
  /usr/local/include/node/openssl/archs/aix-gcc/opensslconf.h
  /usr/local/include/node/openssl/archs/aix64-gcc/opensslconf.h
  /usr/local/include/node/openssl/archs/darwin-i386-cc/opensslconf.h
  /usr/local/include/node/openssl/archs/darwin64-x86_64-cc/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-aarch64/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-armv4/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-elf/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-ppc/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-ppc64/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-x32/opensslconf.h
  /usr/local/include/node/openssl/archs/linux-x86_64/opensslconf.h
  /usr/local/include/node/openssl/archs/linux32-s390x/opensslconf.h
  /usr/local/include/node/openssl/archs/linux64-s390x/opensslconf.h
  /usr/local/include/node/openssl/archs/solaris-x86-gcc/opensslconf.h
  /usr/local/include/node/openssl/archs/solaris64-x86_64-gcc/opensslconf.h
  /usr/local/include/node/openssl/asn1.h
  /usr/local/include/node/openssl/asn1_mac.h
  /usr/local/include/node/openssl/asn1t.h
  /usr/local/include/node/openssl/bio.h
  /usr/local/include/node/openssl/blowfish.h
  /usr/local/include/node/openssl/bn.h
  /usr/local/include/node/openssl/buffer.h
  /usr/local/include/node/openssl/camellia.h
  /usr/local/include/node/openssl/cast.h
  /usr/local/include/node/openssl/cmac.h
  /usr/local/include/node/openssl/cms.h
  /usr/local/include/node/openssl/comp.h
  /usr/local/include/node/openssl/conf.h
  /usr/local/include/node/openssl/conf_api.h
  /usr/local/include/node/openssl/crypto.h
  /usr/local/include/node/openssl/des.h
  /usr/local/include/node/openssl/des_old.h
  /usr/local/include/node/openssl/dh.h
  /usr/local/include/node/openssl/dsa.h
  /usr/local/include/node/openssl/dso.h
  /usr/local/include/node/openssl/dtls1.h
  /usr/local/include/node/openssl/e_os2.h
  /usr/local/include/node/openssl/ebcdic.h
  /usr/local/include/node/openssl/ec.h
  /usr/local/include/node/openssl/ecdh.h
  /usr/local/include/node/openssl/ecdsa.h
  /usr/local/include/node/openssl/engine.h
  /usr/local/include/node/openssl/err.h
  /usr/local/include/node/openssl/evp.h
  /usr/local/include/node/openssl/hmac.h
  /usr/local/include/node/openssl/idea.h
  /usr/local/include/node/openssl/krb5_asn.h
  /usr/local/include/node/openssl/kssl.h
  /usr/local/include/node/openssl/lhash.h
  /usr/local/include/node/openssl/md4.h
  /usr/local/include/node/openssl/md5.h
  /usr/local/include/node/openssl/mdc2.h
  /usr/local/include/node/openssl/modes.h
  /usr/local/include/node/openssl/obj_mac.h
  /usr/local/include/node/openssl/objects.h
  /usr/local/include/node/openssl/ocsp.h
  /usr/local/include/node/openssl/opensslconf.h
  /usr/local/include/node/openssl/opensslv.h
  /usr/local/include/node/openssl/ossl_typ.h
  /usr/local/include/node/openssl/pem.h
  /usr/local/include/node/openssl/pem2.h
  /usr/local/include/node/openssl/pkcs12.h
  /usr/local/include/node/openssl/pkcs7.h
  /usr/local/include/node/openssl/pqueue.h
  /usr/local/include/node/openssl/rand.h
  /usr/local/include/node/openssl/rc2.h
  /usr/local/include/node/openssl/rc4.h
  /usr/local/include/node/openssl/ripemd.h
  /usr/local/include/node/openssl/rsa.h
  /usr/local/include/node/openssl/safestack.h
  /usr/local/include/node/openssl/seed.h
  /usr/local/include/node/openssl/sha.h
  /usr/local/include/node/openssl/srp.h
  /usr/local/include/node/openssl/srtp.h
  /usr/local/include/node/openssl/ssl.h
  /usr/local/include/node/openssl/ssl2.h
  /usr/local/include/node/openssl/ssl23.h
  /usr/local/include/node/openssl/ssl3.h
  /usr/local/include/node/openssl/stack.h
  /usr/local/include/node/openssl/symhacks.h
  /usr/local/include/node/openssl/tls1.h
  /usr/local/include/node/openssl/ts.h
  /usr/local/include/node/openssl/txt_db.h
  /usr/local/include/node/openssl/ui.h
  /usr/local/include/node/openssl/ui_compat.h
  /usr/local/include/node/openssl/whrlpool.h
  /usr/local/include/node/openssl/x509.h
  /usr/local/include/node/openssl/x509_vfy.h
  /usr/local/include/node/openssl/x509v3.h
  /usr/local/include/node/pthread-barrier.h
  /usr/local/include/node/stdint-msvc2008.h
  /usr/local/include/node/tree.h
  /usr/local/include/node/uv-aix.h
  /usr/local/include/node/uv-bsd.h
  /usr/local/include/node/uv-darwin.h
  /usr/local/include/node/uv-errno.h
  /usr/local/include/node/uv-linux.h
  /usr/local/include/node/uv-os390.h
  /usr/local/include/node/uv-sunos.h
  /usr/local/include/node/uv-threadpool.h
  /usr/local/include/node/uv-unix.h
  /usr/local/include/node/uv-version.h
  /usr/local/include/node/uv-win.h
  /usr/local/include/node/uv.h
  /usr/local/include/node/v8-debug.h
  /usr/local/include/node/v8-experimental.h
  /usr/local/include/node/v8-inspector-protocol.h
  /usr/local/include/node/v8-inspector.h
  /usr/local/include/node/v8-platform.h
  /usr/local/include/node/v8-profiler.h
  /usr/local/include/node/v8-testing.h
  /usr/local/include/node/v8-util.h
  /usr/local/include/node/v8-version.h
  /usr/local/include/node/v8.h
  /usr/local/include/node/v8config.h
  /usr/local/include/node/zconf.h
  /usr/local/include/node/zlib.h


//挨个 brew link
Warning: You have unlinked kegs in your Cellar
Leaving kegs unlinked can lead to build-trouble and cause brews that depend on
those kegs to fail to run properly once built. Run `brew link` on these:
  pkg-config
  vim
  libtiff
  libtool
  coreutils
  jansson
  wget
  gdbm
  imap-uw
  freetype
  python
  highlight
  boost
  cscope
  libyaml
  jemalloc
  composer
  perl
  webp
  c-ares
  xz
  unixodbc
  lua
  cunit
  fontconfig
  archey
  spdylay
  htop
  php71
  macvim
  freetds
  autoconf
  gd
  libevent
  rename
  pcre
  imagemagick
  makedepend
  libev
  ruby
  automake

Warning: Homebrew's sbin was not found in your PATH but you have installed
formulae that put executables in /usr/local/sbin.
Consider setting the PATH for example like so

//执行这句，然后 source ~./zshrc
  echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.zshrc

Warning: Some installed formula are missing dependencies.
You should `brew install` the missing dependencies:

//执行这句
  brew install libidn2 libunistring

Run `brew missing` for more details.
```


根据提示完成上文注释中的操作即可，

然后执行以下命令：

```
brew prune  
brew update
```

All Done.



 