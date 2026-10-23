# DecideKit

**让你正在使用的大模型，进入结构化决策模式。**

一个纯 Markdown 的 Agent Skill：提供状态与问题，得到选择、评分或是非判断。灵感来自 [Jev](https://docs.typesafe.ai/introduction) 的决策接口。

无需独立服务、额外 API Key 或运行脚本。推理仍由你的宿主模型完成，照常消耗宿主模型的额度。

## 安装

支持 Skills CLI 的环境可使用：

```bash
npx skills add daijizai/decide-kit --skill decide-kit
```

也可下载本仓库，将包含 SKILL.md 的 `skills/decide-kit/` 文件夹复制到你的 Agent 的技能目录。例如 Codex 使用 `~/.codex/skills/decide-kit/`。安装后新开会话，明确请求“使用 decide-kit”。

## 使用

发送“使用 decide-kit 判断以下输入”，附上：

```json
{
  "state": "机票已买好，想找西湖附近的酒店。",
  "questions": {
    "intent": {
      "type": "choice",
      "instructions": "当前请求属于哪类服务？",
      "criteria": {
        "flight": "机票搜索",
        "hotel": "住宿搜索",
        "other": "其他明确的服务需求"
      }
    }
  }
}
```

预期输出示例（不是实测日志）：

```json
{
  "protocol": "decide-kit/1",
  "answers": {
    "intent": {
      "type": "choice",
      "status": "answered",
      "choice": "hotel",
      "evidence": ["想找西湖附近的酒店"],
      "missing_information": []
    }
  },
  "errors": {}
}
```

## 三种判断

| 类型 | 你提供 | 返回 |
| --- | --- | --- |
| Choice | 问题 + 选项 ID 与描述 | 一个选项 ID |
| Score | 问题 + 从低到高的等级描述数组 | 等级的整数索引，从 0 开始 |
| Noul | 一个是非命题，可附判定标准 | true / false |

信息不足、无法区分选项时返回 `status: "abstained"`，值为 `null`，并列出缺失信息。输入不合法时返回 `errors`。同一请求可包含多个问题。

适合工具路由、工单分类、按明确标准评估内容、判断是否需要补充信息。选出一个动作不会执行该动作。

## 和 Jev 的关系

DecideKit 是独立的提示词协议，与 TypeSafe AI 无关联，**不兼容 Jev API，也没有复现 Jev 模型**。

- 使用当前宿主模型，不改变模型架构、速度或成本。
- 不输出未经校准的概率或置信度。
- Noul 返回布尔值或 null；Score 返回整数等级索引。这与 Jev 的输出语义不同。
- 纯 Skill 无法强制 JSON 格式，也不能保证跨模型一致或每次判断正确。
- 仅根据提供的 state 判断；需要最新事实时，请先获取资料再提供给它。

完整规则：[SKILL.md](skills/decide-kit/SKILL.md)。手工验收案例：[examples/acceptance.md](examples/acceptance.md)。

## 贡献

欢迎提交脱敏的失败案例：输入、宿主模型、实际输出、期望行为与理由。避免只凭一个示例宣称准确率提升。

## License

MIT
