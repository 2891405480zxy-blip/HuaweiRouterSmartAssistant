## 快速启动

### 1. 环境准备

```bash
# 克隆项目
git clone https://github.com/zhangxinyu-dev/HuaweiRouterSmartAssistant
cd HuaweiRouterSmartAssistant

# 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装依赖
pip install -r requirements.txt
```

### 2. 配置 API Key

在 `config/rag.yml` 中填入：
```yaml
model:
  api_key: "your_aliyun_api_key"  # 阿里云百炼 API Key
```

### 3. 启动 LangChain 引擎（FastAPI 服务）

```bash
uvicorn api.server:app --host 0.0.0.0 --port 8000
```

### 4. 启动 Dify（Docker）

```bash
docker-compose up -d
# 访问 http://localhost:80
```

### 5. 在 Dify 中配置 HTTP 请求节点

- 新建 Chatflow 应用
- 添加 HTTP 请求节点，URL 配置为：`http://host.docker.internal:8000/agent/chat`
- 连接用户输入 → HTTP 请求 → 直接回复

## 技术亮点

### 🔄 双引擎架构解耦

- **Dify（产品层）**：负责对话流编排与用户交互，开箱即用的可视化工作流
- **LangChain（引擎层）**：封装为 FastAPI 微服务，提供复杂推理与 Agent 能力
- 通过 HTTP API 对接，实现产品化与深度计算的分离

### 🎯 混合检索突破

自主实现 HybridRetriever 类，融合向量检索与 BM25 关键词检索：
- 向量检索：捕捉语义相似度
- BM25：精确匹配专有名词（如设备型号、故障码）
- **效果**：Top-K 召回率相比纯向量方案提升 10%-15%

### 🔗 多链协作调度

基于 ReAct 框架设计 Agent，统一调度三条处理链：
1. **知识问答链**：向量召回 + LLM 生成
2. **故障排查链**：多步推理 + 工具调用
3. **报告生成链**：数据聚合 + 结构化输出

根据用户意图自动路由，无需人工干预。

### 🎛️ 动态策略控制

中间件机制在关键节点动态注入上下文与切换 Prompt：
- 工具调用监控（重复调用检测）
- 模型调用前日志记录
- 问答 vs 报告模式自动切换
- 无需修改主流程代码，灵活可扩展

## 与朋友项目的区别

| 维度 | 本项目 | 常见求职助手 |
|------|------|-----------|
| 数据来源 | 华为官方产品文档 | AI 生成模拟数据 |
| 应用场景 | 企业网络运维（ToB） | 个人求职辅助（ToC） |
| 检索方案 | 混合检索（向量+BM25） | 纯向量检索 |
| 架构复杂度 | 双引擎解耦设计 | 单一 LLM 调用 |
| 工程深度 | 中间件、多链协作 | 简单工具组合 |

## 后续优化方向

- [ ] 集成 LangGraph 替代 ReAct，支持复杂状态管理
- [ ] 接入向量数据库监控，实时评估检索质量
- [ ] Pydantic 结构化输出，支持设备配置的结构化提取
- [ ] 用户反馈循环，动态优化检索阈值和 Prompt

## 开源声明

本项目知识库基于华为官方产品文档构建，仅供学习研究使用。

---

**GitHub**: https://github.com/zhangxinyu-dev/HuaweiRouterSmartAssistant
