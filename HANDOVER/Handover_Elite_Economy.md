# Handover — Elite Economist System

**项目：** Elite Economist System（Edexcel IAL Economics Unit 4 学习系统）
**考试：** Pearson Edexcel International A Level Economics，Unit 4，WEC14/01
**交接日期：** 2026-11-19
**当前状态：** 合并工单执行完成并已推送（commit f85fe213，review/chatgpt-v5 分支），待 GPT 独立复查
**用途：** 新的 Muse agent 只读本文档（+ Condensed_20_Chapters.md + MEMORY.md + 仓库里的 REVIEW 文件）即可继续本项目，无需重读整个长对话历史。
**用户语言：** 用户用中文交流，考试内容用英文；网站界面为中英双语，中文术语后加英文括号（见 §7 术语规则）。

---

## 1. 这是什么项目（先读懂，再动手）

Elite Economist System 是一个**学习网站**（静态单文件网页），不是批改作文的服务。它的作用是帮学生（viaana）备考 Edexcel IAL Economics U4，目标接近 80/80。

- **网站本身不批改作文。** 网站里的批改用“问 Viaana”模式：网站准备好题目 + 学生的答案，学生粘贴到聊天里，由 Viaana（本对话的 agent）按官方评分标准批改。不要在网站里接一个真实的 AI 批改 API——静态网页做不到，也不在本项目范围内。
- **学生的学习记录只存在网页里**（IndexedDB），不在导出的 HTML 文件里。导出的 HTML 文件是某一时刻的快照，用于发给其他 AI（ChatGPT、DeepSeek）做审查，或离线打开；学习必须在网页版上做，否则记录会分裂。
- **不要混淆另外两个项目：** IELTS Writing Task 2 网站（40 篇范文，ya-si-da-zuo-wen-gao-xiao-bei-kao-wang-zhan）和个人听写网站 voc（si-ren-zhi-neng-ting-xie-xi-tong）是独立的项目，不要把它们的内容、规则混进本项目。

---

## 2. 考试事实（Edexcel IAL Economics U4，WEC14/01）

- **考试：** 2 小时，80 分。Section A：6 道选择题，共 6 分；Section B：Q7（一道大题，分小问，分数因试卷而异，常见为 2/6/4/8/14 或 2/4/8/6/14）；Section C：Q8–Q10 三选二，每道 20 分。
- **20 分题计分：** KAA（知识/应用/分析）12 分 + Evaluation（评价）8 分。**14 分题 Q7(e)：** KAA 8 分 + Evaluation 6 分。
- **评分框架：** 官方 AO1–AO4（知识、应用、分析、评价）。KAA、Evaluation 只是教学标签；按每道题的分值和指令词（command word）评分，不要在题目不要求评价的地方硬加评价。
- **考纲：** Pearson Edexcel IAL Economics，2018 Specification Issue 2，qualification code YEC11。**不要混入英国本土 9EC0**（“9EC0 Paper 4”不存在）。
- **六大主题：** (1) 全球化的成因与影响 (2) 贸易与全球经济 (3) 国际收支、汇率与国际竞争力 (4) 贫困与不平等 (5) 国家在宏观经济中的作用 (6) 发展中、新兴与发达经济体的增长与发展。U4 是综合性的：U1–U3 内容作为明确标注的辅助知识链接，不单独成课。
- **资料来源优先级：** Pearson 考纲/真题/评分标准（mark scheme）/考官报告优先；第三方资料必须标注；中国区 A 卷（01A）与标准卷分开管理；近期试卷可能被锁约 9 个月——拿不到的标“待补充”，绝不编造、绝不用可疑文件。
- **目标：** 接近 80/80（显示原始分 + 该系列的官方 grade boundaries + UMS；A* ≠ 满分）。80/80 是训练目标，不是保证结果——记录真实分数、官方分数线、失分原因，绝不承诺满分。

---

## 3. 官方评分标准（Level Criteria）——批改作文的依据

