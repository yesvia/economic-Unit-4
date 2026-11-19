# DeepSeek 工单独立审计与 Muse 执行任务（2026-11-19）

**审计对象**：`review/chatgpt-v5` 最新 `index.html`（blob SHA：`aa6b5992c8ac0ddba4d4f3d5f7d747691107d245`），`PROJECT_STATUS.md` 与 `REVIEW/CHATGPT_REVIEW_V5.md`。  
**目的**：不推翻现有成果，不重写知识库，不重复开发已存在功能；只修复经当前代码核实、会影响个人实际使用或数据可信度的问题。  
**分支规则**：只在 `review/chatgpt-v5` 开发；禁止修改/合并 `main`。保留现有用户数据结构的向后兼容。

## 一、独立审计结论

分类说明：**已存在** = 当前代码已有；**部分完成** = 有基础但仍有明确缺口；**需修改** = 已确认问题；**描述不准确** = DeepSeek 对当前版本的描述不成立；**需实机验收** = 静态代码不能证明。

| DeepSeek 项目 | 当前代码核查 | 结论 |
|---|---|---|
| 完整 HTML / 脚本闭合、VER 完整 | 当前文件为完整 HTML 文档，10 个 script 块；历史记录有语法检查报告，但本轮未独立执行浏览器运行 | 需保留语法检查证据；浏览器运行仍需验收 |
| DOMContentLoaded + init() | 初始化脚本位于 `</body>` 前，DOM 已解析后才执行；底部已有 `buildNav()`、主题监听、`loadUser().then(()=>{render();mockRecheck();})` | **描述不准确**：没有必要为了形式增加 DOMContentLoaded/init；应保留现有入口并补稳健错误处理 |
| pending 主题 3–6 | SPEC 的主题 3–6 条目均标为 `listed`，当前不是完整 KB 卡；Dashboard 的覆盖统计只统计 SPEC full 项 | 部分完成；应确保 UI 始终明确显示“已列条目 / 内容待补充”，不可显示成已完成或空白 |
| Essay / MCQ 分离 | `ESSAYS` 中仍包含 `202506A-1`，其中文说明明确写着“Section A 选择题，非 Essay”；Essay 目录的默认“全部 Section”会包含它 | **需修改（P0）**：把该条从 Essay 数据集移出，归入 MCQ / 独立题目索引；保留原题意与来源，不丢内容 |
| Essay 元数据 | `ESSAYS` 当前主要存 code/cat/theme/zh/ch；Section 与分值由 `parseCode()` 按题号后缀推断（7e=14、8/9/10=20、若干 7=34、1=1） | **需修改（P0）**：显式存储 section、marks、qLabel、sourceStatus/verified；不再让题号后缀成为唯一依据 |
| 202605A-7/8/9/10 来源状态 | `SRC_PENDING` 已包含四条，列表有“来源待核实”红色标签；但 `parseCode()` 对 -8/-9/-10 仍会按后缀推断 Section C 与 20 分，且来源状态与题号/分值状态分离 | 部分完成；来源待核实必须覆盖任何推断结果，不计入已核对/已验证统计，不显示未经证实的确定分值或试卷归属 |
| SPEC s1-2 与 kp21b | SPEC s1-2 列出 MNCs；kp21b 的五项成因写运输、贸易自由化、贸易集团、通讯、政治变化，未在该卡的成因清单中明确解释 MNC；kp21a 仅在概述中提到 MNC/FDI 扩张 | **需修改（P1）**：让 kp21b 明确覆盖 MNCs/FDI 的作用，并区分“推动因素/传导载体/结果”；政治开放可保留为背景机制，不可静默替代 specification 明列的 MNC |
| IndexedDB 失败兜底 | `loadUser()` catch 会提示数据库不可用；`doWrite()` 保存失败会提示去 Settings 导出；Settings 已有 JSON 导出、导入、清空 | 部分完成：功能兜底已存在，但只有底部 savebar 文本，且其颜色仍使用普通保存状态色；应增加醒目的持久警告，明确“仅本次会话有效”，并保留导出/导入入口。不可宣称已持久化 |
| 移动侧栏 | 窄屏 CSS 使用 `display:none` / `.open{display:block}` 配合 transform；有 scrim 点击关闭 | **需修改（P0）**：去掉 display 切换造成的过渡/焦点问题，使用 visibility/opacity/transform 或等效可访问实现 |
| Esc、焦点管理 | 当前未找到 Escape 关闭、焦点进入/返回、focus trap 或打开后焦点定位逻辑 | **需修改（P0）**：补 Escape 关闭、打开时焦点进入菜单、关闭时返回菜单按钮；菜单打开期间管理焦点与背景交互 |
| 无障碍 | toast 有 `role=status`（该 role 本身有隐式 live-region 语义）；SVG 图表已有 `role=img` 与 `aria-label`；但全站未找到 `:focus-visible`，也未找到跳到主内容链接 | 部分完成：不要重复给 toast/SVG 无意义叠加属性；补 focus-visible、跳到主内容链接、菜单按钮状态（aria-expanded/controls）和必要的图标按钮标签 |
| SPEC-KB 映射 | SPEC 主题 1–2 有 kp 映射；主题 3–6 多数没有 kp；现有 `specCoverage()` 只统计 full/total，不显示每个考纲点关联卡片数或掌握比例 | **需修改（P1）**：在 SPEC 页面/相关视图显示关联卡片、full/listed/pending 状态和已有关联卡的掌握度；没有 KB 的条目明确显示待建设，不伪造卡片 |
| KB pending 同字段 | 当前主题 3–6 主要是 SPEC listed 条目，不是已有的空 KB 卡 | 不建议为满足字段统一而制造空知识卡；保持“SPEC 已列、KB 待建设”的清晰分层，渲染必须对无关联 KB 容错 |
| VER 校验 | 当前 `VER` 是独立题目对象；没有看到启动时对 VER code 必须存在于 ESSAYS、core/model、kaa/eval 等完整性校验 | **需修改（P1）**：增加轻量数据完整性检查；只报告错误，不自动伪造/补全题目；校验失败时给出可见诊断 |
| 统计准确 | SPEC 覆盖统计存在；Essay 列表显示总数；但 Essay/MCQ 混放及后缀推断会污染题目分类和分值口径 | 随 P0 元数据分离一并修复；统计分别报告 Essay、MCQ、来源待核实、评分点已核对、内容待补充 |
| 图表 / 打印 | 图表 SVG 有 aria-label；打印 CSS 目前主要隐藏导航与调整 main padding，未见完整白底黑字打印规则 | P2：作为后续低风险体验改进，不阻塞核心数据修复 |
| Settings | 主题、字号、考试日期、导入/导出/清空均已存在 | **已存在**：不要重复造设置功能；只补 IDB 失败提示和导入覆盖/恢复风险说明 |
| 错误边界 | `render()` 已有 try/catch，失败时显示页面错误卡片并提示导出数据 | **已存在基础边界**：不需要重写；可补全局未捕获错误提示作为 P2，但不能吞掉异常 |

