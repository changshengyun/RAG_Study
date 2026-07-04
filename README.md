# kg_rag_project

项目演示：基于知识检索增强（RAG）与向量数据库的轻量级示例工程。

**目的**: 展示如何把文档切分、生成句向量、存入向量数据库（Chroma）、检索召回、重排（rerank）并调用大模型生成答案的端到端流程。

**项目结构**
- `doc.md`：示例文档，作为知识源。
- `main.ipynb`：主笔记本，包含分片、向量化、存储、检索、重排、生成等步骤的实现代码。
- `pyproject.toml`：项目依赖与元信息（如存在）。

快速说明
- 分片：将 `doc.md` 按空行分片进行处理。
- 嵌入：使用 `sentence-transformers` 生成文本向量（示例中使用中文 `text2vec-base-chinese` 模型）。
- 向量库：使用 `chromadb`（示例采用内存型 EphemeralClient，可换为 PersistentClient 以持久化）。
- 检索：根据查询向量检索 top-k 文档片段。
- 重排：用 `CrossEncoder` 对检索结果按相关度重排，选取 top-k 供生成模型参考。
- 生成：调用 DeepSeek / OpenAI 风格的客户端（示例使用 `OpenAI` 封装的 `ds_client`）进行 RAG 答案生成，注意设置低温度以降低幻觉。

依赖（示例）
- Python 3.8+
- sentence-transformers
- chromadb
- cross-encoder（CrossEncoder）来自 sentence-transformers 的扩展模型包
- python-dotenv
- openai（或 DeepSeek 提供的 SDK，如果使用 deepseek）

安装示例（建议在虚拟环境中执行）

```bash
python -m venv .venv
source .venv/bin/activate   # Windows PowerShell: .\.venv\Scripts\Activate.ps1
pip install -U pip
pip install sentence-transformers chromadb python-dotenv openai
```

配置
- 在项目根目录创建 `.env` 文件，放入以下环境变量（示例）：

```
DEEPSEEK_API_KEY=your_deepseek_api_key_here
```

运行（快速）
1. 启动 Jupyter Notebook/Lab：`jupyter notebook` 或 `jupyter lab`
2. 打开 `main.ipynb`，按单元顺序运行：
	- 分片（split）生成 `chunks`
	- 嵌入（embed）生成 `embeddings`
	- 写入 Chroma（`chromadb_collection.add`）
	- 检索（retrieve）并重排（rerank`)
	- 使用 `generate()` 调用大模型生成答案

注意事项
- 本示例代码为教学/演示用途：为避免在生产中出现规模或性能问题，请考虑批量化、向量化加速、持久化存储和并发安全策略。
- 生成模型可能会“幻觉”出未在片段中出现的事实，示例中已用低温与检索片段约束回答，仍需在生产中加入更严格的提示与校验策略。

贡献
- 欢迎提交 issue 或 PR。若需扩展：可增加持久化的 Chroma 客户端、并加入单元测试与示例数据集。

许可证
- 请在仓库中添加适当的许可证文件（例如 MIT）。

---

