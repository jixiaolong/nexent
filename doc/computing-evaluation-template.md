# 算力评估模板（华为昇腾）

> 基于华为昇腾 NPU 的算力评估，覆盖各业务场景模型部署需求。

## 算力评估表

| 场景 | 模型 | 类型 | 显存 | 昇腾算力 (TFLOPS FP16) | 性能指标 | 并发 | 昇腾选型 | 备注 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| **智能体** | Qwen 32B 微调 | LLM | ≥196G | ≥1200 | 首 token ≤2s / 生成 ≥20 tokens/s | 5~10 | Atlas 800T A2 × 1 (4×910B) | IFFT 微调 · TP=4 |
| | Qwen3 72B 微调 | LLM | ≥384G | ≥1920 | 首 token ≤3s / 生成 ≥15 tokens/s | 3~5 | Atlas 800T A2 × 1 (8×910B) | IFFT 微调 · TP=8 · BF16 · 长上下文 KV Cache 额外 100~200G |
| | Qwen2.5-VL-72B | VLM | ≥256G | ≥1280 | 首 token ≤3s / 生成 ≥15 tokens/s · 图像预处理 ≤1s | 3~5 | Atlas 800T A2 × 1 (4×910B) | TP=4 · BF16 · 含视觉编码器 ~675M · Qwen3-VL 无 72B 版本 |
| | Qwen3-Embedding-8B | Embedding | ≥48G | ≥200 | 延迟 ≤100ms / 吞吐 ≥50 qps | 20 | 合入同节点 (1×910B) | |
| | Qwen3-Reranker-0.6B | Rerank | ≥5G | ≥50 | 延迟 ≤50ms | 50 | 合入同节点 (1×910B) | |
| | PaddleOCR VL 1.6 | OCR | ≥8G | ≥30 | 延迟 ≤500ms / 吞吐 ≥5 img/s | 5 | Atlas 300I Pro (310P) | |
| **舌诊** | EfficientNet / YOLO / U-Net | 对象识别·分割·分类 | ≥1G | ≥10 | 延迟 ≤200ms / 吞吐 ≥10 img/s | 10 | Atlas 300I Pro (310P) | 与 OCR 共用 |
| **导诊** | BERT | 文本分类 | ≥0.1G | ≥5 | 延迟 ≤20ms / 吞吐 ≥100 qps | 50 | Atlas 300I Pro (310P) | |
| **专病库** | — | — | — | — | — | — | — | 待定 |
| **总计** | 8 个模型 | — | **≈260G** | **≥1495** | — | — | 1×Atlas 800T A2 + 1×Atlas 300I Pro | |

---

## 昇腾选型参考

| 昇腾型号 | 单卡显存 | FP16 算力 | 适用场景 |
|:---|:---|:---|:---|
| Ascend 910B | 64GB HBM2e | ≈320 TFLOPS | 大模型训练/推理 |
| Ascend 910C | ≈128GB | — | 超大模型推理 |
| Ascend 310P | 24GB LPDDR4X | ≈64 TFLOPS (INT8) | 轻量推理（OCR/分类/检测） |
| Atlas 800T A2 | 8×910B 64G | — | 训练服务器 |
| Atlas 800I A2 | 8×910B 64G | — | 推理服务器 |

> **推荐配置**: 智能体部署于 1台 Atlas 800T/I A2（4×910B，256G 显存，1280 TFLOPS），轻量场景共用 1张 Ascend 310P（24G）。总显存约需 260G / 总算力 ≥1495 TFLOPS，4×910B 提供 256G 显存 + 1280 TFLOPS，LLM 通过 TP=4 部署，其余模型按卡分配，留有扩展余量。