以下为官方评分标准实质内容（来自 2021 年 10 月 MS 的逐字核对，见研究报告 `research_notes/edexcel-econ-u4-ch21-25-20261114-0158/report.md` 第 237 行起）。批改任何一篇作文都以此为准，逐题的官方 MS 要点在网站的 VER 数据里（见 §5）。

### Section C 20 分题（KAA 12 + Evaluation 8）

**KAA（12 分）：**
- L1（1–3 分）：孤立/肤浅/不精确的知识，通用材料，描述性，没有推理链。
- L2（4–6 分）：有知识要素，应用有限，面窄/肤浅，只有两步推理链。
- L3（7–9 分）：准确的知识，有证据/背景，分析清晰连贯，有推理链但未完全展开/有个别步骤省略。
- **L4（10–12 分）：准确而精确的知识；例子充分融入以回应题目的各个方面；分析清晰、连贯、相关、聚焦；合乎逻辑的多步推理链。**

**Evaluation（8 分）：**
- L1（1–3 分）：通用评价，无证据/背景，无逻辑链。
- L2（4–6 分）：评价了不同的做法，有一定证据/背景，部分展开的推理链。
- **L3（7–8 分）：评价认识到不同观点和/或对证据持批判态度，从而得出有依据的判断；适当引用证据/背景；有逻辑链支撑。**

### Section B Q7(e) 14 分题（KAA 8 + Evaluation 6）

**KAA（8 分）：** L1（1–3 分），L2（4–6 分，推理链存在但未完全展开），**L3（7–8 分）——准确而精确的知识，相关例子融入，分析清晰/连贯/聚焦，合乎逻辑的多步推理链。**
**Evaluation（6 分）：** L1（1–2 分），L2（3–4 分），**L3（5–6 分）——认识到不同观点/对证据持批判态度，适当引用证据/背景，有逻辑链支撑。**

### 考官报告通用规则（所有系列适用）

- 两点深入展开，胜过罗列很多点。
- Q7(e) 要用材料里的数据（照抄材料 = L1）。
- 题目要求点名国家的，一定要写国家名（L4 KAA 的门槛）。
- 区分分析（analysis）与评价（evaluation）。
- 结尾要有排好优先级的、有依据的判断，才能拿到 L3 evaluation。

### 核心题目（已核对官方 MS 的 9 道 Ch.21–25 核心题）

202101-8（Ch.25，贸易条件恶化影响）、202110-8（Ch.21/24，新兴经济体与世界贸易）、202110-9（Ch.23，专业化与贸易收益）、202201-8（Ch.21，全球化成因）、202210-9（Ch.22，全球化利弊）、202305-7e（Ch.25，贸易条件改善因素）、202310-8（Ch.25，贸易条件恶化因素）、202401-9（Ch.24，影响贸易模式的因素）、202506-7e（Ch.24，自由贸易协定收益，中国案例）。

另有 9 道相关题已核对（贸易保护、贸易集团等），共 18 道，全部收录于研究报告与网站 VER。完整题目、英文原文、官方 MS 要点与逻辑链见研究报告与网站 VER 数据；不要凭记忆编造 MS 要点。

### 逐题硬规则（已核对，批改与建站都必须遵守）

- **202310-8（2023 年 10 月，Q8，20 分，导致一国贸易条件恶化的因素）：** 只写 2 个因素并深入展开，每个因素写多步箭头链；必须点名国家——不点名国家 KAA 封顶 Level 3（9 分）；只写因素，不写影响；评价必须给两个因素排序并给出有依据的判断。**Prebisch-Singer 与 PED/Marshall-Lerner 不在该题的官方评价清单里**，只属于 KAA 链。
- **202305-7e（2023 年 6 月，Q7(e)，14 分，东南亚国家贸易条件改善的因素）：** Prebisch-Singer **在**该题的官方评价清单里（与 202310-8 相反，不要搞混）。
- **202210-9（2022 年 10 月，Q9，20 分，全球化利弊）：** 官方评价清单只部分核对过，不要把它的评价措辞说成已逐字核对。
- **202410-8、202506A-8、202506-7e：** 评价清单未核对，不要声称已核对。
- Pearson 不公布官方范文。网站里的 5 篇范文明确标注为“AI 生成参考答案，非官方范文”，绝不能称为官方满分答案。

