# kang-enterprise-process-reviewer

[![status](https://img.shields.io/badge/status-public%20release-2ea44f)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![version](https://img.shields.io/github/v/release/KanG-ciyuan/kang-enterprise-process-reviewer?label=version)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![tests](https://img.shields.io/badge/local%20tests-1%20passed-2ea44f)](tests/)
[![license](https://img.shields.io/badge/license-MIT-6f42c1)](LICENSE)

Kang 的企业流程审查 Skill。用于检查角色责任、证据边界、交接条件、失败路径和人工决策门，区别于产品内置的员工摸排 Skill。

调用：`$kang-enterprise-process-reviewer`

输入：已批准的架构交接、业务背景、流程材料和当前产品。输出：可追溯的流程审查与最小首发流程。它不替员工 Agent 访谈，不写前端。

## 你可以直接这样说

“使用 `$kang-enterprise-process-reviewer` 检查员工提交到负责人决策的流程交接和证据门。”

## 安装与验证

```bash
npx skills add KanG-ciyuan/kang-enterprise-process-reviewer
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/kang-enterprise-process-reviewer
python3 ~/.codex/skills/kang-meta-skill/scripts/validate_skill.py ~/.codex/skills/kang-enterprise-process-reviewer
```

## 前置条件

- [ ] 已准备架构交接、业务背景和流程证据
- [ ] 已确认本轮只读
- [ ] 已确认业务结论仍需人工确认

## Troubleshooting

如果输入材料不足，标记 `to_verify` 并停止推进，不要把预置演示数据当成真实流程。

## License

MIT. See [LICENSE](LICENSE). This is a reusable product-development agent Skill, separate from any private enterprise product.

<!-- kang-author:start -->
## About Kang

Maintained by Kang. GitHub: https://github.com/KanG-ciyuan/

<!-- kang-author:end -->
