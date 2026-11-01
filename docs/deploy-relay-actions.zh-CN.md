# 在 fork 中编译中转镜像

服务器只运行镜像，不需要安装 Node 或 Rust。Relay 与手机 PWA 均从本 fork 的源码构建。

1. 在 fork 的 **Actions → Build relay image → Run workflow** 选择要构建的分支。
2. 成功后，在 Releases 中打开对应的 `relay-<提交前12位>`，下载 `mini-term-relay-linux-amd64.tar.gz`。`SOURCE_COMMIT.txt` 记录完整源码提交。
3. 把镜像包放到 Linux amd64 服务器，导入：

   ```bash
   gzip -dc mini-term-relay-linux-amd64.tar.gz | docker load
   ```

4. 按[中转部署指南](deploy-relay.zh-CN.md)配置密钥与 TLS。在 Compose 中删除 `build` 配置，并将 `image` 改为 `mini-term-relay:<SOURCE_COMMIT.txt 中的完整提交>`。启动时使用 `docker compose up -d --no-build`。

流水线只编译中转与 PWA，不编译桌面端。发布前会运行 Relay 协议测试、PWA 类型检查及构建，以及打包镜像的健康检查、首页、非 root 用户与无挂载检查。构建时不需要生产密钥，实际密钥仅配置在部署服务器和桌面端。

上游同步后，重新运行此工作流即可构建新镜像。升级前保留旧镜像标签与部署配置，便于回退；重建中转后，手机需要重新扫码配对。
