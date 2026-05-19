【项目名称】：AlphaInsight —— 基于多智能体辩论架构的自动化金融尽职调查系统

1. 项目解决的核心痛点：

传统困境：在私募股权投资和二级市场投研中，分析师需要耗费数周时间阅读海量非结构化长文本（如上百页的招股说明书、年度财报、高管财报电话会议录音）。人类在处理动辄几十万字的交叉信息时，极易遗漏关键风险点（如：研报正文与附注中财务数据的矛盾），且 80% 的精力被消耗在低价值的数据提取上，而非深度策略思考。
AI 介入的不可替代性：传统 OCR 或 NLP 抽取技术无法理解复杂的财务逻辑（如“应收账款异常增长隐含的坏账风险”）。必须依赖大语言模型的超长上下文能力（Long Context）和逻辑推理能力，才能实现跨文档的交叉验证和深度风险挖掘。

2. 核心逻辑流（长链推理与多 Agent 协作）： 本项目底层基于 LangGraph 构建了非线性的工作流，核心逻辑如下：

输入与动态规划：系统接收目标公司代码及调研指令后，规划 Agent (Planning Agent) 会进行任务拆解，生成包含宏观行业、财务健康、合规风险三个维度的检索意图树。
多 Agent 深度协作与辩论机制：
情报检索 Agent：并发调用 SEC API、新闻接口及内部 RAG 知识库，召回近 3 年的财报及会议纪要。
财务分析 Agent：执行长链推理 (Long-chain Reasoning)，不仅提取数据，还基于杜邦分析法推导利润率下滑的深层原因。
风控做空 Agent（对抗性设计）：这是系统的亮点。该 Agent 拥有独立的 System Prompt，专门扮演“挑刺者”。它会与财务分析 Agent 展开多轮辩论式审查 (Debate Review)。例如：分析 Agent 认为营收增长健康，做空 Agent 会敏锐地跨文档检索，指出“供应商集中度过高”及“存货周转率大幅下降”的矛盾点，迫使系统进行反思。
自我纠错与输出：经过 3-5 轮的多智能体辩论后，主笔 Agent 会汇总最终共识与分歧，调用 Python 解释器绘制财务走势图，最终输出一份长达 20 页、带有精准溯源（引用至原 PDF 具体段落）的深度尽职调查报告。


效果如下图：
<img width="4542" height="2326" alt="截图 2026-05-19 11-37-56" src="https://github.com/user-attachments/assets/dce647f1-a90c-4a70-89ea-b217c604a432" />


<img width="4096" height="2304" alt="截图 2026-05-19 11-39-04" src="https://github.com/user-attachments/assets/2487447d-70ef-43af-acfb-084a16ea182d" />


<img width="4096" height="2304" alt="截图 2026-05-19 11-39-11" src="https://github.com/user-attachments/assets/f3fad2c7-e355-4865-a028-b87f2f1b2acc" />


<img width="4096" height="2304" alt="截图 2026-05-19 11-39-16" src="https://github.com/user-attachments/assets/f36966ad-4b3d-458b-8c91-b8786d995ff5" />


