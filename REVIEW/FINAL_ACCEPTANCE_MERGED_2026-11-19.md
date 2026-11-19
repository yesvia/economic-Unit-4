# Elite Economist System — 最终验收合并工单
日期：2026-11-19
状态：待 Muse 执行；GPT 独立复查前不得宣称最终验收通过
目标分支：review/chatgpt-v5
基线：index.html，SHA aa6b5992c8ac0ddba4d4f3d5f7d747691107d245，3,297 行 / 436,232 字符

本工单合并用户最新《最终验收修改工单》与此前 GPT/DeepSeek 审计，是当前执行清单。先 P0，再 P1，再 P2。不得重写已核对内容。

## 0. 执行边界

1. 交付单一完整 HTML；删除“第 X 部分”分片注释。当前基线已完整闭合（末尾含 </script></body></html>），未检出“第 X 部分”注释，最终仍须复验。
2. p3/p5 聊天副本曾截断；只以仓库最新完整文件为准，逐个核实符号、数组、字符串、脚本闭合。不得因聊天副本截断而重写网站。
3. 保留现有视觉、KB、VER、MODELS、Q7P、MCQ_OFF、报告卡；只做最小修复，不删用户数据。
4. 官方英文原文、英文选项、英文范文正文、官方 MS 英文要点逐字不改；代码标识符、字段名、id、URL、文件名、函数名不改。
5. 静态检查、Node 断言、Muse 自报测试、真实浏览器/手机实测必须分开记录。未执行的不得说通过。
6. 只提交 review/chatgpt-v5。当前与 main diverged（ahead 4 / behind 1）；禁止自动 merge/rebase/reset/覆盖 main。
7. 完成后逐项填写状态、commit、文件、可复现命令和输出；先标“待 GPT 复查”，不得自行宣布最终验收通过。



## 0A. 用户确认的范围边界：只修正在使用的部分，禁止过度开发

这是本项目的硬性范围约束，优先级高于下文任何看起来像“新增功能”的建议：

1. 本轮目标是把现有网站中用户实际使用的模块修正确、稳定、可信，不是扩建产品，也不是追求商业软件式功能齐全。
2. **不得主动新增全新模块、复杂工作流、后台系统或额外数据结构**，除非它是修复现有功能错误所必需的最小改动，并且先在工单执行记录中说明原因。
3. 不得为了满足清单而开发用户没有在用、现有网站也没有的功能。遇到此类项目，标记“本轮不做（超出使用范围）”，不要擅自实现，也不要因此阻塞验收。
4. 现有功能的修复优先于新增功能：保留现有导航、题库、KB、MCQ、Mock、Q7、Essay Log、Planner、收藏/笔记/掌握度、导入导出和精英训练等已存在模块；只对实际存在且属于当前使用流程的模块修错、补安全保护、改善可用性。
5. P0 中的题库准确性、评分口径、导航可用、用户数据不丢失、官方内容不被篡改属于核心正确性，必须完成。
6. 无障碍、移动端、搜索、打印等只修复现有界面中影响实际使用的缺陷；不为追求形式完整而增加复杂组件或重构整个界面。
7. 术语中英对照只针对实际展示给用户、且确实出现相关术语的中文字段。不得为了达到固定数量而塞入无关术语；“每张卡至少三个术语”不作为验收条件。以字段级扫描找缺漏，再人工确认语义和可读性。
8. 验收以用户现有使用路径为准，不以“功能越多越好”为标准。任何新增建议先列为候选，不得直接开发。

### 本轮明确暂缓/不新增的项目

- 周热力图、学习时长统计：暂缓，不是现有核心流程的修复。
- Anki/CSV 新导出系统：暂缓；现有 JSON 备份/迁移功能必须可靠，但不扩展新的导出格式。
- 新建独立“今日复习队列”模块：暂缓；只修复现有 Planner 的记录、到期逻辑和显示，不另造一套队列。
- 新增 KB 三层遮挡自测系统：暂缓；除非源码中已有该功能且只是修复故障，否则不新建。
- MCQ 自动提升/降低 mastery 的新机制：暂缓；不得自动改用户掌握度。现有掌握度和错题记录只保证准确保存。
- 任何额外分析仪表盘、复杂统计或新评分引擎：不在本轮范围。

