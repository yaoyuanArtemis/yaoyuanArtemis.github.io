---
title: AlphaNestAgent
description: 面向美股机会发现的智能投研 Agent
toc: true
authors:
  - example-author
tags:
  - AI Agent
  - LLM
  - DeepSeek
  - Quant
  - Python
categories:
  - AI Agent
  - 投研工具
  - Python
date: '2026-06-16T21:30:00+08:00'
lastmod: '2026-06-17T00:00:00+08:00'
draft: false
---

## 项目简介

[AlphaNestAgent](https://github.com/yaoyuanArtemis/AlphaNestAgent) 是一个面向美股市场的智能投研 Agent。项目目标是把大模型对话、市场事件跟踪、股票筛选和量化分析能力组合起来，帮助用户更高效地发现值得进一步研究的市场机会。

当前版本更像是 Agent 的基础骨架：已经完成了基于 `pydantic-ai` 的对话入口、DeepSeek 模型接入、环境变量读取、会话历史维护和模型调用异常处理。后续可以在这个基础上继续接入行情、财报、宏观日历、新闻事件和量化回测工具。

## 项目链接

| 项目 | 内容 |
| --- | --- |
| GitHub | [yaoyuanArtemis/AlphaNestAgent](https://github.com/yaoyuanArtemis/AlphaNestAgent) |
| 当前状态 | Prototype |
| 主要语言 | Python |
| 核心依赖 | `pydantic-ai`、`python-dotenv` |
| 模型 Provider | DeepSeek |

## 项目定位

AlphaNestAgent 关注的是“投研辅助”，不是自动交易系统。它更适合承担以下工作：

- 根据用户偏好生成候选股票研究列表
- 跟踪未来三个月可能影响美股市场的重要事件
- 围绕财报、FOMC、非农、CPI、PPI 等催化因素整理风险窗口
- 结合历史价格、技术指标和策略规则做初步量化分析
- 把分散的投研步骤收敛到一个可对话、可扩展的 Agent 工作流中

## 当前实现

项目目前的核心文件比较清晰：

- [`agentMain.py`](https://github.com/yaoyuanArtemis/AlphaNestAgent/blob/main/agentMain.py)：Agent 主入口，负责加载环境变量、初始化模型、维护多轮对话历史和处理 API 调用异常
- [`tools.py`](https://github.com/yaoyuanArtemis/AlphaNestAgent/blob/main/tools.py)：预留工具模块，后续可以放行情查询、事件日历、指标计算、回测等 Agent 工具
- [`pyproject.toml`](https://github.com/yaoyuanArtemis/AlphaNestAgent/blob/main/pyproject.toml)：Python 项目配置，声明包名、Python 版本和依赖

当前模型接入方式是：

```python
model = OpenAIChatModel(
    "deepseek-chat",
    provider=DeepSeekProvider(api_key=os.environ.get("DEEPSEEK_API_KEY")),
)

agent = Agent(model)
```

这意味着项目并没有直接绑定 OpenAI 官方模型，而是通过 `pydantic-ai` 的 OpenAI Chat Model 抽象接入 DeepSeek Provider。这样后续替换模型或扩展 Provider 会更方便。

多轮对话的部分保留了历史消息：

```python
resp = agent.run_sync(user_input, message_history=history)
history = list(resp.all_messages)
```

这部分虽然很小，但它是 Agent 从“单轮问答”走向“连续投研会话”的基础。

## 技术栈

| 模块 | 说明 |
| --- | --- |
| Python 3.11+ | 项目运行环境 |
| pydantic-ai | Agent 框架与模型调用抽象 |
| DeepSeek | 当前使用的对话模型 Provider |
| python-dotenv | 从 `.env` 读取 `DEEPSEEK_API_KEY` |

## 运行方式

项目需要先准备 DeepSeek API Key，并写入 `.env`：

```bash
DEEPSEEK_API_KEY=your_api_key
```

然后安装依赖并运行主程序：

```bash
pip install -e .
python agentMain.py
```

运行后可以在命令行中连续输入问题，程序会把历史消息传给下一轮模型调用，从而支持基础的多轮对话。

示例输入：

```text
未来三个月有哪些可能影响美股科技股的关键事件？
```

预期输出不是直接给出交易建议，而是整理出值得继续研究的事件、风险窗口和候选方向。

## 异常处理

`agentMain.py` 对常见模型调用错误做了分类处理，包括：

- `ModelHTTPError`
- `ModelAPIError`
- `UnexpectedModelBehavior`
- `APIStatusError`
- `APIConnectionError`
- `OpenAIError`

这部分对 Agent 项目很重要。投研类 Agent 后续会接入更多外部数据源，如果没有清晰的错误输出，调试 API Key、模型响应、网络请求和 Provider 状态会比较痛苦。

## 项目价值

这个项目最有价值的地方不是当前已经实现了多少工具，而是它把投研 Agent 的边界先定清楚了：

- 不直接输出买卖建议，而是辅助研究
- 不把所有逻辑写死在 prompt 中，而是预留工具层
- 不只追求一次性回答，而是保留多轮上下文
- 不忽略失败场景，而是把模型调用错误显式打印出来

这些基础结构会决定后续接入真实金融数据时，项目是否还能保持可维护。

## 后续规划

这个项目后续可以沿着三条线扩展：

1. 工具层：在 `tools.py` 中接入行情、财报、宏观日历、新闻和技术指标计算
2. 工作流层：把“市场事件发现 -> 股票池筛选 -> 风险评估 -> 研究摘要”整理成可复用流程
3. 评估层：为推荐结果加入数据来源、置信度、风险提示和回测验证

比较自然的下一步是先实现几个稳定工具，例如：

- 查询股票历史价格
- 获取最近财报日期
- 获取未来宏观事件日历
- 计算简单技术指标
- 输出结构化投研摘要

## 风险提示

AlphaNestAgent 的输出只能作为投研参考，不构成投资建议。股票市场存在不确定性，任何投资决策都应该结合个人风险承受能力、资金安排和独立判断。
