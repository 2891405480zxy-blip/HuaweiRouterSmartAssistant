# 基于 Dify + LangChain 双引擎架构的企业级网络运维智能诊断系统

针对企业网络运维场景，基于华为官方产品文档构建私有化智能诊断系统。采用 Dify + LangChain 双引擎架构，支持设备问答、故障排查与报告自动生成。

## 项目截图
<img width="729" height="803" alt="image" src="https://github.com/user-attachments/assets/4e9bddb2-a215-42d0-9700-c4d6b4e9ad15" />

## 技术架构
- **大语言模型**：通义千问 qwen-max
- **双引擎架构**：Dify 负责工作流编排与对话管理，LangChain 封装为 FastAPI 提供 Agent 推理能力，通过 HTTP API 对接
- **RAG 框架**：LangChain + ChromaDB 向量数据库
- **检索策略**：向量检索 + BM25 混合检索，自主实现 HybridRetriever 类
- **前端界面**：Dify Chatflow / Streamlit

## 核心功能
- **智能诊断**：基于华为路由器官方产品文档构建私有知识库，支持设备配置、故障排查、网络优化等问题解答
- **混合检索**：融合向量相似度检索与 BM25 关键词检索，解决纯语义检索在专有名词匹配上的不足，Top-K 召回率提升约 10%-15%
- **多链协作**：ReAct Agent 统一调度知识问答、故障排查、报告生成三条处理链，根据用户意图自动路由
- **动态 Prompt**：中间件机制实现问答和报告两种模式自动切换，无需修改主流程代码

## 系统架构
```
用户输入
│
▼
Dify Chatflow（工作流编排 + 对话管理）
│  HTTP API
▼
FastAPI 服务（LangChain 引擎层）
│
▼
ReAct Agent
├── 知识问答链 → ChromaDB 混合检索
├── 故障排查链 → 外部数据 + RAG
└── 报告生成链 → 用户数据聚合
```

## 项目结构
```
├── agent/              # Agent 核心
│   ├── tools/          # 工具定义与中间件
│   └── react_agent.py  # ReAct Agent 主体
├── api/                # FastAPI 服务层
│   └── server.py       # HTTP 接口，供 Dify 调用
├── rag/                # RAG 核心
│   ├── rag_service.py  # RAG 服务
│   └── vector_store.py # 向量存储与混合检索
├── model/              # 模型工厂
├── prompts/            # 提示词管理
├── config/             # yml 配置文件
├── utils/              # 工具函数
├── data/               # 知识库文档
└── app.py              # Streamlit 入口
```
## 快速启动

**1. 安装依赖**
```bash
pip install -r requirements.txt
```

**2. 配置 API Key**

在 `config/rag.yml` 中填入阿里云百炼的 API Key。

**3. 建立知识库**
```bash
python -m rag.vector_store
```

**4. 启动 FastAPI 服务**
```bash
uvicorn api.server:app --host 0.0.0.0 --port 8000
```

**5. 启动 Streamlit（可选）**
```bash
streamlit run app.py
```

**6. Dify 配置**

本地启动 Dify（Docker），在 Chatflow 中添加 HTTP 请求节点，URL 填写 `http://host.docker.internal:8000/agent/chat`，即可对接 LangChain 引擎。

## 技术亮点

**双引擎架构**：Dify 承担产品层编排，LangChain 封装为 FastAPI 服务承担引擎层推理，两者解耦，各司其职。

**混合检索**：自主实现 HybridRetriever 类，融合向量检索与 BM25 关键词检索，解决纯语义检索在精确词汇匹配上的不足。

**多链协作**：ReAct Agent 统一调度三条处理链，根据用户意图自动路由，支持复杂多轮对话场景。

**中间件机制**：在工具调用和模型调用关键节点插入监控逻辑，实现动态 Prompt 切换，无需修改主流程代码。
