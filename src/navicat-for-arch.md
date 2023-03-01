<h1 align="center">Arch Linux 上使用 Navicat</h1>

Navicat 15 连接 PostgreSQL 15.2+ 会报错，似乎是兼容性问题，不得不升级 Navicat 16。

[曾经的破解站](https://rlds.tk/)已经打不开了，不过又找到了新的：[navicat.rainss.cc](https://navicat.rainss.cc/)。

破解 Navicat 16 的原理应该是替换写在程序中的公钥，再用注册机算出激活码，如果不手动 Patch 就只能安装破解站提供的修改版本体。不论从安装还是更新角度都略显麻烦，安全性也不能让人放心。如果不依靠搞逆向，并且能让 AUR Helper 自动处理一切更新就会省心很多。因此决定改用无限试用的方法规避激活。

我安装的是 `navicat16-premium-cs` 这个包。

使用 `navicat-reset-trial` 脚本重置激活信息：

```bash
#!/bin/bash
dconf reset -f /com/premiumsoft/navicat-premium/
rm -f -- $HOME/.config/navicat/Premium/preferences.json*
```

使用 `navicat16-premium-cs` 脚本启动程序并关闭激活弹窗：

```bash
#!/bin/bash
navicat-reset-trial
while true; do
  xdotool windowquit $(xwinid '注册') && exit 0
  sleep 0.5
done &
exec /opt/navicat16-premium-cs/AppRun
```

使用 Pacman Hook `navicat16-premium-cs.hook` 自动替换 `navicat16.desktop` 的 `Exec` 字段：

```toml
[Trigger]
Operation=Install
Operation=Upgrade
Type=Package
Target=navicat16-premium-cs

[Action]
Description=Hacking for unlimited trial...
When=PostTransaction
Exec=/usr/bin/sed -i 's|^Exec=.*$|Exec=navicat16-premium-cs|g' /usr/share/applications/navicat16.desktop
```



