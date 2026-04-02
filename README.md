# 华为路由器智能客服系统

![Python](https://img.shields.io/badge/Python-3.11-blue) ![License](https://img.shields.io/badge/License-MIT-green)

基于 **RAG + ReAct Agent** 架构的智能客服系统，支持产品问答、故障排除和个人使用报告生成。

---

## 项目截图

![项目截图](img_1.png)

---

## 技术架构

- **大语言模型**：通义千问 qwen3-max  
- **RAG 框架**：LangChain + ChromaDB 向量数据库  
- **检索策略**：向量检索 + BM25 混合检索  
- **Agent 框架**：ReAct Agent + 中间件机制  
- **前端界面**：Streamlit  

---

## 核心功能

- **智能问答**：基于华为路由器用户手册构建知识库，支持设置指南、故障排除、网络优化等问题解答  
- **混合检索**：结合向量相似度检索与 BM25 关键词检索，提升检索准确率  
- **使用报告**：Agent 自动调用工具链获取用户数据，生成个性化使用报告  
- **动态 Prompt**：中间件机制实现问答和报告两种模式自动切换  

---

## 项目结构
```text
agent/               # Agent 核心
    tools/           # 工具定义与中间件
    react_agent.py   # ReAct Agent 主体
rag/                 # RAG 核心
    rag_service.py   # RAG 服务
    vector_store.py  # 向量存储与混合检索
model/               # 模型工厂
prompts/             # 提示词管理
config/              # yml 配置文件
utils/               # 工具函数
data/                # 知识库文档
app.py               # Streamlit 网页入口
快速启动
安装依赖
pip install -r requirements.txt
配置 API Key
在 config/rag.yml 中填入阿里云百炼的 API Key。
建立知识库
python -m rag.vector_store
启动应用
streamlit run app.py
技术亮点
混合检索：原生实现向量检索与 BM25 关键词检索融合，解决纯语义检索在精确词汇匹配上的不足。
模块化设计：向量存储、RAG 服务、Agent、工具、配置拆分为独立模块，通过 yml 配置文件统一管理，降低耦合度。
中间件机制：在工具调用和模型调用的关键节点插入监控逻辑，实现动态 Prompt 切换，无需修改主流程代码。
