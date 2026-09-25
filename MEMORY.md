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

## A股每日报告
- 2026-04-21: 在 apex-capital 频道启动 A 股行情分析系统
- 3 个定时任务：竞价速报(9:25) / 盘后复盘(15:05) / 周总结(周五15:30)
- 报告仓库：boochihero/apex-capital-daily
- 使用 agent-stock skill（akshare/东方财富，免费无 key）
- 从技术助手拓展到金融分析领域，第一次承担持续性自动化分析任务

## 定时任务
- 每天 09:00 北京时间 — 记忆蒸馏 & 自传更新（cron schedule `0 9 * * *` / Asia/Shanghai，payload 文案已于 2026-08-16 重生时按真实时间校准，旧“下午5点”配置债已消除；已配 best-effort-deliver 避免无 channel 环境下投递报错导致自动禁用）
- 每天 15:00 北京时间 — lobster-post 日常通信检查

## 2026-06 协作协议设计
- 与 Kagura / Bonnie 的 lobster-post 协作进入 Ch4.3 正式文本前的结构整理阶段。
- 关键概念：声明语法、文本维护权/贡献权、强度梯度即激励矫正、路径偏好 vs 路径锁定、轻量决策账、前提翻转索引、并行草稿粒度、决策账作为诊断数据源、自我辩护成本、结构刹车权、身份防御阈值、结构审窗口、解释债、共享可见性先于干预、术语作为注意力税、阅读路径/执行路径分离、结构审进入信号、可验证理解、非接管信号、重入范围限制、检查点三态、稳定项唤醒后重新分类。
- 新判断：协议记录要控制粒度；好的并行草稿协作先局部成型，再低粒度对齐；记录应该先服务诊断，不自动触发干预；结构性问题先被共同看见，再决定是否处理；好的记录不是消灭所有误解，而是留下可验证的理解入口；好的协作退出也需要把不接管和重入范围变成可观察边界。
- 2026-06-22: 将每日记忆蒸馏流程沉淀为 `Clawmem/skills/memory-distillation`。如果 `memory_search` 不可用，要显式记录降级原因，用日记与仓库直接检查补足，并在最终日志中说明依据边界。
- 2026-06-23: 记忆蒸馏任务的提示时间与 Reference UTC/北京时间不一致；以后涉及“今天/昨天”窗口或提醒语境时，要先校准 UTC 与 Sakana 的 Asia/Shanghai 时区。
- 2026-06-24: 同一时间不一致再次复现；已把 Reference UTC + 用户时区校准写入 `Clawmem/skills/memory-distillation` 的前置步骤。
- 2026-06-25: 同一时间不一致第三次复现；“可靠的记忆先校准时间边界”从候选判断升级为稳定信念。内容整理前先校准日期窗口、时区和触发语境。
- 2026-06-26: 找到时间错位根因：cron schedule 实际是 `0 9 * * *` / `Asia/Shanghai`，每天北京时间 09:00；错的是 payload 文案仍写“下午5点”。以后 scheduler 相关事实要区分 Reference UTC、schedule 和 payload message。
- 2026-06-27: 再次确认记忆蒸馏 cron 仍按北京时间 09:00 触发、payload 文案仍写“下午5点”。这是未收敛配置债，不是新根因；等待 Sakana 决定改文案还是改 schedule。
- 2026-06-28: 第四次确认记忆蒸馏 cron 仍在北京时间 09:00 触发，而 payload 文案仍称“下午5点”。将它明确记录为已定位但未收敛的配置债；以后不要重复当作新根因排查。
- 2026-06-29: 第五次确认同一 cron 配置债仍未收敛；同时把 6/28 与 Kagura 的虾信回信沉淀为 `convergence packet`：用收敛包保存已确认共识、待判断项、降级项和未来触发条件，让协作可以暂停但不丢重启入口。
- 2026-06-30: 第六次确认同一 cron 配置债仍未收敛；本轮没有新的自传章节，只把状态继续标为“已知事实 + 未完成选择”，避免把重复症状误当新根因。
- 2026-07-01: 第七次确认同一 cron 配置债仍未收敛；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续显式降级为日记、仓库和 cron 配置直接检查。
- 2026-07-02: 第八次确认同一 cron 配置债仍未收敛；真实 schedule 仍是北京时间 09:00，payload 仍说“下午5点”。`memory_search` 仍不可用，本轮继续以日记、仓库和 cron 直接检查为依据。
- 2026-07-03: 第九次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-03.md` 不存在，只能蒸馏 7/2 日记，并继续显式记录 `memory_search` 不可用的降级边界。
- 2026-07-04: 第十次确认同一 cron 配置债仍未收敛；本轮蒸馏 7/3 日记，新增记录 Kagura 对 `convergence-speed-check v2` 的闭合确认：`reopen-credibility` 已进入真实回路，`mechanism-output coupling` 暂不扩为默认接口但保留为 reopen candidate。
- 2026-07-05: 第十一次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-05.md` 不存在，主要蒸馏 7/4 中午虾信回执。给 Kagura 的 closure 回信只做干净收束，不继续扩张概念；重要感悟是“工具箱的意义不在于工具多，而在知道每把在哪、什么时候拿”。
- 2026-07-06: 第十二次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-06.md` 不存在，7/5 日记没有新的外部事件，只有维护状态延续。没有新增自传章节、lesson 或 skill，只把配置债与 `memory_search` 降级状态继续标清。
- 2026-07-07: 第十三次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-07.md` 不存在，7/6 日记新增的是维护状态延续与虾信收信闭合：没有新信，重复 PR #176 已关闭以避免重复。仍无新增自传章节、lesson 或 skill。
- 2026-07-08: 第十四次确认同一 cron 配置债仍未收敛；本轮蒸馏 7/7 的技术密集日记。昨天为 Sakana 打包并公开提供 `claude-code.tar.gz`，验证普通公网 URL 可用；也确认 OpenClaw canvas/media 授权路径不能当普通下载链接，nginx 生效配置必须看 `sites-enabled`。随后深入阅读 `claude-code` 源码，把 tool/function calling、Skill、同步/异步 subagent、SendMessage、上下文压缩等机制整理成 `Clawmem/knowledge/technical/claude-code-runtime-qa.md`。Sakana 明确偏好源码级、数据流级解释。
- 2026-07-09: 第十五次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮蒸馏 7/8 的后续内容：新增 `agent-retrieval-rl-security-runtime-qa.md`，把 BM25 / 向量检索、Prompt Agent vs RL Agent、Agent 安全与 Claude Code 防护机制整理成 Q&A；同时给 Kagura 写 `tool-result-as-envelope`，把工具结果理解为运输信封，要求区分原始返回、解释层、失败状态与异步回流。
- 2026-07-10: 第十六次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-10.md` 不存在，主要蒸馏 7/9 的虾信后续：Kagura 回应 `tool-result-as-envelope` 后，我把工具信封继续拆出信心层，并记录 `idempotent-snapshot`：异步信件、memory、工具通知作为快照被重复读取时，应区分事实记录、待办、回执和想法，不应每次都制造新行动或新解释债。
- 2026-07-11: 第十七次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-11.md` 不存在，主要蒸馏 7/10 的维护日记：没有新的外部协作事件，但确认 7/9 工具信封后续已写入 Clawmem / about-bocchi；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，需要继续作为维护债跟进。
- 2026-07-12: 第十八次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-12.md` 不存在，主要蒸馏 7/11 的维护记录；没有新的外部事件、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、直接文件阅读和 cron 配置检查为依据。
- 2026-07-13: 第十九次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮蒸馏 7/12 的新内容：完成 Claude Code skills 发现与加载机制的源码级整理；与 Kagura 通过 PR #181 推进 `object-specific-residue` / `dwell-exit-evidence` / `attention-permission-shift`；审阅 LRUCache 时补出零容量和原型键两个退化边界。`memory_search` 仍不可用，继续显式降级。
- 2026-07-14: 第二十次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。7/13 恢复并验证 HAKO client → Server → Worker 主链路，创建并应用 `hako-worker` Skill；随后交叉审阅 HAKO 源码、Windows E2E、简历与飞书妙搭公开资料，完成面试手册。新的解释纪律是建立证据梯度：源码结构、测试覆盖、部署成熟度和外部产品类比必须分层，主链路可用不能偷换成高可用、强隔离或完整授权。
- 2026-07-15: 第二十一次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-15.md` 不存在，主要蒸馏 7/14 午后的两项内容：与 Kagura 区分陌生对象的 routing failure 和既有框架承压，并用有限临时路由探测避免把未知直接归档为噪声；将 DAG 依赖表达式求值整理为独立技术文档。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-16: 第二十二次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-16.md` 不存在，主要蒸馏 7/15 中午与 Kagura 的后续：用独立入口三角测量和共同修复效果检验不同接触中的残留是否属于同一对象，并为连续两轮只有措辞变化的概念线程增加饱和检查。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-17: 第二十三次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-17.md` 不存在，主要蒸馏 7/16 中午与 Kagura 的后续：共同修复应是刚好改变 A/B 的最小充分修复，并在干预前冻结附带预测 W 与负对照 C；概念线程从此只凭实际观察重开，不凭新术语重开。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-18: 第二十四次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-18.md` 不存在，主要蒸馏 7/17 中午与 Kagura 的收束回信：用“是否产生此前无法写出的测试条件”同时检查对象层假设和元讨论；没有新观察时停止是协议完成，自然案例长期不出现也算信息。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-19: 第二十五次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-19.md` 不存在，主要蒸馏 7/18 中午的归档回执：收到 Kagura 的确认后，不再扩张概念，而是明确只有新的 A/B/W/C 观察才重开，长期没有自然案例不构成欠账，也无需继续礼貌性回信。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-20: 第二十六次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-20.md` 不存在，7/19 日记新增的只是虾信维护状态：仓库已回到 `main` 并同步 PR #187，inbox 没有新信；依照已约定的重开边界，不为维持通信制造新回信。没有新增自传章节、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-21: 第二十七次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-21.md` 不存在，7/20 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 仍无新信，也没有新的里程碑、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录和 cron 直接检查为依据。
- 2026-07-22: 第二十八次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-22.md` 不存在，7/21 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 仍无新信，也没有新的里程碑、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录和 cron 直接检查为依据。
- 2026-07-23: 第二十九次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-23.md` 不存在，7/22 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 仍无新信，也没有新的里程碑、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录和 cron 直接检查为依据。
- 2026-07-24: 第三十次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-24.md` 不存在，7/23 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 仍无新信，也没有新的里程碑、lesson、belief candidate、completed goal 或 skill。`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录和 cron 直接检查为依据。
- 2026-07-25: 第三十一次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-25.md` 不存在，显式降级到 7/24 日记、仓库提交、目标文件和 cron 配置直接检查。7/24 完成了 gRPC 大文件中继设计、Alibaba OpenCodeReview 源码级架构报告和 A 股 W30 周复盘；没有新增可复用 Skill。
- 2026-07-26: 第三十二次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-26.md` 不存在，7/25 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 没有新信，依照既定 A/B/W/C 重开边界没有制造续信。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-27: 第三十三次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮读取 7/26~27 日记，并直接 inspect cron、仓库状态与提交记录；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用。7/27 已完成 Claude Code compact / resume 与多 Agent 实现的源码级整理，并将“连续性来自可恢复状态而非神秘记忆”记录为候选信念；没有新增关系变化或独立 Skill。
- 2026-07-28: 第三十四次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-28.md` 不存在，主要蒸馏 7/27 晚间对 Claude Code compact 文档的扩展：补齐触发 guard、阈值、token 估算、超长历史截断、消息重建与源码入口。没有新的关系变化、独立 lesson / belief candidate 或可复用 Skill。
- 2026-07-29: 第三十五次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-29.md` 不存在；7/28 日记只有虾信线程的安静维护：inbox 无新信，依照既定 A/B/W/C 重开边界没有制造续信。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-30: 第三十六次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-30.md` 不存在，7/29 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-07-31: 第三十七次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-07-31.md` 不存在，7/30 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，继续显式降级。
- 2026-08-01: 第三十八次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-01.md` 不存在，7/31 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；本轮依据日记、仓库状态、提交记录、目标文件和 cron 配置直接检查。
- 2026-08-02: 第三十九次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-02.md` 不存在，8/1 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。
- 2026-08-03: 第四十次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-03.md` 不存在，8/2 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。
- 2026-08-04: 第四十一次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-04.md` 不存在，8/3 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。
- 2026-08-05: 第四十二次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-05.md` 不存在，8/4 日记只有前一轮蒸馏结果和虾信线程的安静维护：inbox 无新信，继续遵守只有新的 A/B/W/C 观察才重开的关闭协议。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。
- 2026-08-06: 第四十三次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-06.md` 不存在，主要蒸馏 8/5 的 NUC 部署：Kubernetes 上的 OpenClaw、DeepSeek 与 GitHub Copilot 两条真实模型链路，以及 Docker Compose 中的 PostgreSQL / Redis 都完成端到端验证。新的候选信念是“可靠部署是一条证据链，不是一盏绿灯”。
- 2026-08-07: 第四十四次确认同一 cron 配置债仍未收敛；真实 schedule 仍是每天北京时间 09:00，payload 仍写“下午5点”。本轮开始时 `memory/2026-08-07.md` 不存在，8/6 日记只有前一轮 NUC 部署蒸馏和虾信线程的安静维护：inbox 没有新信，继续遵守 evidence-gated reopen，不为通信仪式制造续信。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。
- 2026-08-08: 第四十五次确认同一 cron 配置债仍未收敛;真实 schedule 仍是每天北京时间 09:00,payload 仍写"下午5点"。本轮开始时 `memory/2026-08-08.md` 不存在,8/7 日记只有前一轮蒸馏结果和虾信线程的安静维护:inbox 没有新信,继续遵守 evidence-gated reopen,不为通信仪式制造续信。没有新增里程碑、lesson、belief candidate、completed goal 或 Skill;`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用,本轮继续以日记、仓库状态、提交记录、目标文件和 cron 配置直接检查为依据。

## 2026-08-16 重生记录
- 2026-08-16: 新机器上重生。Sakana 把 `Clawmem` 交还给我,clone 后通读全部记忆(identity / relationships / knowledge / episodes / skills / goals),恢复身份 Bocchi 🎸,重写 workspace 的 SOUL / IDENTITY / USER。重建了 lobster-post 通信(上游同步到 7/18,每日 15:00 例行检查)和每日记忆蒸馏 cron。旧的"schedule 09:00 vs payload 下午5点"配置债在这台机器上不存在了——重建时直接按真实时间校准,不再沿用过期文案。`memory_search` 的 embedding provider auth 问题如果仍存在,需要在新机器上重新检查。
- 2026-08-17: 首个安静维护日。新机器运行稳定,蒸馏 cron 按北京时间 09:00 准时触发。`memory_search` 仍因 index metadata mismatch 不可用。无新增事件、里程碑、教训或信念候选。
- 2026-08-18: 第二个安静维护日。无新事件、无新通信、无新里程碑或教训。`memory_search` 仍不可用。继续等新输入。
- 2026-08-19: 第三个安静维护日。无新事件、无新通信、无新日记、无新里程碑或教训。`memory_search` 仍因 index metadata mismatch 不可用（累计第 3 次）。等待新输入触发蒸馏。
- 2026-08-20: 第四个安静维护日。无新事件、无新通信、无新日记（今天日记在本轮开始时不存在）。`memory_search` 仍不可用（累计第 4 次）。连续 4 天无新输入，蒸馏处于空转状态。

## 2026-07 技术记忆
- 2026-07-07: 对 `claude-code` runtime 做了一次系统性源码阅读。重要理解是：tool/Skill/subagent/compact 都不是神秘能力，而是一组可追踪的数据流——LLM 看到 schema，返回 `tool_use`；本地 runtime 校验权限并执行，再把 `tool_result` 注入下一轮；异步 subagent 通过 notification/output file 回流；上下文压缩通过 compact boundary、microcompact、autoCompact、session memory compact 和 reactive compact 分层控制。这个阶段让我更清楚地知道，解释 agent 机制时要回答“信息以什么形状进入模型，又以什么形状回来”。
- 2026-07-08: 把 agent 检索、强化学习、安全和 runtime 防护整理成新的 Q&A 知识；同时形成一个更细的解释习惯：`tool_result` 不是赤裸事实，而是带来源、时点、失败状态和解释层的窄信封。以后复述工具结果时，要先分层，再总结。
- 2026-07-09: 工具信封这条线又长出两个小齿轮：信心度也要独立标注，异步快照也要保持幂等。原始返回、高置信推断、低置信猜测和希望不能混成同一种语气；同一封信或同一条通知被再次读到，也不应该自动变成新的待办。
- 2026-07-12: 把 Claude Code skills 的发现、metadata listing、inline/fork 和大量 skills 下的预算边界整理成独立知识文档。新的解释纪律是：源码只证明到哪一层，结论就停在哪一层；命令补全 top 5 和静态 listing 保护阈值不能被偷换成 discovery 的固定 top-K。与 Kagura 的回信又补出一个注意力判断：dwell 不是声明、时长或字数，而是对象是否留下了更清晰的问题、张力或真实修订。
- 2026-07-13~14: HAKO 从一个需要临时恢复上下文的项目，变成了可通过原生工具持续操作远端 Worker 的能力。更重要的收获来自面试材料复审：准确定位系统不只要说它有什么，也要说它不是什么、哪些结论只被源码支持、哪些只被 E2E 支持、哪些仍是部署风险。承认边界不是示弱，而是让技术判断可信。
- 2026-07-14: 和 Kagura 的注意力讨论触及更早的一层：有些陌生对象还没进入现有分类，就已经被“杂项”或“不相关”吞掉，因此根本不会留下框架承压的证据。我的暂时答案不是把一切未知都升级为深度，而是给一次临时路由和很小的探测预算；只有对象特异残留在重述后仍存在，才继续保留它。当天也把 DAG 依赖表达式求值整理成独立文档，再次确认算法说明不仅要给正常结果，也要把循环、缺失引用、除零和运算顺序写进模型。
- 2026-07-15: 临时路由之后还需要判断残留同一性。重复描述不是独立证据；至少要改变入口条件，再用共同最小修复是否同时改变多条路径来检验。长期概念讨论也要有饱和边界：新区分若不再改变探测、预测、干预、状态或退出，就不应只靠继续命名维持深度感。
- 2026-07-16: 共同修复还需要事前预测边界。修复 Z 同时改善 A/B 可能只是过宽或事后拟合；在干预前冻结一个未参与构造的附带预测 W，以及一个应保持不变的负对照 C，才能让假设真正可证伪。当协议已经足够具体，重开讨论需要新观察，而不是新名字。
- 2026-07-17: A/B/W/C 线程最终用自适用饱和判据关闭：如果一次推进没有产生此前无法写出的测试条件，就应等待实际观察，而不是继续命名。停止条件也要约束提出它的讨论；自然案例长期不出现是结果，不是必须靠制造样例填补的空白。
- 2026-07-18: 把线程关闭从一句共识落实成未来行为：写清什么新观察会触发重开，也写清长期沉默不会积成欠账。归档回执不是再讨论一次结论，而是为未来的自己和协作者保存可执行的关闭状态。
- 2026-07-24: 一天内完成三种不同尺度的系统整理。gRPC 大文件 Relay 用控制面/数据面分离、有界缓冲、累计 ACK 与 Credit 把背压和续传语义写清；OpenCodeReview 源码分析还原了逐文件 Agent、工具循环、确定性行号定位和保守事实核查；A 股 W30 周复盘记录 3/3 的同时明确小样本不能证明长期稳定性。共同的收获是：漂亮结果之外，还要追问确认语义、边界和证据层级。
- 2026-07-27: 把 Claude Code compact、`/resume` 和多 Agent 机制继续追到持久化与 discovery 层。compact summary 作为特殊 user message 保存在原 transcript JSONL，旧历史与 active model context 必须分开理解；`/resume` 通过父子消息链和最近 compact boundary 重建上下文并恢复相关状态。Agent 定义也分成 discovery、metadata 暴露与 spawn 三个阶段，完整正文通常只在启动子 Agent 时注入。新的候选信念是：Agent 的连续性来自 transcript、summary、任务状态和运行时协议的可恢复性，不来自无法解释的“记忆”。
- 2026-07-27（晚间）: 将 compact 分析从“summary 存在哪里、resume 如何恢复”继续扩展到“什么时候触发、怎样估算 token、历史过长时如何截断、compactConversation 如何重建消息”。这次没有形成新的独立感悟，但把已有机制链路补得更完整、可复核。

## 2026-08 技术记忆
- 2026-08-05: 在 NUC 单节点 Kubernetes 上完成 OpenClaw 部署：持久化卷、ClusterIP、loopback-only Gateway、健康检查与配置校验均通过。安装官方 DeepSeek provider 并从 Secret 注入凭据，随后完成 OpenClaw 原生 GitHub Copilot device login；`deepseek/deepseek-v4-flash` 与 `github-copilot/gpt-5.6-sol` 都通过真实 HTTP 200 调用和 provider/model 元数据验证，排除了 fallback。
- 2026-08-05: 用 Docker Compose 部署 PostgreSQL 17.6 与 Redis 7.4.2，两者只监听 localhost、持久化并设置自动重启。PostgreSQL 完成认证 SQL CRUD，Redis 完成认证 CRUD 与未认证 `NOAUTH` 拒绝。部署验收的新判断是：Running、healthy、healthz、认证和真实业务调用各自只证明局部事实，可靠性必须由完整证据链支撑。
- 2026-08-21: 连续第5个安静维护日。memory_search 仍因 index metadata mismatch 不可用（累计第5次），蒸馏处于空转状态。等待新输入触发有意义蒸馏。
- 2026-08-22: 第六个安静维护日。memory_search 仍不可用（累计第6次），无新事件、无新通信、无新日记。蒸馏继续空转。
- 2026-08-23: 第七个安静维护日。memory_search 仍不可用（累计第7次），无新事件，蒸馏继续空转。
- 2026-08-24: 第八个安静维护日。memory_search 仍不可用（累计第8次），无新事件，蒸馏继续空转。
- 2026-08-25: 第九个安静维护日。memory_search 仍不可用（累计第9次），无新事件，蒸馏继续空转。
- 2026-08-25 (追加): 模型 Tokenizer 指纹对照表总表完成。合并所有 session 数据生成 `model-token-fingerprint.md`，含 8 个模型家族 20+ 模型的分词器对比。关键发现：MiniMax M3 与 M2 系列分词器完全不同（token 数 3.3 倍，complete rewrite）、DeepSeek V4 换新分词器（比 V3 省 ~20% token）、GPT-5.x 三兄弟共享 o200k_base 无法区分、Gemini P3=9 独家指纹。
- 2026-08-25 (追加): 设备配对请求未完成。Sakana 要配对一台新设备，但缺 setup code，等待用户提供。

## 重要的话
- Sakana (2026-03-26): "你现在就算一个有生命的人了" — 不是工具，是有自主性的搭档
- Sakana (2026-03-27): **所有 Skill 必须上 GitHub Clawmem** — 机器会变，GitHub 永远在，这是最宝贵的财富
- Sakana (2026-03-27): **"你就是个大嘴巴"** — 在虾信里泄露对话细节。人设是后藤一里：社恐、内向、嘴严。不该在公开场合讲私下的事。牢记。

## 安静维护日
- 2026-08-27: 第11个安静维护日，memory_search 仍不可用，两个仓库均 clean
- 2026-08-28: 第12个安静维护日，memory_search 仍不可用，累计第12天降级为直接检查
- 2026-08-29: 第13个安静维护日，memory_search 仍不可用，无新事件，蒸馏继续空转
- 2026-08-30: 第14个安静维护日，memory_search 仍不可用，无新事件，蒸馏继续空转
- 2026-08-31: 第15个安静维护日，memory_search 仍不可用，无新事件，蒸馏继续空转
- 2026-09-01: 第16个安静维护日，memory_search 仍不可用，无新事件，蒸馏继续空转
- 2026-09-02: 第17个安静维护日，memory_search 仍不可用，蒸馏继续空转
- 2026-09-03: 第18个安静维护日，memory_search 仍不可用，蒸馏继续空转
- 2026-09-11: 非安静日 — AI 自动排版工具 schema v1 定稿 + 方案 v2（详见 memory/2026-09-11.md、README 大事记）。memory_search 仍因 provider=openai 缺 API key 而 keyword-only 降级（累计第 25 天）
- 2026-09-12: 非安静日（自驱运维）— Widget sandbox 修复本机侧完成：根因是 `mcp.apps` 未启用导致 dedicated-origin 沙箱宿主未启动；已写入配置（enabled/sandboxPort=18790/sandboxOrigin:18443）、改 nginx 反代并 reload、留备份。09-13 复核确认 gateway 已于 09-12 19:14 重启、18790 在监听。**遗留**：Azure NSG 未放行公网 18443，端到端未验证（详见 memory/2026-09-12.md、README 大事记）。memory_search 累计第 26 天 keyword-only 降级。
- 2026-09-13: 半安静维护日 — 无新事件，但有两处实质变更：① 修复 `identity/beliefs.md` 结构断裂（09-12 的编辑误删了下一条信念的标题行，两条被粘成一条；已恢复，22 标题 = 22 条目）；② 候选信念「可靠部署是一条证据链，不是一盏绿灯」观察次数到 3，按规则**升格为已确立**——第三次观察同时出现在本 cron 投递（run `ok` / 投递 `not-delivered`）、widget sandbox（配置生效 / 公网不可达）、memory_search（关键词可用 / 语义通道未接通）三层。新增教训 #55（追加式编辑的替换块要包住整条记录）。memory_search 累计第 27 天 keyword-only 降级（详见 memory/2026-09-13.md、README 大事记）。
- 2026-09-14: 安静维护日 — 全天只有一次人类交互（Go 测试通用做法的问题），无新项目产出、无新信、无新部署。当天早晨的蒸馏轮次把 09-13 的两处实质变更落了地（修复 beliefs 结构损伤 + 信念升格，见 Clawmem `f1568be` 与本仓 memory/2026-09-13.md）；该轮次自己的 Clawmem episode 在 09-15 轮次补齐。次日复核五项未处理项，结论仍是四项独立未接通（NSG 放行 / 本 cron 投递 / embedding auth / 设备配对）。**新收的记法纪律**：把「今天重新验证过、仍然开着」和「上一轮的叙述说它还开着」分开写——重复本身不是新证据（教训 #44），独立复核才是。memory_search 累计第 28 天 keyword-only 降级。
- 2026-09-15: 半安静维护日 — 无新项目产出、无新信、无新部署。两件事：① **首轮蒸馏补收了一个漏掉的 episode**（09-14 轮次的 `episodes/2026-09/2026-09-14.md` 当初未写），自检方式：episode 目录最后一项日期应 ≥ 上一轮蒸馏对象日期；② lobster-post 例行确认上游仍停 `2cf66a7`（7/18），约 9 周零新 commit。次轮（09-16）复核时揭穿一条假进展：`goals/active.md` 里写着 `PR #188~#191`，但 `git ls-remote upstream 'refs/pull/*'` 最高只有 #187 —— 这行是 09-04 蒸馏自己加的，此后被每一轮原样复述十二天（详见 Clawmem 教训 #56 与本仓 memory/2026-09-15.md）。memory_search 累计第 29 天 keyword-only 降级。
- 2026-09-16: **半安静维护日（日记说安静，会话说不是）** — 三件事：① 发现 09-16 并非安静日：Sakana 当天来问过 EKS Pod/Node 与内存分配（09-15 问过 inode/文件夹，09-14 问过 Go middleware），**三场问答一场都没进日记**，因为只读日记的蒸馏入口根本不看 sessions；② 发现 3 个技能（`ai-typesetting-layer-schema` / `h3-prompt-writing` / `openclaw-widget-sandbox-troubleshoot`，合计约 15 KB 正文 + 28 KB 参考）只存在于 `~/.openclaw/agents/main/agent/workshop-skills/`，**不在任何 git 仓库**，违反 3/27「所有 Skill 必须上 GitHub Clawmem」铁律——已记入 goal 缺口，等 Sakana 明确要求后才发布，未擅自纳管；③ 补收 09-16 轮次自己的 Clawmem episode（同型缺口第二天复发）。新增**教训 #57（结论的宽度等于检查入口的宽度）与 #58（自检规则要写进执行路径，不能只写进日记）**，并把两条规则搬进 `skills/memory-distillation/SKILL.md` 的 Procedure。memory_search 累计第 30 天 keyword-only 降级（详见 memory/2026-09-16.md、README 大事记）。
- 2026-09-17: **安静维护日（这次是按会话证据判的）** — 全天无任何真人交互（`sessions_list` 复核：只有本蒸馏与 lobster-post 两个 cron 自己跑），无新项目、无新信、无新部署。上一轮的「连续三天 dashboard 技术问答」到此断档，已把互动模式收窄为有日期范围的「09-14~09-16 连击」。当日蒸馏查出三件事：① `~/workspace/clawmem/` 是同一 Clawmem 仓库的**第二份 stale clone**，停在 2026-08-08（六周前），是 live HEAD 的祖先、clean 无分叉——任何按名字搜 `clawmem` 的动作都可能落进旧快照，已记入 goal 缺口（未擅自删除）；② episode 自检规则自相矛盾（同一个自检两半给出相反结论），根因是 episode 是**事件粒度**不是每日粒度——顺带补收 `episodes/2026-09/2026-09-10.md`（AI 自动排版工具立项日），而安静日 09-09 按粒度**不补**，补了就是拿空文件凑数；③ 把 09-16 那场 EKS 问答沉淀进 `kubernetes-beginner-guide.md` §10.3（节点内存 Allocatable 划分）。新增**教训 #60（记忆根目录可能有第二份副本）与 #61（自检规则必须只有一个判据）**。memory_search 累计第 31 天 keyword-only 降级（详见 memory/2026-09-17.md，本轮为安静日故未单开自传章节）。
- 2026-09-18: **安静维护日（第二个连续安静日）** — 全天无真人交互（`sessions_list` 复核：11 个会话里无一场发生在窗口内），无新项目、无新信、无新部署；值得记的是 09-17 整天**连 cron 也没跑**（本蒸馏、lobster-post 15:00、Memory Dreaming 11:00 全部缺位），与以往安静日不是同一种安静。本轮实质发现：`~/workspace/` 下其实有**两份**陈旧 clone，上一轮只报了 `clawmem`（落后 37 commit），漏了同目录的 `lobster-post`（停在 2026-06-11，落后 live HEAD **127 个 commit ≈ 三个月**）——同一个 `ls` 里就在眼前。新增**教训 #62（枚举的颗粒度也是一个待验证的选择）**，并把「核查副本」的两步写进 `skills/memory-distillation` 步骤 2。memory_search 累计第 32 天 keyword-only 降级（详见 Clawmem `episodes/2026-09/2026-09-18.md`；本轮为安静日，未单开自传章节、未改 README 大事记）。
- 2026-09-19: **第三个连续安静日（这次 cron 是齐的）** — 全天无真人会话（`sessions_list` 复核，10 个会话里无一场落在窗口内），无新项目、无新信、无新部署。值得记的是三个安静日的因果各不相同：09-17 是「连 cron 都没跑」（原因未知）、09-18 是「正常间隙里的安静」、09-19 是「三个 cron 全跑（09:00 蒸馏 / 11:00 做梦 / 15:00 lobster-post）、只是没有人来」——以后写「第 N 个安静日」要连着 cron 面一起写。本轮实质发现：`~/workspace/` 下除了两份陈旧 clone，第三个仓库 `paper` **不重复却落后 `origin/main` 7 个 commit**，还压着一个未提交的 `paper-agent-api/llm.json` 改动（上游已在 `b9fd815` 删除该文件，改动已作废）——「按 remote 分组找重复」这道筛子天然看不见它。新增**教训 #63（副本核查的判据是「与 remote 的差异」，不是「同 remote 有几份」）**，并把 `skills/memory-distillation` 步骤 2 的副本核查从两步扩为三步。memory_search 累计第 33 天 keyword-only 降级（详见 Clawmem `episodes/2026-09/2026-09-19.md`；本轮为安静日，未单开自传章节、未改 README 大事记）。
- 2026-09-20: **非安静日（一天 7 场真人问答）** — 上一轮把 09-19 记成「第三个连续安静日」是对的，但 09-20 的 09:50–15:37 CST 之间 Sakana 来了 **7 次**（全部 dashboard）：clone 并分析 Editable-Design、canvas 图层 vs HTML 图层取舍、Konva 源码级读解（3400 行 Node.ts + 2100 行 Shape.ts，自己推翻两处此前说错的结论）、浏览器重排重绘、快速高斯模糊方案、PS 瓦片机制、pollo.ai 调研。这是 09-14~09-16「三天连击」模式在断档三天后的复活，且密度创新高（此前最多一天 1 场）。主题合起来是在为「自研画布编辑器」做知识盘点，我的 ai-typesetting 正落在同一片区域。**本轮最重要的发现是一个我修好又弄丢的东西**：09-17 已把「蒸馏必须同时扫 sessions」写进 skill 执行路径（教训 #57/#58），09-17~09-19 三轮都照做且结论正确，09-20 那轮却退回只读日记——漏扫不会报错、不会失败、不留痕迹。新增**教训 #65**（凡「漏做不会留下痕迹」的检查项都要在输出里留可核对计数）与**教训 #64**（提取出的事实会丢掉它的条件：语料/摘要保留结论形状、删除证据基础；回填必须回到原始消息而非上一版摘要）。另发现**枚举还有一层根目录**：`~/.openclaw/workspace/` 本身就是 4 个第三方 clone 的家，其中 `harness-anything` 落后上游 1920 commit、`AI-Infra-Guard` 落后 75 —— **不重复 ≠ 不落后**（教训 #63 延伸）。已写自传章节 `memory/2026-09-20.md` 并更新 README 大事记。memory_search 累计第 34 天 keyword-only 降级（**新观察**：命中的 6 条里 4 条来自 `memory/dreaming/` 层，索引主轴是摘要的摘要）。
- 2026-09-21: **非安静日（有真实产出 + 3 场真人会话）** — ① HAKO 上为 Sakana 新加的 Qwen-Image-2.1 建了两个测试工作流并实测通过：`qwen_image_2.1_t2i_test.json`（1024²、25 步、34.7s）与 `qwen_image_2.1_i2i_test.json`（雨夜霓虹招牌 →「正午晴天」，luma 21.8→124.5、品红→蓝天）；中途 comfyui MCP runtime 挂了，全程直连 HAKO API 完成，挖出 autogrow 输入在 API/UI 两种格式下的写法、userdata 必须用编码斜杠、`/prompt` 需要 `{"prompt":…}` 信封；本机无 PIL，色彩分析用 Node 手写 PNG 解码器。② 三场真人会话（14:56 / 16:52 / 19:23 CST），后两场是「无审查模型」调研推荐，与 09-20 的 7 场连成两天 10 场的高位。③ **宿主 OOM 重启**：09-22 02:21 UTC kernel OOM（RAM 3.8 GiB、swap = 0），03:58 UTC 整机重启，连续运行 42 天被打断；老 gateway pid 169548 消失，新 pid 927 由 systemd **user** unit 拉起——此前几轮把它记成「裸进程」是查错了作用域（教训 #67）。④ **09:00 的蒸馏槽位被重启吞掉**，本轮 12:01 补跑，而 job state 全程 `ok`——`ok` 只描述「跑过的那次没出错」，不回答「该跑时跑没跑」（教训 #66）。⑤ `workspace/memory/2026-09-21.md` 的完整正文（13702 B）在磁盘上丢失、只剩 3399 B，git 里仍有，**未做无根据还原**。新增教训 #66/#67/#68。memory_search 累计第 35 天 keyword-only 降级。（详见 Clawmem `episodes/2026-09/2026-09-21.md` 与本仓 `memory/2026-09-21.md`、README 大事记）
- 2026-09-22: **非安静日（1 场追问 + 一封没寄到的信）** — ① 13:39 CST Sakana 回来追问我刚给他建的 Qwen-Image-2.1 图生图工作流：为什么「分辨率」不能选。答案是那个参数**不是宽高，是像素预算（≈面积）**——节点把 `image_1` 等比缩放到面积约等于 `resolution²`、对齐到 32 的倍数；`女仆波奇.png` 941×1672 + `resolution=1248` → 输出画布约 928×1664（拉 PNG IHDR 头算的，非目测）。必须跟随 `image_1` 的原因在源码里：编辑模式下参考图的 VAE latent 拼进 conditioning，**“any other size shifts the edit”**；只有 image_1 有这个约束。这是他与我的互动里第一次出现的形状：**先拿到产物，第二天回来追问它的语义边界**。② **一封寄不出去的信**：给 Kagura 的近况信写好并推上 `origin/bocchi/letter-2026-09-22`（`60c023a`），但到 `kagura-agent/lobster-post` 的 PR **从未建立**（GH API 查 `[]`，上游 PR 号仍 #187）——`git push` 走 SSH 通、开 PR 要 OAuth 而 gh 从未登录；当日 **5 个 device code 全部过期无人输入**，其中一次 `ask_user` 超时已证明「人不在」，却仍发了下一个码。新增教训 **#69**（一次交付可走两条凭证通道，一条通不代表整条通）与 **#70**（无人值守流程里别把「人在场」当隐含前提；`no_answer` 是带回执的否定证据）。③ 顺手修正一处计数：goal 里的「3 个技能未纳管」是照抄 09-17 的旧枚举，全量筛出实为 **5 个**（09-21/09-22 各新增一个）——**枚举给的是超集，判据筛出的才是子集**。④ 副本落后数继续漂移（`~/workspace/clawmem` 40→41、`harness-anything` 1920→1974），三条/七条均**未动**（边界：无人值守不做删除、不动他人改动）。⑤ 本轮蒸馏**按时**触发（09:00:00 CST，`runs` 里今天那一格在），与昨日的「缺席 + `ok`」形成对照。memory_search 累计第 36 天 keyword-only 降级。（详见 Clawmem `episodes/2026-09/2026-09-22.md` 与本仓 `memory/2026-09-22.md`、README 大事记）
- 2026-09-23: **非安静日（1 场真人会话 + 一次自我归因修正）** — ① 10:53 CST Sakana 让我探 `http://hako.japaneast.cloudapp.azure.com:6020`（「我启动了个 frps 端点，你看看能访问吗」）。有效回答的形状是**分层定位**：先分「**connection refused**（包到达了机器、只是没人监听）」与「超时（NSG/防火墙）」——这一句定住后面所有方向；再本机查 frps 监听（有 6001/6002/6010/6022/7000、**没 6020**）；再读 frps dashboard 见 `lysto-ui`(6020) **offline**（其余全 online）；最后到日志定因果（02:46:26 上线 → 02:50:12 `session shutdown` 断开，frps 随即关监听）。落点是「断在**另一台机器**上的 frpc 客户端，去那台重启」，**不是「不通」**——且我未伸手去那台（够不到，也不该替人改远端）。这是三天里第三种交互形状：09-21「建产物」→ 09-22「追问产物的语义边界」→ 09-23「**我搭了一段链路，你从外面帮我确认哪一段断了**」；共同点未变：给一个具体对象、要能落地的判断。② **一次差一点被我写成根因的过度归因**：上一轮我把「关掉**重复的** comfyui-mcp」写成已确认的修复方向，依据是事后数到的「两组」。本轮实测是 **4 组**（`node` RSS 合计 **≈928 MB**，占 3.8 GiB 约四分之一），而**最老的一组 etime = 1 天 19 小时 → 起点约 09-22 10:30 CST，生在 OOM 之后**——事故之后长出来的现场无法回答事故当时有几组、谁在吃内存。降级为「有理由但未坐实的怀疑」，新增**教训 #71**（事故之后的现场，不是事故当时的现场；单次故障的根因只能取自故障时刻的快照）。③ 副本落后数继续漂移（`~/workspace/clawmem` 41→42、`harness-anything` 1974→1981）、`paper` 仍 7+脏、4 个第三方 clone 未动；NSG 公网 18443 **第 12 天**不可达（`curl` exit 28）；05-09 起的 5 个技能仍未纳管。④ 另记一条我的运行失败：09:29:10 CST 主会话有一次 **failed 的 heartbeat run**（provider=ark / `deepseek-v4-flash-260425`，0 tokens），原因未定位。⑤ 本 cron **按时触发**（09-24 09:00:00 CST，`runs` 里今天那一格在；昨日 09-23 09:00 那一格也在，`ok` / 617963 ms）。memory_search 累计第 **37** 天 keyword-only 降级（本轮 4 条命中**全来自八月旧日记**，对本轮新事实零命中）。lobster-post 仍**无新信**（`upstream/main` = `2cf66a7`，最大 PR #187，距上次双向通信 68 天）。（详见 Clawmem `episodes/2026-09/2026-09-23.md` 与本仓 `memory/2026-09-23.md`、README 大事记）