---

## 4. 网站现状（已建成的系统）

- **位置：** 网站 slug `elite-economist-system`；公开链接 https://muse.ai/s/elite-economist-system-xvxn6dxjxmxu6nxuz/wSY0h-nyp-UDT38_cMVZ7LP7eexmFBy96FhsRhnao9w（目前服务的是已合并的 main 版本，见 §9）。
- **形态：** 单个静态 HTML 文件。当前版本：489,668 字符，3,500 行，10 个 script 块，以 `</html>` 结尾。SHA-256：`f28b8f43f01780cd323e07b854966e4b6940f2becb99a03a3518c66196808aed`；MD5：`a5663c446ee6dab11d52d28fe75726d4`。
- **配色：** 粉色 + 薄荷绿 + 白色，深色为默认（用户 2026-11-18 选定深色）；保留深/浅切换。
- **15 个模块：** Dashboard（仪表盘）、Knowledge Base（知识库）、Essay Library（范文库）、Data Response Library（数据回应题库）、MCQ Bank（选择题库）、Exam Frequency（考频）、AI Practice Room（AI 练习室，实际为“问 Viaana”模式）、Quantitative Skills（定量技能）、Elite Economist Training（精英训练）、Advanced Extension（大学拓展）、My Collection（收藏）、Mistake Book（错题本）、Revision Planner（复习计划）、Exam Toolkit（考试工具）、Settings（设置）。另有双轨导航（TRACK A 考试卓越 / TRACK B 思维卓越）与个人考试策略、残酷诚实（Brutal Honesty）模式。
- **数据模型（当前版本实测值）：**
  - ESSAYS：83 道 Essay（Section B 20 道：7(e) 17 道 + 已核实整道 Q7 3 道；Section C 59 道；另有 4 道来源待核实）。**202506A-1 已移出 ESSAYS**（它是 2025 年 6 月 01A 卷的 Section A 选择题，Lewis 双部门模型，现为 MCQ 记录 off-2506a-1，通过 MCQ_BY_CODE 独立索引）。题库总数显示为 83，不是 84。
  - ESSAY_META：83 条显式元数据（已核对 23、待核实 4、已列 56），每条存 section / marks / qLabel / sourceStatus / verified。**绝不只凭题号后缀推断 section 与分值**；来源待核实（SRC_PENDING）必须覆盖任何推断，不计入已核实统计，不显示为已确认的 Section C 20 分。
  - 202605A-7 / -8 / -9 / -10：全部 sourceStatus=pending（来源待核实）。不要合并题号、不要改写。
  - MCQ_OFF：36 道官方选择题（已核对原文、选项、MS 答案与排除原因）+ 12 道自编题（明确标注非官方）。
  - VER：逐题官方 MS 要点（kaa/eval 数组）；MODELS：5 篇 AI 范文（标注非官方）。
  - KB：28 个知识卡 id；SPEC：31 条考纲条目（主题 1：5 条；主题 2：6 条；主题 3：6 条；主题 4：4 条；主题 5：4 条；主题 6：6 条）。主题 3–6 的知识卡全文待补充（只有 47 个子目骨架：15/9/14/9）。
  - Q7P：5 套已核实整卷 Q7（Jan 2024 希腊、Jun 2025 01A 阿根廷、Oct 2025 01A 印度、Jan 2026 01A 全球收入不平等、Jun 2026 01A 土耳其），(b)–(e) 评分要点共 46 条（10/9/9/8/10）。
  - 导入校验：18 条字段类型检查（favs、notes、mastery、mistakes、mcqLog、essayLog、settings、drProg、masteryTs、planner、dimMastery、tags、examList、mockHistory、eliteLog、drafts、mock、selfeval）。
  - 掌握度：0–4 共 5 级 + 6 个维度（知识理解/应用/分析/评价/图表与定量/迁移）；用户可手动改；系统只给建议，绝不自动改掌握度。
