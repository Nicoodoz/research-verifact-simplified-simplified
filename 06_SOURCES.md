# 06 来源与精简边界

## 版本与核对范围

本阅读版整理于2026-10-25，研究事实截至097。原仓库HEAD为`1111a3e2519536093231a7cfbaf7509dd16bbd4e`；另纳入工作区现行规则、状态和导师汇报/F4零API复核。后者包含1333、166、340等事后重建口径，不是新增预注册实验。

来源根仓库：https://github.com/Nicoodoz/research-verifact 。表中为原仓库相对路径，SHA为本次实际读取文件的SHA-256前16位。部分本地文档尚未提交到原仓库，不能仅用HEAD解释其字节；来源指纹用于识别版本，不是对原实验做新的冻结审计。含这些文档的较完整阅读快照另在 https://github.com/Nicoodoz/research-verifact-simplified 的提交`43f2d94`，其中部分病例材料已裁剪。

01–05正文为根据这些来源重新编写的摘要，不复制旧报告的未来建议或自动执行指令。原文的历史“下一步”“暂停”“尚有reserved”等以当时阶段为限；本版采用截至097的真实状态。主要数字与表内分母核对原报告，未重放全库预测、重训、调用模型或新增医学判断。

## 保留与省略

保留：任务定义、数据暴露、实际系统操作、主要对照与正负结果、原生/最终区别、构造失败、复现波动、类别代价、实现更正及尚未回答的问题。篇幅按信息价值分配，不按结果是否阳性选择。

省略：原始临床文本、患者和命题逐例标识、模型私有请求/回执、权重/缓存、全部代码、中间版本、重复台账、全部bootstrap数组、逐次费用、环境安装及云服务操作记录。没有把这些东西从原项目删除。本版不够用于独立复算、医学逐例审查或代码复现；聚合数字和原因解释的证据边界在正文保留。

外部论文只保留原VeriFact的任务/源码事实和已测操作与常见方法的关系，不复制27项文献筛查的候选排序、主观成功概率或下一阶段方案。这里没有替读者预选研究路线。新颖性和未测机制仍需另行查证，不能仅凭本版称“首次提出”。

多数阶段只有执行者自检。独立审计是否完成须查原报告和reviews实际记录；本版不是新独立审计。临床引用合法、程序测试PASS与医学充分性、分类成功是不同层级。

## 证据定位

正文〔编号〕对应下表；同一行实验可同时引用主报告与后续修正。早期001–065的部分归并来自076证据地图，未声称本次逐任务完成新的全面审计。

