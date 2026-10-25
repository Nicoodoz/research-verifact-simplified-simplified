# 02 实际系统与原VeriFact的差异

## 1. 原作者参照是什么

主参照为原作者公开的同ID预测，较强发布配置为R1-70B、100条sentence facts、rerank、absolute_time、当前住院及去重。源码细节核对作者维护提交`f115d36`，不反推论文当年每次运行完全相同。它不是本项目重新运行的同Judge对照。〔010、论文审读〕

原链路：atomic claim→患者/住院范围的源句子事实→BGE-M3 dense＋learned sparse与融合→去重、BGE重排、top100→带日期/来源的时间序reference→reasoning Judge→辅助模型结构化整理→S/NS/NA。

原reasoning分支只要求verdict和简洁reason，先判断是否addressed；实际调用无示例。非reasoning分支使用三个合成示例。源码存在few-shot reasoning函数不等于实际走了该函数。R1特定模型名会触发system指令并入user；主reasoning模型没有传强制SimpleVerdict schema，而由aux整理结果。〔论文审读〕

静态代码核查发现过重排返回的新列表可能未写回外层列表的风险，并做了最小语义复现；没有证明发布reference或论文成绩实际受影响。修复该问题本身也不自动构成方法贡献。

## 2. 本项目C48前处理

|环节|实际实现|与原作者/目标的关系|
|---|---|---|
|query|原claim，不重写目标、不加入正确原版BHC|避免把目标上下文当额外患者证据|
|候选EHR范围|同患者，CHARTDATE在入院至出院日期内，排除出院总结|是日期过滤，不是逐条HADM_ID硬匹配；患者/时间过滤不是新意|
|切片|按空行分段，超过640字符时在后半段空白处继续切；保存原文起止|段落切片，既非原作者sentence facts，也非语义事件单元；限定/标题仍可能被切开|
|两路召回|BGE dense与本地BM25，各取前80|BM25不是作者BGE learned sparse|
|合并C48|两路交替加入未重复ID，满48停止；候选足够时各保留前24|名额保留，不是作者分数融合，也不是A48的RRF|
|重排|原claim对48条检索文本做BGE rerank；文本含日期、类别、DESCRIPTION和正文|相关性得分不是临床支持概率|
|final pack|重排降序贪心，最多16整段，每note最多2段，约8000字符；放不下则跳过|48是候选数，Judge不一定看48段；可能排除关键第三段或较长段|
|去重|日期＋类别＋正文完全一致才去重|跨日期重复正文仍保留，不等于识别临床事件重复|
|展示|选完按日期、时间、note、原文位置排序；附Note ID和字符区间|时序不是新意；来源定位细；基础formatter不展示DESCRIPTION，可能损失检查名称|

局部头和正文计入8000字符，全局住院头另计，因此reference可略超8000。原作者按top100句子控制数量，两个系统并非等信息量。078桥接中平均字符约8056对22474，C48约少64%；thinking Judge均费约¥0.02592对¥0.03819，约少32%，只计Judge，不含完整检索成本，也未证明精度非劣。〔汇报复核、078〕

095全量reference有032冻结3675、036冻结3573、本地BGE补齐1817条三种来源。后者不声称与原API逐字等价；这一异质性必须随全量结果说明。〔095〕

## 3. V4 T-S和F4

V4在报告中指冻结服务ID`deepseek-v4-flash-0731`。普通/评分与thinking是不同因素：

|记号|实际操作|
|---|---|
|N-V|非thinking，verdict＋reason＋checks|
|N-S|非thinking，再要求三类label_scores|
|T-V|thinking-high，verdict＋reason＋checks|
|T-S|thinking-high，再要求三类label_scores；无示例记B|
|F4|同T-S加入固定四个其他患者的真实医生示例，顺序S/NS/S/NA|

通常thinking输出上限8192、非thinking2048，temperature=0。因此N/T比较包含推理模式与预算差异，不是隔离的内部推理能力测量。checks常含claim_summary、relevant_evidence、supporting_case、opposing_case、overall_assessment。label_scores要求三类整数合计100；它们是自报分数，不是真实token概率。〔051、078、083〕

F4来自071：四名不同患者、三医生首轮一致、2S/1NS/1NA。两S示例采用经核对的短EHR块；NS/NA保留原完整reference，不由模型编造标准解释。四例reference合计17473字符，两个长NS/NA各约8000字符。加入system后，输入常从约3200 tokens增至8800 tokens；084单条Judge均费约¥0.02589→¥0.04170，增加61%。

071评价患者与选例来源患者隔离；后续不少评价患者曾属于历史候选池，虽排除了实际四例患者，也不能都叫未接触示例池的测试。084新增180条中121条/14患者来自071候选患者池，只有59条/7患者池外。后续汇总必须保留这一暴露差异。〔071、084、088〕

## 4. 后处理为何仍必须在事实档案里说明

|名称|操作|不是|
|---|---|---|
|U0|自报三分数最高类|自动等同raw verdict|
|U2|S的log分数加2，再选类|新检索或新的证据推理|
|G（原组件名）|训练得到的一维S偏置|双模型Gate|
|prior-only|按参考与校准类别比例修正分数|识别单病例证据充分性|
|Frozen PriorG|冻结G＋先验比修正|已证实可跨模型/新医院迁移的算法|
|Gate A|用两Judge等信号筛选U0→U2翻S|PriorG|

本阅读版“＋G”一律简称完整Frozen PriorG，讨论内部小G时写“一维G”。V4冻结PriorG相当于S/NS/NA分数分别乘约2.7397/0.2208/0.3675，S相对NS约放大12.4倍。078的384条上prior-only与完整PriorG逐例同标签，不能把二者算成两个独立增益。〔076、078〕

它在固定V4分布上有真实分类收益，但不会补进缺失证据，也不会自动同步原reason；“标签改对”与“解释有证据支持”是不同问题。类别比例和校准标签可以合法用于训练，不等于评价时逐例读GT改答案；与此同时，方法新颖性、同监督强对照与迁移性仍未因此成立。〔036、053、078、082、096〕

## 5. R1与097接口的区别

085/096使用Valdemardi R1-Distill-Llama-70B AWQ，096为AWQ-Marlin。它不是原Casperhansen checkpoint，且使用本项目C48、复杂checks/三分数任务与消息组织，不能称原VeriFact完整复现。085实际没有response_format强制解码，不能把其下降说成“JSON grammar关闭了思考”。〔085、096、论文审读〕

097固定同一V4后端，比较C48/发布reference×当前评分/原作者式简洁任务。简洁版无示例、无分数，确定性解析verdict/reason；未复现原作者always-aux链。它同时改变任务说明、字段及格式契约，收益不能全归于JSON格式；无分数臂也不能补造分数套PriorG。〔097〕