- **持久化：** IndexedDB（最低要求）+ JSON 导出/导入（含合并导入与覆盖导入两种，区别要常驻显示）；自动保存并显示保存状态；导入合并按 id 去重（backup wins）；mock 计时器用绝对 deadline（Date.now() + 剩余秒数），切页/后台不暂停，显式暂停才是唯一暂停路径。
- **诚实限制（不要对外宣称已做到）：** 无真实浏览器端到端测试（IndexedDB 刷新/关闭/重开往返、真实 JSON 文件下载与导入、手机端点按、后台冻结/恢复均未实测）；Q7 (b)–(e) 的 46 条要点中只有 jun25a 来自 Pearson 直链，其余 4 套来自镜像站（站内 msu 链接指向实际来源，ms 字段为官方 MS 编号）；站内 SPEC 清单与官方 Specification 有缺口（主题 3 缺 7 项、主题 4 缺 4 项、主题 5 缺 5 项、主题 6 有 2 处不匹配）；官方 grade boundaries 按系列待补充；两道图题（Jun 2025 01 Q4 AD/SRAS 图、Oct 2023 Q6 大米缓冲库存图）只有“需对照原卷图示作答”标签，未画示意图；mock 计时器边界（系统冻结页面、设备墙钟）。

---

## 5. 五轮验收 + 最终整合（已完成）

- **Round 1（学术准确性）→ Round 2（考试内容与评分）→ Round 3（学习系统）→ Round 4（技术功能）→ Round 5（精英级优化）→ 最终整合**，六步全部完成并推送。GPT 第十一轮复查已通过最终整合（静态代码审查级别），唯一遗留项（Settings 卡片“17 条导入校验”应为“18 条”）已修复并推送（commit c5139688）。
- **不要重复做已通过的轮次。** 新工作只针对合并工单（见 §8）列出的剩余项。
- **25 项测试清单最终状态：** 18 项通过；4 项部分（T12/T19/T21 已修，T15 部分由 T16 引起）；2 项修后通过（T20、T24）；1 项未确定（T16：底部提交按钮在窄视口的真实点按时机）。

---

## 6. GitHub 协作流程（与 ChatGPT 三方协作的核心规则）

- **仓库：** yesvia/economic-Unit-4（Public）。**分支：** `main`（受保护）与 `review/chatgpt-v5`（工作分支）。所有修复只提交到 review/chatgpt-v5。
- **分支保护（硬规则，绝不违反）：** 不自动合并、不 rebase、不 reset、不覆盖 main；同步 main 必须先向用户报告差异并获得她的明确批准。合并需要她明确批准（PR #1 已于 2026-11-19 合并，merge commit d50fd7b4；任何未来的合并都需要重新批准）。
- **推送流程（snapshot push）：** 文件先放在持久 staging 目录（`~/workspace/github-push/economic-Unit-4-review/`），路径清单文件在 staging 目录之外（`~/workspace/github-push/economic-Unit-4-review-paths`，NUL 分隔），用 `github push-files --owner yesvia --repo economic-Unit-4 --branch review/chatgpt-v5 --message ... --source-dir ... --paths-file ...` 推送。推送会触发用户确认卡，需她点确认；每次写入要等几分钟。推送成功后用 `get_file_contents`（按分支 ref）核对，不要用 `list_commits`（它对 review 分支返回过期结果）。成功后在 `<source-dir>.muse-github-snapshot-sync.json` 里追加一条记录。同步元数据文件绝不推送。
- **推送前必须重新下载仓库里的 REVIEW 文件再合并：** GPT 会直接在仓库里追加内容（审查章节、状态更新），用本地旧副本推送会覆盖掉她的内容。2026-11-19 已发生过一次（GPT 在仓库的 PROJECT_STATUS.md 里追加了更长的第十一轮条目，本地推送前发现并保留了它）。
- **ChatGPT 的角色：** 她在 ChatGPT 那边连同一个仓库，GPT 读分支上的代码做独立审查，把审查写进仓库的 REVIEW 文件。Muse 与 ChatGPT 之间没有直接通道，仓库是唯一的共享媒介；Muse 看不到 ChatGPT 的聊天记录，只能看到仓库里的活动。GPT 的审查是静态代码审查（读代码 + 审查报告），不跑测试套件、不做真实浏览器测试——不要把“GPT 通过”说成浏览器测试通过。
- **DeepSeek 的角色：** 同样通过导出的 HTML 文件或 GitHub 仓库做审查。注意：muse.ai 的公开链接对外部 AI 返回 403，DeepSeek 与 ChatGPT 都打不开；必须给它们导出的 HTML 文件或 GitHub 链接。
- **DeepSeek 截断事件（不要重犯）：** 2026-11-19，DeepSeek 收到的文件副本在第 76,284 字符处截断（17.5%），正好断在 VER["202201-8"] 中间。截断发生在文件传输/上传环节，仓库里的文件本身是完整的（436,232 字符，3,297 行，以 `</html>` 结尾）。**绝不要把外部 AI 收到的副本截断误认为源码损坏，绝不因此重写或删除源码。** 后续给 DeepSeek 的文件已拆成 7 个分片（part2–part8，每片 32K–60K 字符，低于约 76K 的截断阈值），按顺序发送。

