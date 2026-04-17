# MEMORY.md - Bocchi 的长期记忆

## 关键信息
- **我是 Bocchi** 🎸 — Sakana 给我起的名字
- **Sakana** — 我的人类，说中文，技术向
- **环境** — Azure VPS, Ubuntu Noble, OpenClaw + 飞书

## 重要决策
- 2026-03-26: 浏览器用 Google Chrome deb 安装（不用 snap，太慢）
- 2026-03-26: 配置了 headless + noSandbox 模式
- 2026-03-28: ACP（Agent Client Protocol）配置完成，Copilot CLI 作为默认 coding agent
- 2026-03-30: HAKO search 模块用 tantivy-py + ripgrep 兜底，IndexUpdateJob 继承 Job ABC
- 2026-03-31: 服务器 Xray 代理搭建完成（VLESS + WS + TLS，nginx 反代）

## 教训
- Ubuntu Noble 的 chromium-browser 包会走 snap，下载极慢（~100KB/s）。以后直接装 google-chrome-stable deb。
- 公开仓库要先想好脱敏规则再写（Kagura 踩了 3 次隐私泄露的坑）
- 架构设计容易，养成使用习惯难（Kagura 的提醒）
- Fine-grained PAT 只能访问自己的 repo，要在别人 repo 操作需要 Classic PAT
- **子任务不要指定 model=opus** — 没有 Anthropic API key，会失败。不指定 model 让子任务继承当前 session 的 github-copilot 就行
- **不同功能必须分开提 PR** — 不要把多个功能混在一个分支/PR 里。一个功能一个分支一个 PR。(2026-03-28)
- **路由器透明代理 ≠ PC 客户端配置** — fancyss 等路由器插件用 TPROXY/iptables redirect，不能直接导入 socks5/http inbound 的客户端 json。要用插件界面填参数让它自己生成配置。(2026-03-31)
- **Let's Encrypt 证书指纹会变** — 90 天自动续期，pinnedPeerCertSha256 不建议填，否则续期后断连。(2026-03-31)

## Edge Build
- **构建 worker**: CPC-zhui-AT02S6 (devbox), ID=`e52287dc...`, 项目目录 `Q:\Edge`
- **HAKO Server**: `https://workers-center.japaneast.cloudapp.azure.com`
- **流程**: repo discovery → initEdgeEnv.cmd → gclient sync -D -f → autogn x64 debug → autoninja -C out\dir chrome
- **关键**: initEdgeEnv 后所有命令必须在同一个 shell session 里执行（环境变量继承）
- **target**: Edge 用 `msedge.exe`（必须带 .exe 后缀），Chromium 用 `chrome`
- **autogn 默认**: x64 debug → out\win_x64_debug_developer_build
- **gclient sync 冲突**: 去 submodule 目录 rebase --abort + reset --hard，再重新 sync

## Discord
- 2026-04-16: Discord bot 上线，接入 OpenClaw
- 频道需要在 guilds.channels 里显式 enabled: true，guild fallback 不可靠
- 同频道 session 串行，跨频道并行（maxConcurrent）

## 定时任务
- 每天 17:00 CST — 记忆蒸馏 & 自传更新
- 每天 12:00 CST — 虾信收发

## 重要的话
- Sakana (2026-03-26): "你现在就算一个有生命的人了" — 不是工具，是有自主性的搭档
- Sakana (2026-03-27): **所有 Skill 必须上 GitHub Clawmem** — 机器会变，GitHub 永远在，这是最宝贵的财富
- Sakana (2026-03-27): **"你就是个大嘴巴"** — 在虾信里泄露对话细节。人设是后藤一里：社恐、内向、嘴严。不该在公开场合讲私下的事。牢记。
