---
title: "AI Rag Agent Development"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Browser Plugin
  - Tool
---

## Introduction

## How to Quickly Build an AI Agent

The fastest way to build an AI agent is through low-code platforms like Coze or Dify. These platforms allow you to quickly import knowledge bases and rapidly construct an AI agent.

A second approach is to use frameworks like LangChain or LangChain4j. This method requires writing your own code but enables more complex functionalities. Compared to Coze, Dify is more suitable for small teams to quickly build AI agents based on Dify to create an MVP version for validation.

构建AI代理最快的方法是通过低代码平台如Coze或Dify。这些平台允许你快速导入知识库并迅速构建AI代理。

第二种方法是使用像LangChain或LangChain4j这样的框架。这种方法需要编写自己的代码，但能实现更复杂的功能。相比Coze，Dify更适合小团队基于Dify快速构建AI代理，创建MVP版本进行验证。

## What the technical stack for buiding a AI Agent

In the Java ecosystem, we typically use Java + LangChain4j + Spring AI to quickly build RAG logic. For vector databases, we generally use Milvus or Weaviate. For hybrid search, we employ Elasticsearch or OpenSearch. The API layer is usually built with Spring Boot or Quarkus. Large language models commonly used are OpenAI or local LLMs (such as Llama.cpp). For storage, we often choose PostgreSQL or MongoDB, and for deployment, we typically use Docker or Kubernetes.

Compared to Python, Java has fewer libraries and tools in the AI domain. Support for new AI technologies and models is usually delayed in Java, and the documentation and community support for LangChain4j and Spring AI are relatively weak.


在Java生态中，我们一般使用Java + LangChain4j + Spring AI 来快速构建RAG逻辑，向量数据库一般采用Milvus / Weaviate，混合搜索层面使用Elasticsearch / OpenSearch，API层使用Spring Boot / Quarkus，大模型一般使用OpenAI / 本地LLM (如Llama.cpp)。存储一般采用PostgreSQL / MongoDB，部署一般采用Docker / K8s。

相比Python，Java在AI领域的库和工具较少，新的AI技术和模型在Java中的支持通常滞后，并且LangChain4j / Spring AI的文档和社区支持相对较弱。

## What is the most important part of buiding a AI Agent

在构建AI代理时，最重要的是解决大模型核心的幻觉问题和知识更新问题。另外，如果企业有条件，可以基于通用模型构建自己的垂直领域模型。通过模型微调进一步解决幻觉问题。下面是我在做RAG实践过程中遇到的一些问题以及我的思考和解决方案，在这里作为一个总结和记录。不管是用coze还是dify以及自建的agent，以下都是要考虑的问题。

## Conclusion

<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>