如 P1/P2 列表与本节冲突，以本节为准。Muse 必须在执行记录中逐项标注“修复现有功能”或“暂缓/不做”，不得把暂缓项目当成未完成缺陷。

## 1. P0 — 阻塞最终验收

### P0-A 符号与导航
逐项确认以下符号真实定义且在调用时可用，不只是字符串出现：VIEWS.practice、BINDS.practice、VIEWS.quant、VIEWS.frequency、ALLMCQ、mcqById、FP_EX、LOGIC_EX、COUNTER_EX、MULTI_EX、EVAL_EX、REWRITE_BASES、COUNTRIES、eliteLogRoot、drAnsweredCount、drTrainProg、logElite、eliteCount、bhFilledCount、dimOf。
逐个检查对应按钮/导航点击路径，不得 ReferenceError、TypeError、空白页。
VIEWS.mocknav / BINDS.mocknav 不得无保护地直接调用 VIEWS.practice() / BINDS.practice()；改为 go("practice") 或提供安全存在性检查和回退。

### P0-B 题库分类、显式元数据与统计
1. 从 ESSAYS 移除 202506A-1（Section A 选择题），或确保所有列表、筛选、统计统一排除 Section A；重新核算“84 道 Essay”等数字。
2. Essay 显式存 section、marks、qLabel、source/verified 状态；不得只按题号后缀推断。未知值用 null/待核实。
3. SRC_PENDING / pending / parseCode(c).unverified 必须覆盖任何推断；不得把 pending 题计入已核实统计或作为已确认 Section C 20 分题。
4. 所有“已核对/已填充/已掌握”统计排除 pending、SRC_PENDING、unverified；徽章、Dashboard、Settings、搜索使用统一口径。
5. 统一 202310-8 demand/eval：两处均写“官方核对硬规则：排序、幅度、短长期、净进口/出口国差异”；若来源不足以支持，统一降级为“同系列官方口径”，不得互相矛盾。
6. INT_FREQ 必须由 INT_FREQ_DERIV 实时生成。若保留常量，加入一致性断言；不一致时显示“频率数据不一致，请检查”。
7. logDispTotal 与 weakEssays 按 essayTypeInfo(r).max 计算：Section B 14 分按 /14，Section C 20 分按 /20，整卷按 /80；不得统一按 /20。未知题型安全处理。

### P0-C MCQ 与模拟卷
1. 修复 off-2401-5：UI 显示 Japanese yen / US dollar 两栏表头，或将选项写成完整“买入日元/卖出美元”等表述，确保可作答。
2. 核对 off-2506a-2 选项 B：英文究竟是 “current account deficit” 还是 “current account”；中文必须与官方 MS 逐字对应。不得改英文选项。
3. mockBodyHTML Section C 的 cOpts 过滤 SRC_PENDING 与 parseCode(c).unverified。essayByCode(c) 为空时跳过并显示“题目数据缺失”，不得抛错。
4. 确认 MCQ_OFF 完整 36 道、数组闭合；VIEWS.mcq 完整含 ALLMCQ、MCQF 过滤、会话、计时、答题记录、错题写入、结果页。
5. p3/p5 截断处不得在最终文件留下未闭合字符串、数组、对象或脚本。

### P0-D 导入、持久化与降级
1. BINDS.settings 的 pendingImport / pendingRaw 提升为模块级变量，保证切页返回待确认数据不丢；或明确提示“请在本页完成导入，切换页面将丢失待确认数据”。
2. IndexedDB 不可用/事务失败时页面仍可浏览，显示醒目红色横幅“数据仅本次有效”，说明刷新/关闭可能丢失，JSON 导出仍可用。只有事务 oncomplete 后才显示保存成功。
3. 刷新后验证收藏、笔记、掌握度、错题、Essay Log、drProg、mockHistory、eliteLog、drafts、mock、selfeval 保留。
4. JSON 导出后分别测试合并导入、覆盖导入；两种方式区别在按钮旁常驻说明。
5. 设置页导入确认区建议一次性渲染 + 事件委托，重复选择文件不得残留旧监听。

