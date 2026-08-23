# Phase 1: DimASR (VA 回归) 实验报告

> SemEval 2026 Task 3 - Subtask 1: Dimensional Aspect-Based Sentiment Analysis
>
> **任务**: 给定文本 + Aspect，预测 VA (Valence-Arousal) 分数
>
> **数据集**: zho_restaurant（繁体中文餐厅评论）

---

## 一、任务描述

### 1.1 DimASR 任务

**输入**: 文本 + 方面词 (Aspect)

**输出**: VA 分数 (Valence, Arousal)

- **Valence (效价)**: 情感的正负倾向，范围 1-9
- **Arousal (唤醒度)**: 情感的强烈程度，范围 1-9

### 1.2 数据样例

```
文本: "經典黃悶魷魚好吃！酸湯的也很不錯"
方面词: "黃悶魷魚"
VA分数: 6.75#5.75 (Valence=6.75, Arousal=5.75)
```

## 二、数据集

### 2.1 数据统计

| 数据集 | 样本数 | 描述 |
|--------|--------|------|
| 训练集 | 6,050 条原始样本 / 8,523 个展开样本 | 使用 `Quadruplet` 字段 |
| 验证集 | 300 条原始样本 / 685 个展开样本 | 使用 `Aspect_VA` 字段 |

### 2.2 VA 分布

| 指标 | 最小值 | 最大值 | 均值 | 标准差 |
|------|--------|--------|------|--------|
| Valence | 1.50 | 8.25 | 5.81 | 0.99 |
| Arousal | 4.00 | 8.25 | 5.76 | 0.65 |

### 2.3 数据特点

- 繁体中文餐厅评论
- 每条样本可能包含多个方面词
- VA 分数连续值，范围 1-9

## 三、实验分组

### 3.1 分组概览

| 实验组 | 方法 | 基座模型 | 可训练参数 |
|--------|------|----------|-----------|
| **Group A** | 全参数微调 | RoBERTa-wwm-ext | 100% | 
| **Group B** | PEFT (LoRA/P-Tuning) | RoBERTa-wwm-ext | ~0.1-0.3% |
| **Group C** | LLM SFT | Qwen3 | ~0.1% | 

### 3.2 Group A: 全参数微调

| 实验ID | 模型 | 方法 | 参数量 |
|--------|------|------|--------|
| Exp-A1 | Chinese-RoBERTa | RoBERTa-base | 102M |
| Exp-A2 | Chinese-RoBERTa | RoBERTa-base | 102M |
| Exp-A3 | Chinese-RoBERTa | RoBERTa+CNN | 104M |
| Exp-A4 | Chinese-RoBERTa | RoBERTa+LSTM | 104M |

### 3.3 Group B: 参数高效微调 (PEFT)

| 实验ID | 模型 | PEFT方法 | 可训练参数 |
|--------|------|----------|-----------|
| Exp-B1 | Chinese-RoBERTa | LoRA | ~0.1% |
| Exp-B2 | Chinese-RoBERTa | P-Tuning | ~0.2% |
| Exp-B3 | Chinese-RoBERTa | LoRA+CNN | ~0.3% |
| Exp-B4 | Chinese-RoBERTa | LoRA+LSTM | ~0.3% |

### 3.4 Group C: LLM SFT

| 实验ID | 模型 | 方法 | 参数量 |
|--------|------|------|--------|
| Exp-C1 | Qwen3-0.6B | SFT+LoRA | 0.6B |
| Exp-C2 | Qwen3-1.7B | SFT+LoRA | 1.7B |
| Exp-C3 | Qwen3-0.6B | 回归+LoRA | 0.6B |
| Exp-C4 | Qwen3-1.7B | 回归+LoRA | 1.7B |



## 四、实验结果

### 4.1 Group A 结果

| 实验 | 方法 | RMSE | 参数量(M) |
|------|------|------|-----------|
| Exp-A1 | RoBERTa-base | ~3.66 | 102.27 |
| Exp-A2 | RoBERTa-base | **~0.54** | 102.27 |
| Exp-A3 | RoBERTa+CNN | ~3.67 | 104.63 |
| Exp-A4 | RoBERTa+LSTM | ~4.99 | 104.37 |

### 4.2 Group B 结果 (PEFT)

| 实验 | 方法 | RMSE | 可训练参数 | 总参数(M) |
|------|------|------|-----------|-----------|
| Exp-B1 | LoRA | 4.9963 | 0.29% | 102.56 |
| Exp-B2 | P-Tuning | **0.7099** | 1.71% | 104.05 |
| Exp-B3 | LoRA+CNN | 4.9963 | 2.53% | 104.93 |
| Exp-B4 | LoRA+LSTM | 4.9963 | 2.29% | 104.67 |

