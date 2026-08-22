# kang-enterprise-process-reviewer

[![status](https://img.shields.io/badge/status-public%20release-2ea44f)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![version](https://img.shields.io/github/v/release/KanG-ciyuan/kang-enterprise-process-reviewer?label=version)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![tests](https://img.shields.io/badge/contract%20tests-3-2ea44f)](tests/)
[![license](https://img.shields.io/badge/license-MIT-6f42c1)](LICENSE)

Kang 的通用业务流程审查数字员工 Skill。适用于 SaaS、内部工具、服务运营和 AI 辅助流程，检查责任、证据、交接、异常、授权和人工决策门。

调用：`$kang-enterprise-process-reviewer`

输入：流程目标、范围、角色、触发条件、约束和可用证据。输出：节点合同、规则/Agent/人工边界、异常路径、证据门和最小可行流程。它不写前端，不把访谈或模型输出冒充业务事实。

## 你可以直接这样说

“使用 `$kang-enterprise-process-reviewer` 检查采购审批的责任、证据、驳回、超时和升级路径。”

## 安装与验证

```bash
npx skills add KanG-ciyuan/kang-enterprise-process-reviewer
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/kang-enterprise-process-reviewer
python3 ~/.codex/skills/kang-meta-skill/scripts/validate_skill.py ~/.codex/skills/kang-enterprise-process-reviewer
```

## 前置条件

- [ ] 已准备流程目标、范围、角色、约束和可用证据
- [ ] 已确认本轮只读
- [ ] 已确认业务结论仍需人工确认

## Troubleshooting

如果材料不足，执行有限审查并标记 `to_verify`；如果授权、敏感数据或关键决策人不明确则停止推进，不把预置数据当成真实流程。

## License

MIT. See [LICENSE](LICENSE). This is a reusable product-development agent Skill, separate from any private enterprise product.

<!-- kang-author:start -->
## About Kang

Maintained by Kang. GitHub: https://github.com/KanG-ciyuan/

<!-- kang-author:end -->