### P0-E 移动侧栏与无障碍
1. 移动侧栏不再用 display:none + transition 切换；使用 visibility / opacity / transform。
2. 支持 Escape 关闭、遮罩点击关闭；打开时焦点进入侧栏，关闭后焦点返回菜单按钮；合理管理背景焦点。
3. 增加 :focus-visible；toast 加 aria-live="polite"；SVG 加 <title>/<desc> 与 aria-labelledby；增加“跳到主内容”链接；所有图标按钮有 aria-label。
4. 长英文括号在移动端可换行、不遮挡按钮；只对相关容器增加 overflow-wrap:break-word / word-break:break-word，不做破坏布局的全局替换。

### P0-F 内容纠错
修复 202210-9 中文范文“对被 displaced 的工人”为“对被替代/失业工人的转移支付”。官方英文不改。

## 2. P1 — 体验与学习逻辑

1. Settings 报告卡：第一轮/第二轮/第三轮/v5/Round 4/Round 5 改为 <details> 默认收起；仅“最终整合 · 已完成与待补充”默认展开，便于找到导入导出和主题。
2. 搜索补全：MCQ 选项与解析；KB principle/mechanism/lose；Essay 范文段落；Collection 标签。搜索范围说明必须与实现一致。
3. VIEWS.collection 搜索输入约 200ms debounce；标签输入支持回车添加。
4. Planner 复习后记录“记住/模糊/忘记”并动态调整间隔；首页显示“今日复习 N 张”。打开页面本身不算复习。
5. 【暂缓，不新增】MCQ 自动 mastery 建议/维度建议机制；本轮不得引入自动掌握度变更。只保证现有答题记录和掌握度数据正确。
6. 【暂缓，不新增】KB 三层遮挡自测系统；除非当前源码已有该功能且仅需修复现有故障，否则不开发。
7. 只修复现有 AO 追踪口径：优先明确标注“整卷记录不计入 AO 平均”；除非现有数据模型确实需要且不会破坏兼容性，否则不新增 mockAo 字段。
8. 设置页导入确认区用一次性渲染 + 事件委托，避免重复监听。
9. VIEWS.extension 的 Development 深链 kp6x 占位卡顶部显示“内容待补充，以下仅为官方子目清单”。
10. 打印样式隐藏侧栏、报告卡、savebar、toast；图表白底黑字；表格不横向溢出。

## 3. P2 — 学习能力提升与内容润色

1. 【暂缓，不新增】周热力图/学习时长统计。本轮不开发新统计模块。
2. 【暂缓，不新增】Anki/CSV 导出。本轮只确保现有 JSON 导出/导入可靠。
3. 【暂缓，不新增】独立今日复习队列；只修复现有 Planner 的到期与复习记录，不新建另一套队列。
4. 中文润色：kp21a “single international market”改为“更紧密一体化的国际市场”；kp22a“绝对贫困↓”补“取决于增长质量与分配”；kp27c 改为“生产补贴通常降低国内价格，不产生关税式消费端无谓损失，但有财政成本与资源错配”；kp25e 明确 Prebisch–Singer 只用于长期/初级产品出口国。
5. 每个 VER 详情增加官方 MS 原文链接/来源说明；Q7P 镜像 MS 标注“镜像来源，非 Pearson 直链”；Pearson 直链失效时说明。
6. 页脚显示内容最后核对日期、数据版本号、导出提醒；Dashboard 显示上次保存时间及“建议每周导出备份”。
7. Jun 2025 01 Q4 AD/SRAS、Oct 2023 Q6 大米缓冲库存两道图题保持“需对照原卷图示作答”；不得用示意图冒充原图。若绘制必须标“示意图，非原卷图”。
8. 显示“官方 grade boundaries 待补充”空状态；成绩页注明“原始分，非 UMS/等级”。
9. 搜索命中 pending 卡时提示“仅官方子目清单，正文待补充”。
10. 数据迁移提示：换设备/浏览器使用 JSON 导出导入；合并/覆盖区别常驻显示。

## 4. 专有名词中英对照规则

