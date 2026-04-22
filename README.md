# DDK 构建 GKI 内核的树内模块

## 用法

1. fork 本仓库，启用 actions  
2. 运行 Build Module ，填写你需要的内核分支（必须是 ddk 支持的）、要构建的树内模块的相对路径
   （如 net/netfilter/ipset）、需要传给 scripts/config 的选项（如 `-m CONFIG_IP_SET` 表示 `CONFIG_IP_SET=m`） 
3. 运行，等待成功后从 actions run 下载 modules ，包含构建的模块 .ko 的压缩包。

[DDK](https://github.com/Ylarod/ddk) 支持的内核分支：android12-5.10, android13-5.10, android13-5.15, android14-5.15, android14-6.1, android15-6.6, android16-6.12

**注意**：workflow 使用 gki_config 编译，如果是厂商特有的内核不保证编译出来的内核模块能够兼容。

例如小米 17 的 6.12 内核未打开 CONFIG_NETFILTER_XTABLES_COMPAT （GKI 内核是打开的），从而导致编译出来的 ip_set, xt_set 等中的 xt_af 结构不正确，解决方法是增加 scripts/config 选项 `-d NETFILTER_XTABLES_COMPAT` 来禁用它。

如果编译其他内核模块存在不兼容的现象，可以检查你的内核的 `/proc/config.gz` 中，与这个内核模块相关的配置是否与 gki 内核的 gki_config 一致，并增加 scripts/config 命令行选项保证使用相同的配置。

## 加载模块

由于 GKI 内核存在[导出符号裁剪](https://blog.xzr.moe/archives/236/#section-3:~:text=CONFIG_TRIM_UNUSED_KSYMS)，因此不一定能直接用 insmod 加载。可以使用 `ksud debug insmod` 进行加载。非 KernelSU 用户可下载 KernelSU 最新 apk ，解压 lib/arm64-v8a/libksud.so 获取 ksud 。也可以使用 [lkmloader](https://github.com/Kernel-SU/lkmloader.ko) 加载模块。

## TODO

目前只能构建 ddk 中的源码的模块，可以考虑增加从其他仓库拉取源码，或者构建树外模块的支持。
