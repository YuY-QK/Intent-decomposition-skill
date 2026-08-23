[English](README.md) | **简体中文**

# 意图拆解与优化（Intent Decomposition & Optimization）

一个 Claude Code 技能（skill）：在执行请求之前先暂停下来做判断——这个请求适合用普通对话完成，还是需要一个智能体（agent）/ 多步骤工作流？如果判断需要智能体，它还会去查找匹配的技能（先查 Claude 自身的技能目录，再搜索 GitHub），而不是让你从零开始搭建。

它是**仅在显式调用时触发**的：不会拦截每一条消息，只在类似"这应该是一个 agent 任务吗"、"先分析一下再执行"、"/plan"、"先评估一下范围"这样的请求下触发。

## 安装（Claude Code）

```
/plugin marketplace add YuY-QK/Intent-decomposition-skill
/plugin install intent-decomposition@intent-decomposition-marketplace
```

安装后，在任意对话中用类似这样的话触发它：

> "开始之前——这应该用普通对话完成，还是需要一个 agent？"

## 仓库结构

```
.
├── .claude-plugin/
│   └── marketplace.json          # 市场目录（列出下面的插件）
└── plugins/
    └── intent-decomposition/
        ├── .claude-plugin/
        │   └── plugin.json       # 插件清单
        └── skills/
            └── intent-decomposition/
                └── SKILL.md       # 技能本体
```

## 发布前检查清单

1. 推送前先在本地验证：在本目录下运行 `claude plugin validate .`。
2. （可选，但推荐）先从本地文件夹安装 —— `/plugin marketplace add ./gh-repo`，然后 `/plugin install intent-decomposition@intent-decomposition-marketplace` —— 在公开之前先实际试用一下这个技能。
3. 将这些文件推送到 [YuY-QK/Intent-decomposition-skill](https://github.com/YuY-QK/Intent-decomposition-skill) 的仓库根目录（`.claude-plugin/` 文件夹必须位于仓库根目录，不能嵌套在子目录中）。
4. 如果想要固定版本，打一个 release 标签；否则市场会默认跟踪 `main` 分支。

## 更新

每次发布时，请同时更新 `plugin.json` 和 `marketplace.json` 中对应条目的 `version` 字段——只有这个字符串变化了，用户才会收到更新。

## 许可证

MIT —— 详见 plugin.json。如果你想使用其他许可证，请自行修改。
