### 下载

官网已关闭下载界面, 但是实际下载地址还在

[下载地址](https://d1.music.126.net/dmusic/netease-cloud-music_1.2.1_amd64_ubuntu_20190428.deb)

### 安装

[[archlinux安装deb应用]]


### 问题排查

#### libgio-2.0.so.0: undefined symbol: g_module_open_full

```
$ ldd -r /lib/libgio-2.0.so.0 |grep module
libgmodule-2.0.so.0 => /usr/lib/libgmodule-2.0.so.0 (0x00007f6e4f67b000)

$ sudo cp /usr/lib/libgmodule-2.0.so.0 /opt/netease/netease-cloud-music/libs
```

#### libtiff.so.6: undefined symbol: jpeg12_write_raw_data, version LIBJPEG_8.0

```
$ ldd tree /usr/lib/libtiff.so.6
/usr/lib/libtiff.so.6:
	linux-vdso.so.1 (0x00007ffcdcbc4000)
	libzstd.so.1 => /usr/lib/libzstd.so.1 (0x00007ff51a754000)
	liblzma.so.5 => /usr/lib/liblzma.so.5 (0x00007ff51a721000)
	libjbig.so.2.1 => /usr/lib/libjbig.so.2.1 (0x00007ff51a714000)
	libjpeg.so.8 => /usr/lib/libjpeg.so.8 (0x00007ff51a677000)
	libz.so.1 => /usr/lib/libz.so.1 (0x00007ff51a65d000)
	libm.so.6 => /usr/lib/libm.so.6 (0x00007ff51a56f000)
	libc.so.6 => /usr/lib/libc.so.6 (0x00007ff51a387000)
	/usr/lib64/ld-linux-x86-64.so.2 (0x00007ff51a8de000)

$ readelf -sW /usr/lib/libjpeg.so.8 | grep jpeg12_write_raw_data
182: 000000000001fa10   276 FUNC    GLOBAL DEFAULT   10 jpeg12_write_raw_data@@LIBJPEG_8.0

$ sudo cp /usr/lib/libjpeg.so.8 /opt/netease/netease-cloud-music/libs
```

#### libpangocairo-1.0.so.0: undefined symbol: pango_font_get_hb_font

```
$ sudo cp /usr/lib/libpango-1.0.so.0 /opt/netease/netease-cloud-music/libs
$ sudo cp /usr/lib/libpangoft2-1.0.so.0 /opt/netease/netease-cloud-music/libs
```


#### libpango-1.0.so.0: undefined symbol: fribidi_get_par_embedding_levels_ex

```
sudo cp /usr/lib/libfribidi.so.0 /opt/netease/netease-cloud-music/libs
```