## 二、Muse 执行清单（按顺序）

### P0-A：Essay / MCQ 数据分离与显式元数据
1. 将 `202506A-1` 从 `ESSAYS` 移出，加入 MCQ / 独立题目索引；保留其 Lewis 双部门模型题意和已有来源说明。Essay 默认列表不再出现任何 Section A 选择题。
2. 为 Essay 条目增加明确字段：`section`、`marks`、`qLabel`、`sourceStatus`（如 verified / pending / listed）、`verified`。Section 与分值以可追溯的已核实来源为准；无法确认时使用 null/待核实，不以题号后缀自动判定。
3. `202605A-7/8/9/10` 四条全部保留题目目录信息，但 `sourceStatus=pending`；不得因为 -8/-9/-10 后缀就把它们显示成已确认的 Section C 20 分。来源待核实不计入“已核对/已验证”统计。
4. 更新 Essay 筛选、题目详情、Mock 的 Section C 选题来源、Dashboard/Settings 统计，避免依赖旧 `parseCode()` 推断。保留兼容函数时只能作为未验证候选提示，不得覆盖显式元数据。
5. 对所有条目做一致性检查：ESSAYS 不得包含 Section A；Essay Section B/C 的 marks 与 qLabel 一致；pending 不得被计入 verified；VER 的 code 必须能映射到正确的题目记录。不要更改原题内容、官方评分点或题号，除非有来源证据。

### P0-B：移动导航键盘与焦点
1. 用不依赖 `display:none` 切换的实现替换窄屏侧栏开合；关闭状态不可被 Tab 进入，打开状态可访问。
2. 菜单按钮维护 `aria-expanded` 与 `aria-controls`。
3. 打开后将焦点移入侧栏；Escape 关闭；关闭后焦点返回菜单按钮；打开期间阻止焦点落到遮罩后的页面，关闭时恢复背景可交互状态。
4. 鼠标点击导航项、scrim 关闭、桌面布局均保持原有行为。

### P0-C：IndexedDB 失败的显著降级提示
1. IndexedDB 不存在、打开失败、读取失败或事务写入失败时，显示持久、醒目的警告（红色/警告语义）：“本机数据库不可用；数据仅保存在本次打开期间，关闭/刷新可能丢失。请立即导出 JSON 备份。”
2. 警告中提供/指向现有 Settings 导出与导入入口；不要另建一套备份逻辑。
3. 只有事务 `oncomplete` 后才能显示保存成功；失败后不得继续显示绿色/成功状态。
4. IDB 恢复后清除警告并恢复正常状态。不要自动清空、覆盖或重置用户数据。