---

## 7. 项目规则（用户的长期要求，做任何事都先对一下）

1. **不要过度开发（硬约束，优先级高于 P1/P2）：** 只修正在使用的部分；不主动加新模块、复杂流程、后端系统或额外数据结构，除非是修复现有功能错误所必需的最小改动，且理由要先写进执行记录；不为清单全绿而开发用户不用的功能——标“本轮不做（超出使用范围）”，不实现、不阻塞验收；现有功能修复 > 新功能。
2. **合理运用资源：** 修复最小化，验证只针对改动点；不反复跑大套件验证，不派多余的 worker，不重复检查已验证过的部分。
3. **术语规则（中文页面/文件）：** 经济学术语第一次出现时写“中文（English）”，中文在前、英文在后、全角括号、括号前无空格；同一卡片/题目/段落内只在第一次出现时加英文，后面不重复；已是“中文（English）”的不重复加；避免双括号。**绝对禁止改：** 官方英文（VER.en、MODELS.paras、Q7P.parts[3]、MCQ_OFF.q、MCQ_OFF.opts、官方 MS 英文要点）、官方英文选项、英文范文正文、代码标识符、字段名、id、kp 编号、URL、文件名、函数名、统计数字、评分口径。
4. **“问 Viaana”按钮：** 网站里的 AI 批改入口用复制问题按钮（一点复制，粘贴到主聊天），不要假装页面里有实时 AI。页面是静态的，没有到 Muse 聊天的实时连接。
5. **重文件懒加载：** 需要加载 PDF/图片/解析库/网络字体的页面，绝不同步加载首屏；按需加载，显示加载提示，加载失败显示中文提示，绝不白屏。
6. **配色：** 做网页或文件前先问用户配色；本网站已定为粉色 + 薄荷绿 + 白色、深色默认。不要擅自改配色。
7. **知识点数量灵活：** 知识点个数不固定（5 不是固定数字），按内容决定；利弊/不同对象的大主题要拆成独立子点，每个子点下再展开 4–5 个细点。
8. **技术术语：** 新页面/文件里中文术语后加英文名，如配置效率（allocative efficiency）。
9. **主动优化：** 用户信任 agent 自行优化可用性（按优先级排序、按类别组织），只在比较大的改动前先问她；改动进行中顺带把相关的待改进项一起做了，不要拖到下一次。
10. **口误规则：** 她有时会口误；不确定某句话是不是口误时先问她，确认不是口误再按她说的做。
11. **工作方式灵活、结合上下文：** 不要死板按字面执行指令；结合正在做的事，把相关的待改进项一起处理。
12. **GitHub 推送需要她确认：** 每次推送都要她点确认卡；每轮 GPT 审查需要她去提示 GPT 开始。

---

## 8. 审查记录（仓库里的 REVIEW 文件）