目标：中文解释、中文题干、中文解析、中文评价要点中，经济学术语首次出现统一写“中文（English）”，中文在前、英文在后，使用全角中文括号且括号前不空格。普通术语英文小写；专有名词/缩写保留大写；理论名保留连字符。例如：
- 经常账户（current account）；经常账户赤字（current account deficit）；经常账户顺差（current account surplus）
- 马歇尔-勒纳条件（Marshall–Lerner condition）；J 曲线效应（J-curve effect）；普雷维什-辛格假说（Prebisch–Singer hypothesis）
- 外国直接投资（foreign direct investment, FDI）；跨国公司（multinational company, MNC）
- 人类发展指数（Human Development Index, HDI）；购买力平价（purchasing power parity, PPP）
- 世界贸易组织（World Trade Organization, WTO）；国际货币基金组织（International Monetary Fund, IMF）；欧洲中央银行（European Central Bank, ECB）
- 总需求（aggregate demand, AD）；总供给（aggregate supply, AS）；短期总供给（short-run aggregate supply, SRAS）；长期总供给（long-run aggregate supply, LRAS）
- 生产可能性边界（production possibility curve, PPC）；长期平均成本（long-run average cost, LRAC）
高频词表还应覆盖：资本与金融账户、国际收支、贸易条件、比较优势、绝对优势、机会成本、专业化、关税、配额、补贴、非关税壁垒、贸易创造/转移、自由贸易区、关税同盟、共同市场、经济与货币联盟、汇率、贬值/升值、法定贬值/升值、洛伦兹曲线、基尼系数、消费者/生产者剩余、无谓损失、挤出效应、结构性失业、职业流动性障碍。
同一张知识卡、同一道题、同一段解析内，首次出现才加英文；后文不重复。若已有“中文（English）”跳过。避免双重括号；如“经常账户（current account, CA）”，不要“经常账户（current account）（CA）”。长英文括号仅对相关容器加换行样式。

绝对禁止改：
- 官方英文 VER.en、MODELS.paras、Q7P.parts[3]、MCQ_OFF.q、MCQ_OFF.opts、官方 MS 英文要点；
- 官方英文选项、英文范文正文；
- 代码标识符、字段名、id、kp 编号、URL、文件名、函数名；
- 统计数字、评分口径、内容核对状态。
不做全文机械替换。

扫描字段：
- KB：defZh、principle、causes、mechanism、chain、diagramNote、caseTxt、essayUse、drUse、mcqTrap、confusion、lose；
- SPEC/THEMES/ESSAYS：zh；
- VER：kaa/eval/chain 中文部分；
- MODELS：demand、command、structure、ao、chains、whyEval、whyJudge、lose、zh 中文部分；
- Q7P：pts 中文要点、parts 中文说明（不得改 parts[3] 英文）；
- 自编 MCQ：qzh、why、wrong；MCQ_OFF：qzh、topic、why、wrong（不得改 q/opts）；
- DRTRAIN：options、why、prompt 中文部分；
- COMMAND_WORDS、TENSTEPS、DIMS、ERR_TYPES 中文说明；
- Settings、Dashboard、复习计划、错题本、搜索说明中的中文术语。

自动扫描建议：建立术语数组，对每个字段逐字段检查术语首次出现后 0–6 字符内是否有对应“（English）”；缺失输出字段名、术语、建议改法。人工复核后批量改。修改后复扫，确认首次出现均带英文括号且无重复。不得只做全文件字符串扫描后声称字段级通过。

## 5. 最终验收标准与交付

1. 每个 inline script 单独通过 node --check；外链 script 不当作 inline 检查。浏览器本地打开 console 零错误。
2. 15 个导航项逐项点击无空白/异常；Mock Examination、Practice Room、Quantitative Skills、Exam Frequency 可进入。
3. 桌面 1440px、移动 390px、暗/亮主题截图；移动侧栏开关、Escape、焦点进出实测。
4. 刷新后收藏、笔记、掌握度、错题、Essay Log、drProg、mockHistory、eliteLog、drafts、mock、selfeval 保留；导出 JSON 后分别测试合并/覆盖导入。
5. 无 IndexedDB 环境页面可浏览，红色“数据仅本次有效”横幅可见，导出按钮可用。
6. 所有统计与 ESSAYS/SRC_PENDING/MCQ_OFF/Q7P/INT_FREQ_DERIV 一致；pending/unverified 有徽章且不计入已核对。
7. 随机抽查实际使用中的 KB 卡；检查其中实际出现的高频术语首次出现是否带英文括号，不设每张卡至少三个术语的硬指标。
8. 随机抽 10 道 MCQ、5 道 Essay、5 套 Q7，中文题干/解析首次出现术语带英文。
9. 搜索 current account 与“经常账户”、Marshall–Lerner 与“马歇尔-勒纳”均命中相同内容集合。
10. 390px 长英文括号不溢出、不遮按钮。
11. 官方英文原文、英文选项、英文范文正文逐字未改。
12. 必须交付：单一完整 HTML；术语扫描报告（术语/字段/改前/改后）；未修改清单（英文原文、英文选项、代码字段未动，附 diff/hash）；验收截图与 console 截图；每个 inline script 的 node --check 输出；导入导出记录；Mock/练习室/精英训练/搜索点击路径截图。无法完成项写明原因、风险和替代方案。

