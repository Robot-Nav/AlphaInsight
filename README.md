# AlphaInsight

**基于多智能体辩论架构的自动化金融尽职调查系统**

## 1. 项目含义
在私募股权投资和二级市场投研中，分析师通常需要耗费数周时间阅读海量非结构化长文本（如上百页的招股说明书、年度财报、高管电话会议纪要等），这对传统人工尽调流程提出了巨大挑战。
**AlphaInsight** 通过引入大语言模型（LLM）的超长上下文处理能力与多智能体（Multi-Agent）协作机制，打破了这一困境。本项目基于 LangGraph 构建非线性工作流，结合 RAG、对抗辩论与财务推理，实现自动化金融尽职调查报告生成。

## 2. 项目内容
本项目底层基于 LangGraph 构建了非线性的工作流，核心逻辑与智能体（Agent）分工如下：
* **输入与动态规划**：系统接收目标公司及调研指令后，**规划 Agent (Planning Agent)** 会进行任务拆解，生成包含宏观行业、财务健康、合规风险三大维度的检索意图树。
* **并发情报检索**：**情报检索 Agent** 并发调用 SEC API、新闻接口及内部 RAG 知识库，召回目标公司近 3 年的财报数据及相关会议纪要。
* **长链财务分析**：**财务分析 Agent** 执行长链推理 (Long-chain Reasoning)，不仅仅是提取数据，还会深度结合财务模型挖掘利润下滑或异常增长背后的深层原因。
* **对抗性风控审查（亮点）**：**风控做空 Agent** 拥有独立的 System Prompt，专门扮演“挑刺者”的角色。它会与财务分析 Agent 展开多轮辩论（例如：质疑营收增长下的供应商集中与存货周转矛盾），迫使系统反思。
* **汇总与生成报告**：经过 3-5 轮的多智能体深度辩论后，**主笔 Agent** 会汇总最终共识与分歧，并调用 Python 代码解释器自动绘制财务走势图表。最终输出包含精准溯源（引用至 PDF 段落）的 20 页深度尽调报告。

## 3. 算法原理
* **非线性状态机图工作流 (LangGraph)**：打破传统的线性 Chain 模式，利用图结构（Nodes 代表 Agent 或函数，Edges 代表条件路由）管理多智能体间的交互逻辑。
* **多智能体对抗辩论机制 (Adversarial Agent Debate)**：借鉴生成式对抗网络 (GAN) 和博弈论思想。引入“财务看多”与“风控做空”的双视角对抗机制，通过多轮交互提升结论可信度。
* **检索增强生成 (RAG)**：对研报和 SEC 文件等非结构化数据进行文本分块 (Chunking) 与向量化 (Embedding)，通过高维向量检索匹配最相关的文本片段，解决大模型事实对齐问题。
* **长链推理 (Long-chain Reasoning / CoT)**：通过系统级 Prompt 工程引导大模型按部就班地进行逻辑推理，将复杂的财务分析问题拆解为多个具有先后顺序的推理子任务。

## 4. 算法公式
项目运行中涉及的核心数学算法与财务分析理论模型：

**1. 向量检索的余弦相似度计算（Cosine Similarity）**
在 RAG 知识库检索模块，用于衡量用户查询向量 $Q$ 与文档片段向量 $D$ 的语义相似度：
$$
\text{Similarity}(Q, D) = \frac{Q \cdot D}{\lVert Q \rVert \times \lVert D \rVert}
= \frac{\sum_{i=1}^{n} Q_i D_i}{\sqrt{\sum_{i=1}^{n} Q_i^2}\ \sqrt{\sum_{i=1}^{n} D_i^2}}
$$

**2. 核心财务拆解：杜邦分析法（DuPont Analysis）**
财务分析 Agent 在进行企业盈利能力溯源时，使用杜邦模型对净资产收益率 (ROE) 进行长链拆解：
$$
\text{ROE} =
\left(\frac{\text{Net Income}}{\text{Sales}}\right)
\times
\left(\frac{\text{Sales}}{\text{Total Assets}}\right)
\times
\left(\frac{\text{Total Assets}}{\text{Shareholders' Equity}}\right)
$$
（即：净资产收益率 = 销售净利率 × 总资产周转率 × 权益乘数）

**3. 辩论收敛与停止阈值（Information Entropy）**
在风控 Agent 和财务 Agent 进行的 3-5 轮对抗中，可用信息增益作为理论截断依据。当第 $t$ 轮辩论产生的新信息熵 $H(t)$ 的变化幅度低于预设阈值 $\epsilon$ 时，系统自动终止辩论进入总结阶段：
$$
\Delta H = \left| H(t-1) - H(t) \right| < \epsilon
$$

## 5. 运行步骤
该项目前端采用 Vite + React + TypeScript 架构，以下为本地运行部署的步骤：

### 环境依赖
- Node.js (推荐 v18 或更高版本)
- npm / pnpm / yarn
- OpenAI API Key (或兼容该格式的大模型接口 Key)

### 快速启动
1. **克隆项目代码**
   ```bash
   git clone https://github.com/Robot-Nav/AlphaInsight.git
   cd AlphaInsight
   ```

2. **安装相关依赖**
   ```bash
   npm install
   ```

3. **配置环境变量**
   在项目根目录复制或创建 `.env` 文件，并填写所需的大模型及第三方检索服务的 API 密钥：
   ```env
   VITE_OPENAI_API_KEY=your_openai_api_key_here
   VITE_SEC_API_KEY=your_sec_api_key_here
   ```

4. **启动本地开发服务器**
   ```bash
   npm run dev
   ```
   启动成功后，打开浏览器访问控制台中显示的本地地址（通常为 `http://localhost:5173` ）。

5. **构建用于生产环境的静态文件**
   ```bash
   npm run build
   npm run preview
   ```

---
## 效果展示
<img width="4542" height="2326" alt="系统架构截图1" src="https://github.com/user-attachments/assets/dce647f1-a90c-4a70-89ea-b217c604a432" />
<img width="4096" height="2304" alt="系统运行截图2" src="https://github.com/user-attachments/assets/2487447d-70ef-43af-acfb-084a16ea182d" />
<img width="4096" height="2304" alt="系统运行截图3" src="https://github.com/user-attachments/assets/f3fad2c7-e355-4865-a028-b87f2f1b2acc" />
<img width="4096" height="2304" alt="系统运行截图4" src="https://github.com/user-attachments/assets/f36966ad-4b3d-458b-8c91-b8786d995ff5" />
