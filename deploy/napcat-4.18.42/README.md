# NapCat 4.18.42 镜像与插件备份

Docker Hub 上的 `mlikiowa/napcat-docker` 已无法拉取（`pull access denied` / 仓库已不可访问）。
本仓库备份了在本机（fnOS）实际运行中的 **NapCat 4.18.42** 容器镜像，以及配套的 NapCat 插件。

## 镜像

```
ghcr.io/mingk326/napcat-docker:4.18.42
```

拉取：

```bash
docker pull ghcr.io/mingk326/napcat-docker:4.18.42
```

说明：

- 镜像由运行中容器打包而来（`docker commit`），基础层为 `mlikiowa/napcat-docker`（4.18.41），
  上层覆盖为手动升级后的 4.18.42 程序文件，因此版本为 **4.18.42**。
- 运行数据（`/app/napcat/config`、`/app/.config/QQ`）不包含在镜像内，务必用卷挂载持久化。
- 首次运行需扫码登录 QQ；WebUI 端口为容器内 `6099`。

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