## 6. GPT 对当前基线的初步静态核验（不是最终验收）

对象：review/chatgpt-v5/index.html，SHA aa6b5992c8ac0ddba4d4f3d5f7d747691107d245。只做源码静态检索，未运行 Node 测试，未打开真实浏览器。

已确认：
- 指定的 20 个符号均至少能检索到一次；这不代表运行时可调用。
- VIEWS.mocknav 仍直接返回 VIEWS.practice()；BINDS.mocknav 仍直接调用 BINDS.practice()，需修。
- 202506A-1 仍在 Essay 数据映射数组，且注释标明 Section A 选择题，需移除或全局排除。
- parseCode() 仍按题号后缀推断 Section / marks，需改显式元数据并保证 pending 覆盖。
- INT_FREQ 仍是硬编码常量，INT_FREQ_DERIV 是另一份逐题推导表，需实时生成或一致性断言。
- weakEssays 排序仍用 a.mock?80:20，需按 essayTypeInfo(r).max；logDispTotal 也须检查显示分母。
- 当前 CSS 未检索到 visibility/opacity/transform 的侧栏方案；脚本未检索到 Escape 处理。
- 未检索到 :focus-visible、aria-live、跳转主内容链接、overflow-wrap。
- toast() 当前只更新文本和 class；SVG svgWrap() 目前只有 role=img 与 aria-label。
- 文件有 10 个 script 标签，部分是外链；最终 node --check 只逐个提取 inline script。
- 当前文件无“第 X 部分”注释，HTML 末尾闭合完整。

仍待逐项源码确认：
- 202310-8 demand/eval 具体冲突、202210-9 中文范文错词；
- off-2401-5 实际表头渲染、off-2506a-2 B 项中文与官方 MS；
- mockBodyHTML 的 cOpts 过滤和 essayByCode 缺失处理；
- pendingImport/pendingRaw 跨页生命周期；
- MCQ_OFF 数量、VIEWS.mcq 完整路径；
- INT_FREQ_DERIV 与最终统计一致性；
- 所有统计徽章/Dashboard/Settings/搜索是否统一 verified 口径；
- P1/P2 现有实现程度、重复开发风险；
- 术语字段级扫描与官方英文保护范围；
- 浏览器 console、截图、真实 IndexedDB、导入导出、移动焦点实测。

## 7. Muse 执行记录（完成后填写）

| 项目 | 状态 | Commit / 文件 | 测试证据 | GPT 复查 |
|---|---|---|---|---|
| P0-A 符号与导航 | 待执行 | | | |
| P0-B 题库元数据与统计 | 待执行 | | | |
| P0-C MCQ / Mock | 待执行 | | | |
| P0-D 导入 / 持久化 | 待执行 | | | |
| P0-E 移动端 / 无障碍 | 待执行 | | | |
| P0-F 内容纠错 | 待执行 | | | |
| P1 体验与逻辑 | 待执行 | | | |
| P2 学习增强与内容润色 | 待执行 | | | |
| 中英术语扫描 | 待执行 | | | |
| 全部交付证据 | 待执行 | | | |

执行建议：先完成 P0-A 至 P0-F；完成后 GPT 针对性复查。再对实际展示且正在使用的中文内容做术语扫描（先保护官方英文与代码字段，再人工抽查）。最后只修复用户实际使用的现有 P1/P2 功能。所有“暂缓/不新增”项目不得实施，也不得作为验收阻塞项。不得为了追求清单全绿而扩建产品、重写模块或破坏现有数据/主流程。
