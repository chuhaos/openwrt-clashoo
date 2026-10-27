# Feed 公钥

这里存放 `down.dllkids.xyz` OpenWrt feed 的公钥副本，用来给用户独立核对 `scripts/install.sh` 里内置的信任锚点。

| 文件 | 用途 | SHA-256 |
|---|---|---|
| `dllkids-feed.pub.pem` | OpenWrt 25.12+ 的 apk 索引与包索引签名（ECDSA P-256） | `ea9dd08b41e6b0fc7ffda166a89b2b4904ad5607bf77d82c9f7f2a54e63a8948` |
| `dllkids-feed.pub` | 24.10 及更早的 opkg `Packages.gz` 签名（usign Ed25519） | `216565b4d2c50f90da39ee1b19f1c6d2de0392c0043ca9bea311ac831a44779e` |

opkg 公钥 ID（即 `/etc/opkg/keys/` 下的文件名）：`f2a0779946829e7a`

## 怎么核对

安装脚本只信包里写死的这两个 SHA-256，下载公钥后先比指纹再用，不匹配就直接退出。

```sh
# 从 feed 取（和脚本走的是同一个源）
curl -fsSLO https://down.dllkids.xyz/openwrt-feed/keys/dllkids-feed.pub.pem
curl -fsSLO https://down.dllkids.xyz/openwrt-feed/keys/dllkids-feed.pub

# 和本目录里的副本比，或和上表指纹比
sha256sum dllkids-feed.pub dllkids-feed.pub.pem
```

线上 feed 与本仓库的副本应当完全一致；两者不一致说明其中一处被改动，应先停下排查。

## 能防什么、不能防什么

- **能防**：链路上的中间人、下载到的公钥被替换、包索引被篡改（索引签名 + 索引里的包摘要）。
- **不能防**：同时攻陷本仓库和 feed 源站的攻击者，以及发布账户失陷。脚本本身也是从网上取的，它无法为自己背书——真要防这一层，只能靠人工核对上面的指纹。

## 轮换

换钥匙时先更新 `scripts/install.sh` 里的 SHA-256 常量，再更新本文件，然后发布新的公钥。必要时可以保留一段时间新旧两枚指纹同时可用作为过渡，但脚本目前是单指纹，过渡期需要发新版本脚本。
