# AX300-WiFi-Adapter-Linux-Driver

适用于 Tenda W311MIv6.0 USB 无线网卡（可能也适用于 Tenda U2，我没测过，
但它们的确是同一个驱动）。本仓库将其 deb 包解包并适配 Debian 12 系统。

官方驱动地址：https://www.tenda.com.cn/material/show/629679242969157

修改内容包括：

- 修改 `usr/src/tenda/aic8800/drivers/aic8800/aic8800_fdrv/rwnx_rx.c` 以通过编译
- 删除 `usr/src/tenda/aic8800/aicrf_test` 中没用的 `Android.mk` 和 `aicrf_test.c`
- 添加 `usr/src/tenda/aic8800/aicrf_test` 中的 uninstall 规则
- 在 `DEBIAN/prerm` 中添加对上条 uninstall 规则的调用，使卸载时无残留

第一条修改如下所示，`ieee80211_amsdu_to_8023s` 函数签名已在 Debian 12
内核头文件中 backport 了，但代码中用宏判断内核版本没有做特殊处理。

```diff
--- a/usr/src/tenda/aic8800/drivers/aic8800/aic8800_fdrv/rwnx_rx.c
+++ b/usr/src/tenda/aic8800/drivers/aic8800/aic8800_fdrv/rwnx_rx.c
@@ -473,7 +473,7 @@
                                  RWNX_VIF_TYPE(rwnx_vif), 0, NULL, NULL, false);
 #else
         ieee80211_amsdu_to_8023s(skb, &list, rwnx_vif->ndev->dev_addr,
-                                 RWNX_VIF_TYPE(rwnx_vif), 0, NULL, NULL);
+                                 RWNX_VIF_TYPE(rwnx_vif), 0, NULL, NULL, false);
 #endif
 
         count = skb_queue_len(&list);

```

使用方法：
```bash
dpkg-deb -b ax300-wifi-adapter-linux-driver
sudo apt install ./ax300-wifi-adapter-linux-driver.deb
```

已知问题：
- 每次更新 `linux-image` 和 `linux-headers` 包后需要重新安装

