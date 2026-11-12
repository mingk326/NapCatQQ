# NapCat 4.18.42 镜像与插件备份

Docker Hub 上的 `mlikiowa/napcat-docker` 已无法拉取（`pull access denied` / 仓库已不可访问）。
本仓库备份了在本机（fnOS）实际运行中的 **NapCat 4.18.42** 容器镜像，以及配套的 NapCat 插件。

## 镜像

```
ghcr.io/mingk326/napcatqq:4.18.42      # 最新（已含插件市场索引补丁）
ghcr.io/mingk326/napcatqq:4.18.42-r2   # 同上，同一镜像的显式补丁标签
```

拉取：

```bash
docker pull ghcr.io/mingk326/napcatqq:4.18.42
```

说明：

- 镜像由运行中容器打包而来（`docker commit`），基础层为 `mlikiowa/napcat-docker`（4.18.41），
  上层覆盖为手动升级后的 4.18.42 程序文件，因此版本为 **4.18.42**。
- 运行数据（`/app/napcat/config`、`/app/.config/QQ`）不包含在镜像内，务必用卷挂载持久化。
- 首次运行需扫码登录 QQ；WebUI 端口为容器内 `6099`。

### 已内置的补丁（r2 起）

原作者删除 `NapNeko/napcat-plugin-index` 后，NapCat 内置插件市场索引地址全部 404。
本镜像在构建时把 `/app/napcat/napcat.mjs` 中的索引地址替换为本仓库镜像：

```
https://raw.githubusercontent.com/NapNeko/napcat-plugin-index/main/plugins.v4.json
→ https://ghproxy.net/https://raw.githubusercontent.com/mingk326/NapCatQQ/main/deploy/napcat-plugin-index/plugins.v4.json
```

- 原文件备份为 `/app/napcat/napcat.mjs.orig`
- 索引备份说明见 [../napcat-plugin-index/README.md](../napcat-plugin-index/README.md)
- 使用该镜像无需再在容器内打补丁；若换回官方/其它镜像，需重新执行补丁步骤

## 使用示例

见 [docker-compose.example.yaml](docker-compose.example.yaml)：

```bash
cp docker-compose.example.yaml docker-compose.yaml
# 按需修改端口与挂载路径
docker compose up -d
```

WebUI 地址：`http://<宿主机IP>:6200/webui?token=<webui.json 中的 token>`

`webui.json` 位置：`<napcat配置目录>/webui.json`，修改后需重启容器生效。

## 端口规划（本机实际部署）

| 实例 | NapCat WebUI | AstrBot 面板 |
|---|---|---|
| 原始 | 6099 | 6185 |
| NapCat1 | 6200 | 6187 |
| NapCat2 | 6199 | 6186 |
| NapCat3 | 6201 | 6188 |
| NapCat4 | 6202 | 6189 |
| NapCat5 | 6203 | 6190 |
| NapCat6 | 6204 | 6191 |
| NapCat7 | 6205 | 6192 |
| NapCat8 | 6206 | 6193 |

NapCat 与 AstrBot 通过 Docker 网络内反向 WebSocket 连接：`ws://astrbot:6199/ws`（`MODE=astrbot` 自动配置）。

## 插件

### plugins/napcat/napcat-plugin-cleaner

NapCat WebUI 的清理插件，用于清理缓存 / 临时文件。

安装：把 `napcat-plugin-cleaner` 目录放到 NapCat 配置目录下的 `plugins/` 中，重启容器。

```
<配置目录>/plugins/napcat-plugin-cleaner/
├── index.mjs
├── package.json
└── webui/
    └── dashboard.html
```

## 版本与来源

| 项目 | 值 |
|---|---|
| NapCat.Core | 4.18.42 |
| 基础镜像 | mlikiowa/napcat-docker:latest（2026-10-21 构建，镜像内 4.18.41） |
| 打包时间 | 2026-11-12 |
| 索引补丁版本 | `4.18.42-r2`（`:4.18.42` 已指向同一镜像） |
| digest | `sha256:782098651c5450a88a72c0f0e8ef2e7724cfa04c6319214d764a72bf85d70112` |
| 公开状态 | public，可匿名拉取（已验证） |