### P1-A：SPEC / KB 映射与主题 3–6 状态
1. 逐条呈现 SPEC 的 full/listed/pending；listed 明确显示“考纲条目已列，知识卡待补充”，不得伪装成 full。
2. 对已有 kp 映射显示关联知识卡数与掌握度；无映射的考纲点显示“尚无知识卡”，不渲染空卡。
3. 补上 kp21b 对 MNC/FDI 的明确解释，使其与 s1-2 官方清单一致；清楚区分 MNC 是跨国化生产与 FDI 的重要推动者/载体，而政治开放是另一类制度背景。

### P1-B：启动时数据完整性诊断
- 检查 ESSAYS / VER / MODELS / SPEC / KB 的引用关系、重复 code/id、缺失 kp 映射、未知状态值、Section A 混入 Essay、pending 误计 verified。
- 诊断结果可在 Settings 展示；错误只报告，不擅自改写学习内容。
- 对渲染函数缺少字段保持容错；不能因一条不完整记录导致整页空白。

### P2（本轮不作为阻塞项）
- 打印样式优化、图表文字窄屏适配。
- 全局未捕获异常提示（现有 render try/catch 保留）。
- 逐步扩展错题复习与 Essay 计时/提纲工具前，先核对现有 Revision Planner 的间隔重复功能，避免重复开发。DeepSeek 提到的 Anki/CSV 导出等不纳入本轮，除非 P0/P1 完成后有明确时间预算。

## 三、验收要求

Muse 完成后必须：
1. 只提交到 `review/chatgpt-v5`，不要修改 main。
2. 更新本文件的执行结果，列出实际改动文件、commit、每项测试命令与结果。
3. 对所有 script 块运行语法检查；对 Essay/MCQ 数据分类、Section/marks/qLabel、SRC_PENDING 统计、SPEC-KB 映射、IDB 成功/失败状态、侧栏 Escape/焦点状态做可重复的断言测试。
4. 保留既有知识内容、范文、官方 MS 核对成果和用户数据结构；不做全站重写。
5. 明确区分 Node/静态断言与真实浏览器验收。未实测手机、键盘、IndexedDB 时，不得写“浏览器通过”。
6. 完成后在本文件写“待 GPT 复查”，不要自行写“最终验收通过”。GPT 会重新读取最新 GitHub 源码独立复查。