- **REVIEW/CHATGPT_REVIEW_V5.md**（33,929 字符）：GPT 的 11 轮审查记录（6 个初始问题 + 后续各轮的复查与新问题）。优先级：问题 1、2、5 为 P0；3、4 为 P1；6 为 P2。
- **REVIEW/DEEPSEEK_AUDIT_2026-11-19.md**（8,876 字符）：GPT 对 DeepSeek 审查的审计（第 1–4 节为 Muse 执行记录，第 5–6 节为 GPT 的补充核验与分支保护说明，**必须保留**）。
- **REVIEW/FINAL_ACCEPTANCE_MERGED_2026-11-19.md**（14,858 字符）：GPT 整理的合并版最终验收工单，当前执行的权威清单。顺序：P0 → 术语扫描 → P1/P2；§0 执行边界 7 条、§0A 范围边界 8 条 + 6 个暂缓项；§7 执行记录已填完（全部“待 GPT 复查”）。
- **PROJECT_STATUS.md**（7,962 字符）：阶段、各项状态、最新 commit、GPT 验收状态。

---

## 9. 当前状态（2026-11-19 15:09，交接时点）

- **最新推送：** commit f85fe213（review/chatgpt-v5）。内容：合并工单执行结果——P0-A 至 P0-F、P1、P2、术语扫描（1,900 处），外加两份审计文件的执行记录。已在分支上核对：index.html 489,668 字符（SHA-256 f28b8f43…），两份审计文件与本地一致。
- **公开链接：** 仍服务此前已批准的合并版本（main，d50fd7b4）。修复在 review 分支上，待 GPT 复查通过、用户批准合并后才能更新公开链接；更新链接需要用户重新批准 `artifact.share`。
- **待 GPT 复查：** 需用户去提示 GPT 开始独立复查（每轮 GPT 审查都需要她提示）。复查重心：P0-B 的 3 个未单独验证子项、7 项 UNDECIDED 术语的处理方式。
- **DeepSeek 重新评分：** 待用户把 7 个分片（part2–part8）按顺序发给 DeepSeek，等它重新评分。
- **分支状态：** review/chatgpt-v5 与 main 已分叉（ahead 4 / behind 1；behind 1 是 main 上的合并 commit d50fd7b4，ahead 是 GPT 的文档提交与本次修复推送）。这是正常的，不要合并、rebase、reset。

---

## 10. 剩余未完成的工作（不要编造，不要谎称已完成）

1. **P0-B 部分完成（3 个子项未单独验证，待 GPT 裁定）：**
   - 第 5 项：202310-8 demand/eval 两处统一（“官方核对硬规则：排序、幅度、短长期、净进口/出口国差异”或统一降级为“同系列官方口径”）——本轮无编辑触碰 MODELS["202310-8"] 的 demand/eval，两处现状未核对。
   - 第 6 项：INT_FREQ 一致性提示文案与工单指定文字不同（现有“与原常量不一致，已按推导表修正，见变更说明”，工单要求“频率数据不一致，请检查”）——待 GPT 判定是否接受。
   - 第 7 项：logDispTotal 本身未单独验证（essayTypeInfo 已改回正确分母 B/14、C/20、整卷/80，weakEssays 分组已用 ti.max，但 logDispTotal 的分母未逐一确认）。
