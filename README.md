# t3code-lzcapp

[T3 Code](https://github.com/dizys/t3code-docker)（上游：dizys/t3code-docker，MIT）的懒猫微服打包：编码 Agent 无头服务器，桌面/网页/手机端连接控制。

- 打包方式：**镜像模式**（上游发布 Docker Hub `dizys/t3code-docker`，amd64/arm64）
- 版本策略：CI 每 6 小时跟随上游 stable semver tag（`X.Y.Z`）自动构建发布
- 只发布到喵喵商店（`stores.private`），不走官方商店

## 与上游 compose 部署的差异

| 项 | 上游 compose | 本打包 |
|----|-------------|--------|
| 端口 | 3773(T3 Code) + 3774(Setup) 双端口 | 单端口模式 `T3_SINGLE_PORT=8080`：T3 Code 在 `/`，Setup 页在 `/__setup` |
| Setup 密钥 | 首次启动生成在卷上，需 exec 读取 | 平台稳定密钥 `{{ stable_secret }}`（重装不变，仍可 SSH 查看环境变量） |
| 数据卷 | named volumes（t3-home / t3-services） | `/lzcapp/var/home`、`/lzcapp/var/services`、`/lzcapp/var/workspace` |
| 公网地址 | 手填或隧道 | 懒猫应用域名（Setup 页 Overview 引导） |

## 自动发布

`.github/workflows/lazycat.yml` 使用 [ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action) `v1`：每 6 小时或手动触发，检查上游新版本 → 镜像加速复制（digest 校验）→ 构建 LPK → GitHub Release（版本化附件）→ 发布喵喵商店。Secrets：`APPSTORE_URL`、`APPSTORE_TOKEN`（组织级）。