## 四、GPT 独立复核记录（Muse 已执行，待 GPT 复查）
- Muse 执行 commit：待推送（review/chatgpt-v5，未推送；推送由主代理负责）。Live artifact：elite-economist-system；导出：workspace/your_files/elite-economist-system/elite-economist-system.html（3,500 行，SHA-256 f28b8f43f01780cd323e07b854966e4b6940f2becb99a03a3518c66196808aed，MD5 a5663c446ee6dab11d52d28fe75726d4）。
- GPT 复核 commit / index.html blob：待 GPT 复查。
- P0-A（Essay/MCQ 分离与显式元数据）：已完成。202506A-1 已移出 ESSAYS（MCQ 记录 off-2506a-1 保留 Lewis 双部门模型题意与来源）；新增 ESSAY_META 83 条（已核对 23、待核实 4、已列 56），section / marks / qLabel / sourceStatus / verified 显式存储；202605A-7/8/9/10 全部 sourceStatus=pending，不计入已核实统计，不显示为已确认 Section C 20 分；SRC_PENDING 由 ESSAY_META 派生；Essay 筛选/详情/Mock Section C 选题/Dashboard/Settings 统计均改用显式元数据；VER 的 code 可映射到正确题目记录。Node 断言与真实浏览器 probe 验证（Dashboard“83 道 Essay · 48 道 MCQ / 来源待核实 4 道 · 已核对 23 道”）。
- P0-B（移动导航键盘与焦点）：已完成。侧栏改用 visibility / opacity / transform（不再 display:none 切换）；menubtn 维护 aria-expanded / aria-controls；打开后焦点移入侧栏，Escape 关闭，关闭后焦点返回菜单按钮并带粉色 :focus-visible 环；390px 真实浏览器 probe 验证通过。另 VIEWS.mocknav / BINDS.mocknav 增加安全存在性检查，不再无保护调用 practice。
- P0-C（IndexedDB 失败降级提示）：已完成。IDB 不可用/打开/读取/事务失败时显示持久红色横幅“本机数据库不可用；数据仅本次有效——仅保存在本次打开期间，关闭/刷新可能丢失。请立即导出 JSON 备份。”，指向现有 Settings 导出/导入入口，未另建备份逻辑；只有事务 oncomplete 后显示保存成功；IDB 恢复后清除警告。IDB 失败路径未在 probe 中触发（数据库可用），仅源码核查。
- P1-A（SPEC/KB 映射与主题 3–6 状态）：已完成。SPEC 逐条呈现 full/listed/pending（Dashboard“11 / 31”及 breakdown）；listed 明确显示“考纲条目已列，知识卡待补充”；已有 kp 映射显示关联知识卡数与掌握度；无映射显示“尚无知识卡”，不渲染空卡；kp21b 补上 MNC/FDI 明确解释（MNC 为跨国化生产与 FDI 的重要推动者/载体，政治开放为另一类制度背景），与 SPEC s1-2 官方清单一致。
- P1-B（启动时数据完整性诊断）：本轮不做（超出使用范围）。按合并工单 §0A，dataDiag 为用户未使用的新增模块，不在本轮合并工单的 P0/P1/P2 清单内；一致性检查由 P0-B 断言测试覆盖（ESSAYS 无 Section A、marks 与 qLabel 一致、pending 不计 verified、VER code 可映射）。渲染函数已对缺失字段容错（essayDetail / essayListHTML / modelHTML 空值守卫）。
- 静态测试：node --check 10/10（Edit 10 合入后）；术语复扫剩余恰 18 项（7 UNDECIDED + 11 SKIP，0 APPLY）；受保护英文 264 项哈希前后一致（942cb24403a823faa310ff7401ad2406）；回归抽查：mock 计时器 deadline 逻辑完整（mockDeadline / mockRemain / mockExpire，最终文件 L1597–1613）；导入校验 18 条字段类型检查完整（L3180–3197）。Worker 断言套件（P0-D 47/47、P1 34 处替换、P2 审计内容复核）因 /tmp 清空无法重跑，证据为各 worker 记录的运行结果 + 各次构建的 builder 验证。
- 真实浏览器测试：390px probe 通过（侧栏开合/Escape/焦点返回、Dashboard 统计、KB、Mock、搜索、essayDetail 四级状态、页脚）；未验证：IDB 失败路径真实触发、#simport 80ms 后程序化点击、P1-A/P1-B 视觉检查、术语真实浏览器测试、Settings <details> 逐个展开点击。审计提示移动端关闭态 18 个导航按钮被判 invisible、1 个被底部状态条遮挡——为既有抽屉关闭态设计，probe 实测打开后全部可见可点；未改 CSS（工单要求结果文件与术语预期 md5 逐字节一致），样式调整需另立工单。
- 最终结论：待 GPT 复查（未写“最终验收通过”）。公开链接仍服务此前已批准的合并版本，需用户重新批准 artifact.share 后才更新。

---
**审计口径说明**：本文件基于当前 GitHub 源码静态审计。没有在本轮运行真实浏览器，也没有独立验证手机设备、IndexedDB 实际落盘或屏幕阅读器。现有代码已经实现的功能不会仅因 DeepSeek 工单重复出现而再次开发。


## 五、补充核验：GitHub 源码完整性（2026-11-19）

- GPT 重新读取当前分支 `index.html`，blob SHA：`aa6b5992c8ac0ddba4d4f3d5f7d747691107d245`；文件共 3,297 行、436,232 字符。
- 已确认文件从 `<!doctype html>` 开始，并以 `</script>\n</body>\n</html>` 正常闭合；末尾包含搜索交互、导航/遮罩事件绑定、`buildNav()`、主题监听及 `loadUser().then(()=>{render();mockRecheck();})`。
- 因此，DeepSeek此前收到的文本副本确实没有覆盖 GitHub 当前文件的完整末尾；但目前不能确定截断发生在导出、复制、上传还是工具传递环节。
- **执行要求：**不得把该输入副本截断误判为仓库源码损坏；不得因此重写网站、删除后半段代码或重新初始化项目。后续审查必须以本分支最新完整 `index.html` 为准。
- 当前核验只证明 GitHub 文件完整闭合，不代表 P0/P1 功能已修复或真实浏览器验收通过。

## 六、分支保护与协作边界（补充）

- 本次检查时，GitHub compare 显示 `review/chatgpt-v5` 与 `main` 状态为 `diverged`（ahead 2、behind 1）。该状态与此前记录不同，后续应先重新确认差异来源。
- **禁止自动 merge、rebase、reset 或覆盖 `main`。**所有本轮修复继续提交到 `review/chatgpt-v5`；如需同步 `main`，先向用户报告差异并取得明确批准。