|编号|原文件|SHA-256前16位|
|---|---|---|
|状态|`docs/PROJECT_STATUS.md`|`2308b0f04cf85654`|
|规则|`AGENTS.md`|`293ff840c2b1f4aa`|
|研究原则|`docs/RESEARCH_EXECUTION.md`|`d7173c5806dd2145`|
|汇报复核|`docs/2026-10-15_导师汇报_前处理与大模型推理.md`|`967e9ef3a3967521`|
|F4复核数据|`docs/导师汇报_F4_raw复核.json`|`9aaabd4b6619f413`|
|论文审读|`docs/01_原论文源码与路线审读.md`|`746eb8143bfe638e`|
|009|`outputs/009_retrieval_baselines/report.md`|`2f4e4adc1e36c944`|
|010|`outputs/010_verifact_baseline_alignment/report.md`|`9e1af776802dd30e`|
|011|`outputs/011_evidence_integrity_packaging/report.md`|`ede002bcef55a9a2`|
|013|`outputs/013_multi_mechanism_screen/report.md`|`105a2d19338303ec`|
|017|`outputs/017_verification_supervised_evidence_utility/report.md`|`9d649d5e86563f87`|
|028|`outputs/028_traceable_claim_evidence_verification/report.md`|`185ea2f662a048bb`|
|036|`outputs/036_final_validation/report.md`|`67ecf4afe24eab9f`|
|042|`outputs/042_coverage_aware_evidence_packing/report.md`|`ff3fc24054b5b95c`|
|043|`outputs/043_targeted_evidence_utility/report.md`|`a8772e85106c2b9f`|
|044|`outputs/044_incremental_evidence_recovery/report.md`|`dedb62aef1ad1b77`|
|046|`outputs/046_requirement_aware_retrieval_screen/report.md`|`c319fd3d60940550`|
|047|`outputs/047_mechanism_stratified_validation/report.md`|`25e96d301a2589dc`|
|048|`outputs/048_end_to_end_evidence_recovery/report.md`|`f27fcf68ce2e3a68`|
|049|`outputs/049_evidence_decision_utilization/report.md`|`282733d36ee99446`|
|051|`outputs/051_thinking_mode_validation/report.md`|`134784b85e344392`|
|053|`outputs/053_thinking_prior_validation/report.md`|`c89a80f157b7e045`|
|065|`outputs/065_verification_evidence_construction/report.md`|`40d38e5546b7745d`|
|066|`outputs/066_oracle_headroom/report.md`|`79705453514664f4`|
|067更正|`outputs/069_final_pack_controlled_repair/067_correction.md`|`475be333cbbb48a7`|
|068|`outputs/068_final_pack_utility_diagnosis/report.md`|`2dacd6ff0cea5c52`|
|069|`outputs/069_final_pack_controlled_repair/report.md`|`88f6ebc55edbe18f`|
|070|`outputs/070_fixed_evidence_reasoning_diagnosis/report.md`|`5e86d92c5059f8a0`|
|071|`outputs/071_judge_icl_feasibility/report.md`|`d59a70a34b35d337`|
|072|`outputs/072_adaptive_judge_examples/report.md`|`28a30928d56acbee`|
|073|`outputs/073_clinical_example_matching/report.md`|`6c301fcd98f3a98d`|
|074|`outputs/074_anchor_dynamic_examples/report.md`|`dc910b71c408c77f`|
|075|`outputs/075_annotated_static_examples/report.md`|`dd3229d6f7eb3578`|
|076|`outputs/076_existing_results_synthesis/report.md`|`5ec4270c1f7a5b15`|
|076地图|`outputs/076_existing_results_synthesis/evidence_map.md`|`9a5852844fddb3fb`|
|077|`outputs/077_static_example_discrimination/report.md`|`ca7f885e6f151dba`|
|078|`outputs/078_component_ablation/report.md`|`9a8b581358fd683b`|
|079|`outputs/079_f4_priorg_adaptation/report.md`|`62a5731e41b2fcd8`|
|080|`outputs/080_final_decision_adaptation/report.md`|`b24d18652a87260c`|
|081|`outputs/081_gt_blind_evidence_selection/report.md`|`0624db9a6b5b690c`|
|082|`outputs/082_anchor_configuration_adaptation/report.md`|`a3fca0fdce5fdaf7`|
|083|`outputs/083_native_rationale_information/report.md`|`6e7ee2cad36c5ea2`|
|084|`outputs/084_f4_coverage_validation/report.md`|`ab8077ebaf97cf2a`|
|085|`outputs/085_r1_70b_valdemardi/report_formal.md`|`0e511d27c53e875a`|
|086|`outputs/086_global_demonstration_selection/report.md`|`30e9d5a792f4b52f`|
|086更正|`outputs/087_f4_evidence_intervention/086_correction.md`|`c534cce432b6fdf4`|
|087|`outputs/087_f4_evidence_intervention/report.md`|`e0ddbb832ed90dbc`|
|088|`outputs/088_evidence_preserving_demonstration_compression/report.md`|`cad007647c766950`|
|089|`outputs/089_source_audited_demo_compression/report.md`|`15e8ba2d3c638c2c`|
|090|`outputs/090_demo_constructor_repair_validation/report.md`|`4a80504d2af746dd`|
|092|`outputs/092_task_adapted_verifier/report.md`|`53e8318d464e54a3`|
|093|`outputs/093_lossless_evidence_budget/report.md`|`046c9ff330cfe062`|
|094|`outputs/094_answer_isolated_verification/report.md`|`2e310ff0c525d4db`|
|095|`outputs/095_anchored_minimal_contrast/report.md`|`878f2ee38b2c7a3d`|
|096|`outputs/096_r1_native_priorg/report.md`|`2e19de6895e97139`|
|097|`outputs/097_native_judge_source_dependence/report.md`|`38185f88eb486e80`|