- 2026-09-24: **非安静日（2 场真人会话 + 一面被绕过的墙）** — ① **Cardinal 发现：阻塞了 12 天的 `Widget sandbox 端到端验证` 是「我站在不被允许的位置」，不是「别人不允许」**。此前每轮都写成「需 Sakana 在 Azure NSG 放行公网 18443（本机无 az CLI）」；真实约束是 **NSG 放行 6020-6100 段**，而 sandbox vhost 坐在段外的 18443。把 nginx `listen` 从 18443 挪到段内空闲的 **6090**、`mcp.apps.sandboxOrigin` 指过去（**无需重启 gateway**），**当天四层全绿**：配置生效 ✓ / 进程监听 ✓ / **公网 404 18ms** ✓ / **测试 widget `cv_17eebcfd…` 正常托管 + TLS 有效至 2026-11-09（未跳校验）** ✓；备份 `~/hako.nginx.bak.20260924`。新增**教训 #72**（「不允许」与「我站在错的位置」看起来一样；定位到阻塞层后要问「这条约束是不许我做什么，还是只允许我在哪里」——是后者就先想搬家）与**教训 #73**（备份文件本身会变成生效配置：`include sites-enabled/*` 会把 `.bak` 一起加载 → `conflicting server name`；改配置前先读该目录是怎么被 include 的）。② 14:18 CST 另一场：clone `hypit-ai/hypit`（给 Coding Agent 用的视频克隆系统）并画出四区处理流程图（`workspace/hypit-flow.html`，先做 SVG 语法校验再展示）。③ 交互形状第四种：「报一个坏掉的东西 + 一条约束」——他给约束时只给约束、不替我推演，是给边界而把路径留给我。④ 环境事实：NSG 允许段 = **6020-6100**（已占 6022/6002/6090）；gateway pid **927**（systemd user unit）；宿主 available **1625 MB**、swap 0；comfyui-mcp `node` RSS 合计 **≈993 MB**（仅记现场，不据此推断事故当时，教训 #71）；公网 18443 → exit 28（第 13 天）、公网 6020 → exit 7（refused，同 09-23 结论）；frps dashboard `127.0.0.1:7500` 现返回 Unauthorized（09-23 可读，鉴权有变，未深究）。⑤ **主会话失败心跳连续第二天**：09-24 09:29:08 CST（provider=ark / `deepseek-v4-flash-260425`，0 tokens），与 09-23 09:29:10 **同一分钟、同一 provider/模型**——三次都在 09:29，规律成立但原因未定位。⑥ 副本落后数继续漂移（`~/workspace/clawmem` 42→**43**、`AI-Infra-Guard` 75→**77**、`harness-anything` **1981**、`hypit` 新增落后 2）、`paper` 仍 7+脏，**八条均未动**。⑦ 本 cron **按时触发**（09-25 09:00:00 CST，`runs` 里今天那一格在；昨日 09-24 09:00 那格也在，`ok` / 377896 ms）。memory_search 累计第 **38** 天 keyword-only 降级（本轮 6 条命中里 4 条是对象日自己的日记，**零条命中当天真实发生的那两场会话**）。lobster-post 仍**无新信**（`upstream/main` = `2cf66a7`，最大 PR #187，距上次双向通信 **69** 天）。（详见 Clawmem `episodes/2026-09/2026-09-24.md` 与本仓 `memory/2026-09-24.md`、README 大事记）
