# Enterprise RAG Agent

面向企业知识库的RAG智能对话助手，基于LangGraph构建，支持多格式文档检索、工具调用与任务规划。

## 项目简介

本项目是一个通用的企业知识库RAG Agent，解决传统RAG系统在文档检索精度、多轮对话记忆、工具调用能力上的不足。系统能够解析企业文档（PDF/Word/Markdown），通过混合检索与Agent编排，实现文档问答、摘要生成、多轮对话，并支持MCP协议进行工具标准化接入。

## 核心功能

- **多格式文档解析**：基于PyMuPDF解析PDF、Word、Markdown等格式，按章节结构进行语义分块
- **混合检索增强**：结合FAISS向量检索与BM25关键词检索，提升专业术语的召回率与精确率
- **LangGraph Agent编排**：基于StateGraph实现意图分类、工具调用、多轮对话记忆的完整决策流
- **Function Calling / 工具调用**：内置“文档摘要生成器”工具，Agent能自主判断何时检索、何时生成摘要
- **MCP Server封装**：将文档检索与摘要能力封装为标准化MCP服务，支持跨平台工具接入
- **FastAPI后端**：提供RESTful API，支持文档上传、对话交互与流式输出
- **Docker私有化部署**：提供容器化打包方案

## 架构图

```mermaid
graph TD
    User[用户提问] --> FastAPI[FastAPI 后端]
    FastAPI --> Agent[LangGraph Agent 编排]
    
    Agent --> Intent[意图分类节点]
    Intent -->|需要检索| RAG[RAG 检索工具]
    Intent -->|需要摘要| Summary[文档摘要生成器]
    Intent -->|通用对话| LLM[大模型生成]
    
    RAG --> Vector[FAISS 向量检索]
    RAG --> BM25[BM25 关键词检索]
    Vector --> Rerank[重排序]
    BM25 --> Rerank
    Rerank --> Prompt[拼接 Prompt]
    
    Summary --> MCP[MCP Server]
    Prompt --> LLM
    LLM --> Validate[校验节点]
    Validate --> Response[返回回答与引用]
    
    MCP --> Summary
