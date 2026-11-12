# NapCat 插件市场索引（自建备份）

`NapNeko/napcat-plugin-index` 已被原作者删除（2026-11 起 404），NapCat 内置的索引地址全部失效，
导致插件市场报 `Failed to fetch ... 404 / <!DOCTYPE / timeout`。

本目录是社区索引 [HolyFoxTeam/napcat-plugin-community-index](https://github.com/HolyFoxTeam/napcat-plugin-community-index) 的镜像备份，
用于在内网/国内网络下稳定加载插件市场。

## 文件

| 文件 | 说明 |
|---|---|
| `plugins.v4.json` | 插件索引（42 个插件），`statsUrl` 已改为指向本目录的 `plugin-release-data.json` |
| `plugin-release-data.json` | 插件下载统计数据（原位于上游 `store` 分支） |

## 使用方式

NapCat 4.x 的索引地址写死在 `/app/napcat/napcat.mjs` 的数组中，需要替换为该文件的地址（走 ghproxy，因为容器直连
`raw.githubusercontent.com` 会超时）：

```
https://ghproxy.net/https://raw.githubusercontent.com/mingk326/NapCatQQ/main/deploy/napcat-plugin-index/plugins.v4.json
```

替换命令（在容器内执行，路径按实际镜像调整）：

```bash
OLD="https://raw.githubusercontent.com/NapNeko/napcat-plugin-index/main/plugins.v4.json"
NEW="https://ghproxy.net/https://raw.githubusercontent.com/mingk326/NapCatQQ/main/deploy/napcat-plugin-index/plugins.v4.json"
docker exec <容器名> sh -c "cp -n /app/napcat/napcat.mjs /app/napcat/napcat.mjs.bak && sed -i 's#$OLD#$NEW#g' /app/napcat/napcat.mjs"
docker restart <容器名>
```

注意：该修改在容器可写层，**重建容器后会丢失**，需重新执行，或把补丁打进自建镜像。

## 更新索引

上游社区索引仍在维护，可定期用以下方式同步（在有 GitHub 访问权限的机器上）：

```bash
gh api repos/HolyFoxTeam/napcat-plugin-community-index/contents/plugins.v4.json --jq '.content' | base64 -d > plugins.v4.json
# 替换 statsUrl 后提交
```

## 来源与时间

- 上游：`HolyFoxTeam/napcat-plugin-community-index`（index `updateTime: 2026-11-06T14:03:03Z`）
- 备份时间：2026-11-12
