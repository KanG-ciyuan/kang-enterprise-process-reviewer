# Kang Enterprise Process Reviewer

[English](README.md) | 简体中文

[![Release](https://img.shields.io/github/v/release/KanG-ciyuan/kang-enterprise-process-reviewer?display_name=tag&sort=semver&style=flat-square)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Last commit](https://img.shields.io/github/last-commit/KanG-ciyuan/kang-enterprise-process-reviewer?style=flat-square)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/commits/main)

**审查企业流程是否真正可执行、可追责、可恢复，并具备安全引入 AI 的条件。**

这是一个只读的流程审查 Skill，面向 SaaS、内部工具、服务运营和 AI 辅助流程。把一份拟定的流程交给它，它会就责任、证据、授权、交接、异常、升级和人工决策点给出审查结论——但不会改动被审查的产品。

行为内容只有一份 Markdown 提示词契约 [`SKILL.md`](SKILL.md)（45 行），加上 [`references/process-rubric.md`](references/process-rubric.md) 这份 46 行的评分细则。仓库里没有运行时代码，也没有脚本。

**状态：public release candidate（公开发布候选）。** `manifest.json` 中声明 `"status":"public-release-candidate"`，`reports/creation-handoff.md` 也称 v0.2.0 是「a public release candidate pending release evidence」。标签与 GitHub release `v0.2.0` 均已存在，且指向当前 HEAD。同一份 `manifest.json` 还带有 `"maturity_tier":"production"`，这与候选状态的措辞以及缺失的输出证据文件相互冲突；本 README 采用候选状态的措辞。详见[验证证据](#验证证据)。

## 为什么流程图不够用

> 流程图只说明工作流向哪里。治理良好的流程会说明谁负责、什么证据构成授权、异常如何恢复，以及 AI 必须在什么时候停止。

流程图描述的是「预期的流转路径」。它不说明谁拥有决策权、一个动作在什么前提下才算合法、谁接收交接结果、以及顺利路径失败时会发生什么。而这些恰恰会在真实运行中变成自我审批、静默失败、无人认领的异常，以及没人敢回退的自动化。

因此这项审查拒绝把任何单一来源当作完整事实。来自 [`references/process-rubric.md`](references/process-rubric.md)：

> Interviews describe experience; logs describe recorded behavior; policies describe intended rules. None alone proves the complete process. Preserve conflicts instead of averaging them away.

（访谈描述的是经验，日志描述的是已记录的行为，制度描述的是预期规则。任何单一来源都无法证明完整流程。冲突应当被保留，而不是被平均掉。）

| 流程图能回答 | 通常留下的空白 | 本审查关注的位置 |
| --- | --- | --- |
| 步骤顺序 | 每一步由谁负责 | 每个关键节点的 actor 与可追责负责人 |
| 在某个判断处分支 | 判断凭什么被授权，谁不能审批 | 授权规则与职责分离 |
| 顺利路径 | 失败、超时、重试与恢复行为 | 每个节点的 failure、recovery、timeout、escalation |
| 终态 | 接收方是否验收，是否可审计 | 接收方验收与审计记录 |

## 简单流程 vs 治理流程

| 关注点 | 简单流程 | 治理流程 |
| --- | --- | --- |
| 节点定义 | 一个带标签的方框 | 明确写出 actor、trigger、input、rule、action、output、receiver、evidence、authorization、failure、recovery、timeout、escalation、audit record |
| 审批 | 「经理审批」 | 审批人、决策对象、所看证据、允许的结论、审计记录全部写明 |
| 证据 | 默认存在 | 标注为 `confirmed`、`inferred` 或 `to_verify`，并注明来源 |
| 失败 | 不画 | 每个关键节点的失败模式、恢复路径、超时与升级时限 |
| 完成 | 流程结束 | 接收方就绪、交接被接受、完成状态可观测 |
| AI 参与 | 未定义 | 拆分为确定性规则、有界 Agent 工作与人工判断，并带有硬性停止条件 |

## 审查哪些维度

本仓库真正定义了**七个维度**，每一个都能在 [`SKILL.md`](SKILL.md) 与 [`references/process-rubric.md`](references/process-rubric.md) 中找到明确文本：

| 维度 | 仓库定义了什么 | 出处 |
| --- | --- | --- |
| Responsibility 责任 | 已知 actors、每个关键节点的可追责负责人、输出中的 actor 责任地图 | `SKILL.md:15`、`:31`、`:35`；`references/process-rubric.md:46` |
| Evidence 证据 | 证据标签、来源与充分性、证据台账、每个节点的 `evidence` 字段 | `references/process-rubric.md:5-7`、`:29`；`SKILL.md:21`、`:31` |
| Authorization 授权 | 每个节点的 `authorization` 字段、最小权限审查项、申请/审批/审计职责分离 | `references/process-rubric.md:26-27`；`SKILL.md:31` |
| Handoff 交接 | 每个节点的 `receiver` 字段、接收方就绪与交接验收审查项、输出中的下游交接 | `references/process-rubric.md:34`；`SKILL.md:31`、`:35` |
| Exception 异常 | 异常归属审查项，以及作为输出要素的异常与升级路径 | `references/process-rubric.md:33`；`SKILL.md:35` |
| Escalation 升级 | 每个节点的 `escalation` 字段、升级时限，以及停止并升级规则 | `SKILL.md:31`、`:39-41`；`references/process-rubric.md:33` |
| Human Decision Gate 人工决策门 | 人工判断作为边界类别、人工审批记录要求、未解决的人工决策门会阻止就绪判定 | `references/process-rubric.md:21`、`:23`、`:46` |

**State 不是一个已定义的维度。** 仓库里没有状态章节、没有状态归属规则、没有状态迁移定义。与状态相关的文本只有两处：审查项清单里的一条 `stale state`（`references/process-rubric.md:32`），以及 `to_verify` 证据标签中的 `stale`（`references/process-rubric.md:7`）。陈旧状态、并发与重复处理可以作为审查关注点提出，但这不构成状态机或生命周期覆盖，仓库也没有这样声称。

<details>
<summary>九项审查测试与七步方法</summary>

审查测试（`references/process-rubric.md:27-35`）：授权与最小权限；申请、审批与审计职责分离；证据来源与充分性；敏感数据最小化与保留；可回退性与恢复；重复、超时、重试、陈旧状态与并发；异常归属与升级时限；接收方就绪与交接验收；完成状态可观测与可审计。

方法（`SKILL.md:21-27`）：

1. 冻结范围并建立证据台账。
2. 把每个实质节点建模为 `actor -> trigger -> input -> rule -> action -> output -> receiver`。
3. 为每个关键节点补充 failure、recovery、timeout、authorization、escalation 与审计行为。
4. 区分确定性规则、有界 Agent 或模型工作、人工判断。
5. 测试矛盾、缺失负责人、自我审批、编造证据、静默失败与不可回退的自动化。
6. 应用流程评分细则，然后提出最小可信流程。
7. 在进入任何实施交接之前，先返回阻断项与人工决策。

第 2 步是节点链的建模简写，并不是下文的完整节点合同。合同指 `SKILL.md:31` 与 `references/process-rubric.md:13` 中的 14 字段清单。

</details>

## 节点合同（Node Contract）

这是本仓库最强的一个概念。每个关键节点必须写明 **14 个字段**（`SKILL.md:29-31`，`references/process-rubric.md:11-13` 复述）：

`actor` · `trigger` · `input` · `rule` · `action` · `output` · `receiver` · `evidence` · `authorization` · `failure` · `recovery` · `timeout` · `escalation` · `audit_record`

评分细则补充了完整性规则：

> The process is incomplete if a field is materially relevant but has no owner or explicit `not_applicable` rationale.

（如果某个字段实质相关，却没有负责人，也没有明确的 `not_applicable` 理由，那么这个流程就是不完整的。）

| 分组 | 字段 | 它迫使回答的问题 |
| --- | --- | --- |
| 流转 | `actor`、`trigger`、`input`、`rule`、`action`、`output`、`receiver` | 谁在什么信号下、依据哪条规则行动，结果交给谁？ |
| 合法性 | `evidence`、`authorization` | 什么支撑这个动作，什么允许这个动作？ |
| 韧性 | `failure`、`recovery`、`timeout`、`escalation` | 失败时怎么办，出问题叫谁？ |
| 可追责 | `audit_record` | 留下什么，让后来的审查者可以核对？ |

**这个说法的边界——依赖它之前请先读。** 节点合同**只是一份散文式字段清单**。仓库没有附带 schema 文件、没有校验器、没有示例节点合同实例，也没有任何测试去检查产出的节点。没有任何机制强制执行这 14 个字段；它们是给模型的指令。仓库自身对它也不一致：输出 fixture [`evals/output_cases.json`](evals/output_cases.json) 声称节点只包含 **8** 个字段，静默丢掉了 `action`、`evidence`、`authorization`、`recovery`、`timeout`、`audit_record`（`evals/output_cases.json:8`）。请以 `SKILL.md` 与评分细则中的 14 字段清单为合同，把该 fixture 视为较弱的复述。

## 证据与授权

证据是标注出来的，而不是断言的（`references/process-rubric.md:5-7`）：

| 标签 | 定义 |
| --- | --- |
| `confirmed` | 由权威制度、直接观察、可靠系统记录或具名负责人的确认所支撑。 |
| `inferred` | 由间接或不完整证据支撑，只能作为临时模型使用。 |
| `to_verify` | 无支撑、相互冲突、陈旧、由演示数据生成，或后果严重到必须直接确认。 |

塑造这项审查的规则：演示数据、模型输出和上游报告都不算已确认的业务事实（`SKILL.md:11`）；材料缺失时必须执行有限审查，并把无支撑的结论标记为 `to_verify`（`SKILL.md:17`）；冲突要被保留而不是平均掉（`references/process-rubric.md:9`）；审查测试明确覆盖证据来源、充分性、敏感数据最小化与保留（`references/process-rubric.md:28-30`）。

授权方面，检查最小权限、申请/审批/审计的职责分离，以及每个关键节点上显式的 `authorization` 字段（`references/process-rubric.md:26-27`；`SKILL.md:31`）。输出中必须包含授权与数据控制（`SKILL.md:35`）。

就绪判定——仓库真正定义的 gate 概念——只在 `references/process-rubric.md:44-46` 定义了一次：

> A process is implementation-ready only when critical nodes have accountable actors, evidence and authorization rules, receiver acceptance, exception paths, and audit records. Any unresolved blocker, sensitive-data authority, or human decision gate prevents readiness.

（只有当关键节点具备可追责的 actor、证据与授权规则、接收方验收、异常路径与审计记录时，流程才算实施就绪。任何未解决的 blocker、敏感数据授权问题或人工决策门，都会阻止就绪判定。）

**命名说明。** 本 README 的早期版本以及 `agents/interface.yaml` 曾提到「证据门 / Evidence Gate」。这个词在 [`SKILL.md`](SKILL.md) 和 [`references/process-rubric.md`](references/process-rubric.md) 中都没有定义。真正存在的是上面的证据标签与这里引用的就绪判定（readiness gate）。本 README 只使用这些真实名称。

## 规则 / Agent / 人工 的边界

决策划分只在 `references/process-rubric.md:19-21` 定义了一次，共三类：

| 类别 | 适用条件 |
| --- | --- |
| 确定性规则 | 输入、结果与例外都明确且可测试。 |
| 有界 Agent/模型工作 | 抽取、分类、比较、摘要或建议，且不确定性可见、动作可回退。 |
| 人工判断 | 政策例外、后果重大的审批、模糊的业务事实、敏感访问或不可回退的动作。 |

Agent 一侧的边界写得很明确（`references/process-rubric.md:23`）：

> An Agent may recommend but must not silently approve, certify truth, grant access, or suppress an exception. Human approval must identify the approver, decision object, evidence seen, allowed outcomes, and audit record.

（Agent 可以建议，但不得静默批准、不得认证事实真伪、不得授予访问权限、不得压下异常。人工审批必须写明审批人、决策对象、所看证据、允许的结论以及审计记录。）

## 异常与升级

异常被当作有归属的路径来审查，而不是意外。每个关键节点都带 `failure`、`recovery`、`timeout`、`escalation`，审查测试覆盖重复、超时、重试、陈旧状态、并发、异常归属与升级时限（`references/process-rubric.md:32-33`）。异常与升级路径是必需的输出要素（`SKILL.md:35`）。

升级最终必须落到人。停止规则（`SKILL.md:39-41`）：

> Stop when authority, sensitive-data use, approval ownership, or an irreversible decision is unresolved. Escalate policy choices and business truth to the named human owner. Never allow an Agent to make a consequential decision merely because the evidence is inconvenient to obtain.

（当权限、敏感数据使用、审批归属或不可回退的决策尚未解决时，必须停止。政策选择与业务事实要升级给具名的人类负责人。绝不能因为取证不方便，就让 Agent 做出后果重大的决策。）

严重级别是四档已定义词汇（`references/process-rubric.md:39-42`）：`blocker`（不安全的权限、缺失的重大决策负责人、编造的事实，或不可恢复的关键失败）、`high`（关键节点无法可靠完成、交接或恢复）、`medium`（流程只能靠未文档化的个人经验运转，或造成显著延迟/错误风险）、`low`（影响有限的局部清晰度或效率问题）。

## 输出

审查返回十一个要素（`SKILL.md:35`）：范围与证据台账；actor 责任地图；节点表；规则/Agent/人工决策划分；授权与数据控制；异常与升级路径；矛盾点；最小可行流程；发现项；待决事项；下游交接。

每条发现项必须包含十个字段（`SKILL.md:37`）：`id`、`severity`、`evidence_status`、`source`、`node`、`failure_mode`、`business_impact`、`required_change`、`owner`、`verification`。每个关键节点都必须回答：谁行动、什么授权或支撑该行动、交接了什么、失败时会发生什么。

仓库提供的是**声明式**契约，而不是工具链：没有模板、没有 schema 文件、没有示例输出，也没有任何 runner 去解析产出的审查报告。输出契约是散文。详见[验证证据](#验证证据)。

## 验证证据

| 项目 | 状态 | 依据 |
| --- | --- | --- |
| 版本 `0.2.0` 一致性 | `VERIFIED` | `SKILL.md:6`、`manifest.json`、`reports/skill-ir.json:6`、[`tests/test_contract.py`](tests/test_contract.py)、git tag `v0.2.0` = HEAD `39851b9`、GitHub release `v0.2.0`。无版本不一致。 |
| 包契约测试 | 运行结果 `VERIFIED` | `python3 -m unittest discover -s tests -v` → `Ran 3 tests in 0.001s` / `OK`。 |
| 测试是否验证审查行为 | **否** | 3 个测试共断言 2 次文件存在、9 次 Markdown 标题子串、1 次旧名称反向守卫、3 个 fixture 数量阈值。它们不执行 Skill、不调用模型、不解析任何输出。 |
| 触发 fixture | 作为录制 fixture 为 `VERIFIED` | [`evals/trigger_cases.json`](evals/trigger_cases.json) 含 11 条手写中文用例（5 条 `should_trigger`、3 条 `should_not_trigger`、3 条 `near_neighbor`）；[`reports/trigger-eval.json`](reports/trigger-eval.json) 可逐字节复现，结果为 11/11（`pass_rate` 1.0）。 |
| 触发评估方法 | 关键词打分 | 打分器从 SKILL description 中抽取概念关键词，与每条 fixture 文本做子串匹配，阈值 `0.3`。**没有调用任何模型，也没有测试任何路由。** runner 位于另一个仓库（`kang-meta-skill`），本仓库不包含它，因此该报告无法仅凭本仓库重新生成。 |
| 输出契约评估 | `TO_VERIFY` — missing evidence | [`evals/output_cases.json`](evals/output_cases.json) 只含一条用例，断言是英文散文，且**没有任何 runner**。`manifest.json` 声明了一个名为 "output contract evaluation" 的发布门，但没有对应脚本或报告。`reports/output-evidence.json` **不存在**，作者自己的 release check 也标记 `provider_or_human_output_evidence: missing_evidence: true`。 |
| 声明的测试 runner | 未附带 | 审计机器上没有任何 `python3` 安装 `pytest`，仓库也没有任何依赖文件声明它。该测试套件只能在标准库 `unittest` 下运行。 |
| 持续集成 | **无** | 没有 `.github/`、没有 workflow 文件、没有 `Makefile`、没有 `tox.ini`。这 3 个测试只有在人工输入命令时才会运行。 |
| 包校验脚本 | `HISTORICAL` — 作者环境 | `quick_validate.py` 与 `validate_skill.py` 针对本地检出运行可以通过，但两者都位于另一个仓库 `kang-meta-skill`，不属于本包。 |
| 密钥与客户数据 | `VERIFIED` 干净 | 无凭据、无 `.env`、无邮箱地址。对全部 10 个 commit、全部 blob 做全历史扫描，公司或客户标识零命中。`CRM` 只出现在一条合成 fixture 字符串中。仓库不含客户数据，也不含访谈逐字稿。 |
| GitHub release 说明 | 部分无支撑 | `v0.2.0` 的 release 正文声称有 "output schemas" 与 "cross-domain evaluations"。本仓库没有任何文件包含 schema，唯一有报告的评价是关键词触发打分。不要依赖这两个说法；该 release 没有附带任何 asset。 |

<details>
<summary>本 README 上一版中被移除的说法</summary>

| 已移除 | 原因 |
| --- | --- |
| `status: public release` 徽章 | 与 `manifest.json`（`public-release-candidate`）及 `reports/creation-handoff.md`（"pending release evidence"）矛盾。状态措辞现已对齐仓库自身声明的候选状态。 |
| 静态 `contract tests-3` 徽章 | 手写计数会过期，而且放在发布徽章旁边会暗示测试提供了它并未提供的保证。已替换为 release、license 与 last-commit 徽章。 |
| 把「证据门 / Evidence Gate」当作已定义机制 | 该词出现在上一版 README 和 `agents/interface.yaml`，但在 `SKILL.md` 与评分细则中都没有定义。真实存在的是证据标签与就绪判定（readiness gate）。 |
| 「数字员工」框架 | `SKILL.md` 中不存在该说法，它定义的是一个只读的流程保障角色。该框架属于另一个项目（`kang-agent-workforce`），不适用于本项目。 |
| 验证命令中的作者本机绝对路径 | 它们泄露了私有目录结构，对任何其他读者都不可用。已替换为真正可运行的仓库内相对命令。 |

</details>

## 示例

本仓库不附带任何示例输出——没有 `examples/`、`templates/` 或 `schemas/` 目录。唯一的输出 fixture（[`evals/output_cases.json`](evals/output_cases.json)）是一条关于采购审批流程的合成 prompt，断言为英文散文，并不是参考答案。

下表用于说明 14 字段节点合同，是为本 README 编写的。它不是仓库附带的产物，也没有任何本仓库工具生成或校验过它。

| 字段 | 某个节点的示例内容 |
| --- | --- |
| `actor` | 采购申请人 |
| `trigger` | 提交超过常设阈值的采购申请 |
| `input` | 申请单、预算行、供应商信息、既有报价 |
| `rule` | 阈值政策与品类准入规则 |
| `action` | 将申请路由给具名的预算负责人审批 |
| `output` | 带条件的审批结论 |
| `receiver` | 申请人与采购运营队列 |
| `evidence` | 政策版本、申请单、报价比较 |
| `authorization` | 预算负责人持有该成本中心的授权；申请人不能审批自己的申请 |
| `failure` | 审批人不可用，或审批过程中预算行已用尽 |
| `recovery` | 以有记录的委托进行代审批，或将申请退回修改 |
| `timeout` | 超过决策时限后申请自动升级 |
| `escalation` | 有争议或属于政策例外的情形，升级给政策中具名的财务负责人 |
| `audit_record` | 申请单号、审批人身份、结论、所看证据、时间戳 |

该类情形在 fixture 中的断言只覆盖了这 14 个字段中的 8 个，这正是[节点合同](#节点合同node-contract)一节所述的不一致。

## 适用 / 不适用

**适用场景**

- 在实施之前审查一份拟定的业务或运营流程。
- 检查 actors、触发条件、输入、规则、证据、交接、异常、授权、升级和人工决策门是否真的被定义。
- 定位自我审批、缺失负责人、编造证据、静默失败与不可回退的自动化。
- 判断 AI 可以在哪些环节参与，以及哪里必须由人工决策门阻断。

**不适用场景**（`SKILL.md:3`）

- 产品导航设计或视觉体验批评。
- 流程或产品的实现。
- 对员工做原始访谈。
- 把演示数据、模型输出或上游报告当作已确认的业务事实（`SKILL.md:11`）。

如果流程目标或流程边界缺失，审查会停止，而不是在假设上继续推进（`SKILL.md:17`）。

## 安全与人工边界

- 角色是**只读**的：只写被指派的流程产物，不修改产品（`SKILL.md:45`）。`manifest.json` 声明 `"permissions":{"default":"read-only","implementation":"requires human approval"}`。
- 当权限、敏感数据使用、审批归属或不可回退的决策未解决时，审查会**停止**，并升级给具名的人类负责人（`SKILL.md:41`）。
- Agent 可以建议，但不得静默批准、认证事实真伪、授予访问权限或压下异常（`references/process-rubric.md:23`）。
- 未解决的 blocker、敏感数据授权问题或人工决策门会阻止就绪判定（`references/process-rubric.md:46`）。

**这些是给模型的指令，不是被强制执行的管控。** 本仓库中没有任何东西——没有 linter、没有输出校验器、没有 schema、没有测试——能阻止模型忽略停止与升级规则。所声明的只读权限在这里也没有任何实现或审计机制。

## 快速开始

前置条件（`SKILL.md:15`、`:17`）：流程目标、范围、已知 actors、触发条件与预期结果、权威政策或约束、可用证据。架构、当前工作流、系统日志、表单、访谈与异常记录属于支撑输入。

```bash
git clone https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer.git
cd kang-enterprise-process-reviewer
python3 -m unittest discover -s tests -v
```

最后一条命令就是[验证证据](#验证证据)中所述的包契约检查——它验证的是包身份与标题是否存在，而不是审查行为。

入口是 [`SKILL.md`](SKILL.md)（`manifest.json` 中 `"entrypoint":"SKILL.md"`），调用方式为 `$kang-enterprise-process-reviewer`（`SKILL.md:45`）。请与 [`references/process-rubric.md`](references/process-rubric.md) 一起阅读，后者承载了证据标签、节点合同、决策边界、审查测试、严重级别与就绪判定；评分细则由 `SKILL.md:26` 链接，是本包中唯一的参考文档。

<details>
<summary>安装与适配器状态——部分未验证</summary>

- `npx skills add KanG-ciyuan/kang-enterprise-process-reviewer` 曾出现在上一版 README 中。npm 包 `skills` 确实存在（描述为 `The open agent skills ecosystem`），因此 CLI 是真实的，但本仓库的端到端安装**未经验证**——审计环境没有通往 `github.com` 的网络路径。请把该命令视为 `TO_VERIFY`。
- `manifest.json` 声明 `"target_platforms":["codex","agent-skills-compatible"]`，而 `agents/interface.yaml` 声明 `adapter_targets: [openai, claude, generic, agent-skills-compatible]`。两处声明不一致，且只有 [`agents/openai.yaml`](agents/openai.yaml) 存在——`claude` 与 `generic` 只有声明、没有适配器产物。不要假定这些适配器可用。
- 非 Codex 场景的安装目录结构没有任何文档说明。

</details>

---

## 属于 Kang 开源 AI 体系

本项目是「面向企业 AI 转型、Agent 协作与 AI 原生产品交付的证据驱动体系」的一部分。

| 阶段 | 项目 | 作用 |
| --- | --- | --- |
| DISCOVER 发现 | [enterprise-ai-diagnostic-skills](https://github.com/KanG-ciyuan/enterprise-ai-diagnostic-skills) | 在自动化之前，先弄清企业真实业务如何运行 |
| DEFINE 定义 | [kang-product-architect](https://github.com/KanG-ciyuan/kang-product-architect) | 把模糊需求转化为可实施、可审查的产品契约 |
| DEFINE 定义 | [kang-enterprise-process-reviewer](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer) | 审查流程是否可执行、可追责、可恢复 |
| BUILD & COORDINATE 构建与协同 | [kang-agent-workforce](https://github.com/KanG-ciyuan/kang-agent-workforce) | 角色化的 Agent 数字员工团队与显式交接 |
| BUILD & COORDINATE 构建与协同 | [kang-agent-collab](https://github.com/KanG-ciyuan/kang-agent-collab) | Agent 协作与交接协议 |
| BUILD & COORDINATE 构建与协同 | [kang-frontend-standard](https://github.com/KanG-ciyuan/kang-frontend-standard) | AI 构建界面的前端质量标准 |
| VERIFY 验证 | [kang-b2b-ux-auditor](https://github.com/KanG-ciyuan/kang-b2b-ux-auditor) | 用户能否真正把工作做完 |
| VERIFY 验证 | [kang-product-acceptance-auditor](https://github.com/KanG-ciyuan/kang-product-acceptance-auditor) | AI 构建产品的独立验收 |
| DELIVER 交付 | [kang-github-readme](https://github.com/KanG-ciyuan/kang-github-readme) | 证据感知的 README 工程 |
| DELIVER 交付 | [kang-ppt-skill](https://github.com/KanG-ciyuan/kang-ppt-skill) | 证据感知的演示文稿设计 |

**横向基础设施：** [kang-meta-skill](https://github.com/KanG-ciyuan/kang-meta-skill) —
Skill 工程化、评估与发布治理。

**早期工作：** [ai-agent-rules](https://github.com/KanG-ciyuan/ai-agent-rules)、
[workflow-five-steps](https://github.com/KanG-ciyuan/workflow-five-steps)、
[renovation-agent](https://github.com/KanG-ciyuan/renovation-agent)。

```text
发现 DISCOVER
企业 AI 诊断 Skills
        ↓
定义 DEFINE
Kang Product Architect
Kang Enterprise Process Reviewer
        ↓
构建与协同 BUILD & COORDINATE
Kang Agent Workforce
Kang Agent Collab
Kang Frontend Standard
        ↓
验证 VERIFY
Kang B2B UX Auditor
Kang Product Acceptance Auditor
        ↓
交付 DELIVER
Kang GitHub README
Kang PPT Skill
```

> 这是一张生态地图，不是严格的运行时流水线。各阶段描述的是项目所处的工作位置，
> 而不是强制的执行顺序。

## 开源许可证

本项目采用 [MIT License](LICENSE) 开源。

<!-- kang-author:start -->
## About Kang

Maintained by Kang. GitHub: https://github.com/KanG-ciyuan/
<!-- kang-author:end -->