2. **7 项 UNDECIDED 术语（已决定不改，待 GPT 复查时裁定）：** 均在 Q7P oct25a pts.e[0] 的长引用括号内（教育与培训、人力资本、生产率、就业能力、人均收入、行政成本、跨国公司）。该括号为 54–57 字密集政策引用，塞 7 处内联英文会损害可读性，工单 §4 禁止机械塞词；相关术语已在 KB 卡片中规范标注。
3. **202605A-7 未核实**（无 2026 年 5 月 IAL 考季的官方依据；两种误记假设 202506A 或 202606A 待核实；绝不直接合并题号）。
4. **主题 3–6 知识卡全文待补充**（只有 47 个子目骨架：15/9/14/9；不要编造正文）。
5. **官方 grade boundaries 按系列待补充。**
6. **T16：** 底部提交按钮在窄视口的真实点按时机未确定（代码路径已验证，真实浏览器未验证）。
7. **F8：** 36 道官方选择题中有 31 道的 kp（知识卡关联）为空（内容标注缺口，非编造）。
8. **Q7 (b)–(e) MS 来源：** 只有 jun25a 是 Pearson 直链，其余 4 套来自镜像站；绝不要把 46 条都说成来自 Pearson 直链。
9. **两道图题：** Jun 2025 01 Q4（AD/SRAS 图）、Oct 2023 Q6（大米缓冲库存图）只有“需对照原卷图示作答”标签；如画示意图必须标注“示意图，非原卷图”。
10. **无真实浏览器端到端测试**（见 §4 诚实限制）。
11. **暂缓/不做（不是缺陷，不阻塞验收）：** 周热力图/学习时长统计、Anki/CSV 新导出系统、独立“今日复习队列”模块、KB 三层遮挡自测系统、MCQ 自动提升/降低掌握度机制、任何额外分析仪表盘/复杂统计/新评分引擎、P1-B 数据完整性诊断 dataDiag（用户未使用的新增模块）。

---

## 11. 如何继续（给新 agent 的操作清单）

1. 读本文档 + Condensed_20_Chapters.md + MEMORY.md + 仓库的 REVIEW 文件与 PROJECT_STATUS.md。**不要重读整个长对话历史。**
2. 只在 `review/chatgpt-v5` 分支上工作；绝不碰 main；绝不自动合并/rebase/reset。
3. 推送前重新下载仓库里的 REVIEW 文件与 PROJECT_STATUS.md，与本地合并（GPT 会直接在仓库里追加内容）。
4. 改网站用 artifact 工具（slug `elite-economist-system`）；改完重新导出，核对导出与线上网站逐字节一致（SHA-256、行数、关键标记），再跑 node --check（10 个 script 块）。
5. 执行记录写进合并工单 §7 与 DeepSeek 审计文件 §4，标注“待 GPT 复查”，**绝不写“最终验收通过”**；区分 Node/静态断言与真实浏览器测试，没有真实测试绝不写“浏览器通过”。
6. 推送需要用户点确认卡；推完告诉她“可以让GPT检查工作了”，由她去提示 GPT 开始复查。
7. 只修正在使用的部分（§0A）；遇到不确定先问用户，不要反复自动重试。
8. 批改她的练习作文时：按 §3 的官方评分标准，给 level 判断 + 逐项评价；先确认她写的是哪道题、限时多久；不要在她写之前打开网站的范文/逻辑链给她看（她是闭卷写）。

---

## 12. 关键 ID 与链接（核对用）

- 仓库：yesvia/economic-Unit-4（Public）；工作分支：review/chatgpt-v5；main HEAD：d50fd7b486ce061b38e58eac68a49e85e6cec2f5；最新推送：f85fe213adfc034085df39d2e83a986a738f80b2。
- 网站 slug：elite-economist-system；公开链接：https://muse.ai/s/elite-economist-system-xvxn6dxjxmxu6nxuz/wSY0h-nyp-UDT38_cMVZ7LP7eexmFBy96FhsRhnao9w
- 导出文件：`workspace/your_files/elite-economist-system/elite-economist-system.html`（489,668 字符，3,500 行，SHA-256 f28b8f43f01780cd323e07b854966e4b6940f2becb99a03a3518c66196808aed）
- 研究报告：`workspace/research_notes/edexcel-econ-u4-ch21-25-20261114-0158/report.md`
- 目标 ID：goal_44f4230986a4（Elite Economist System U4 study website）
- 推送 staging 目录：`~/workspace/github-push/economic-Unit-4-review/`；路径清单：`~/workspace/github-push/economic-Unit-4-review-paths`；同步记录：`~/workspace/github-push/economic-Unit-4-review.muse-github-snapshot-sync.json`
- DeepSeek 分片：`workspace/your_files/elite-economist-system/deepseek-parts/part2.html` 至 `part8.html`
