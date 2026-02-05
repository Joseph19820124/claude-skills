---
name: query-bedrock-bill
description: 查询 AWS Bedrock Claude 模型的调用量和费用
allowed-tools: Bash(aws *)
---

使用 AWS CLI 查询 Bedrock 的 Claude 模型使用情况和费用。

## 配置

- AWS Profile: `uk-2`
- Region: `eu-west-2`
- 模型:
  - Claude Opus 4.5: `eu.anthropic.claude-opus-4-5-20251101-v1:0`
  - Claude Haiku 4.5: `eu.anthropic.claude-haiku-4-5-20251001-v1:0`

## 定价参考（eu-west-2，按 1M tokens 计）

| 模型 | 输入价格 | 输出价格 |
|------|----------|----------|
| Claude Opus 4.5 | $15 | $75 |
| Claude Haiku 4.5 | $0.80 | $4 |

## 执行步骤

1. 查询 Claude Opus 4.5 的调用次数和 Token 使用量（过去 30 天）：

```bash
# 调用次数
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name Invocations --dimensions Name=ModelId,Value=eu.anthropic.claude-opus-4-5-20251101-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum

# 输入 Token
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name InputTokenCount --dimensions Name=ModelId,Value=eu.anthropic.claude-opus-4-5-20251101-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum

# 输出 Token
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name OutputTokenCount --dimensions Name=ModelId,Value=eu.anthropic.claude-opus-4-5-20251101-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum
```

2. 查询 Claude Haiku 4.5 的使用情况：

```bash
# 调用次数
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name Invocations --dimensions Name=ModelId,Value=eu.anthropic.claude-haiku-4-5-20251001-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum

# 输入 Token
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name InputTokenCount --dimensions Name=ModelId,Value=eu.anthropic.claude-haiku-4-5-20251001-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum

# 输出 Token
aws cloudwatch get-metric-statistics --profile uk-2 --region eu-west-2 --namespace AWS/Bedrock --metric-name OutputTokenCount --dimensions Name=ModelId,Value=eu.anthropic.claude-haiku-4-5-20251001-v1:0 --start-time $(date -u -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) --period 86400 --statistics Sum
```

3. 使用 Cost Explorer 查询 Bedrock 费用（作为参考）：

```bash
aws ce get-cost-and-usage --profile uk-2 --region eu-west-2 --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) --granularity MONTHLY --metrics "UnblendedCost" --filter '{"Dimensions": {"Key": "SERVICE", "Values": ["Amazon Bedrock"]}}'
```

4. 汇总结果并计算费用估算：

按以下格式输出表格：

### 使用量汇总表

| 模型 | 调用次数 | 输入 Tokens | 输出 Tokens |
|------|---------|-------------|-------------|
| Opus 4.5 | X | X | X |
| Haiku 4.5 | X | X | X |

### 费用估算

根据 Token 使用量计算预估费用：

**Opus 4.5:**
- 输入费用 = 输入Tokens / 1,000,000 × $15
- 输出费用 = 输出Tokens / 1,000,000 × $75
- 小计 = 输入费用 + 输出费用

**Haiku 4.5:**
- 输入费用 = 输入Tokens / 1,000,000 × $0.80
- 输出费用 = 输出Tokens / 1,000,000 × $4
- 小计 = 输入费用 + 输出费用

**总预估费用 = Opus 小计 + Haiku 小计**

### 按天分布趋势

列出每天的使用情况，便于观察使用趋势。

### 注意事项

- Cost Explorer 费用可能有 24-48 小时延迟
- 预估费用基于标准按需定价，实际账单可能因折扣、免费额度等因素不同
- Cache tokens（如有）可享受折扣价格
