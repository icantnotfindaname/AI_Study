# Chapter2 Agent的简介

任务环境的规范描述：PEAS
- Performance
- Environment
- Actuators 执行器
- Sensors 传感器

Agent = Architecture(体系结构 ) + Program

四种基本的Agent程序：
- Simple reflection：基于当前感知选择行动，不关注感知历史   【随机化避免无限循环】
- 基于模型的反射 Model-based reflection：使用内部模型跟踪世界的当前状态，以此做出决策
- 基于目标的 Goal-based：同样有Model，但也要记录目标  【搜索和规划是寻找达成Agent目标的行动序列的人工智能领域】  【效率低但是更加灵活，可以对相关的行为进行修改来适应新的条件】
- 基于效用的 Utility-based：性能度量得出区分度，有一个效用函数  【Model + Utility Function 然后选择最佳执行方案】

组件表示：
- Atomic  比如城市之间，简化成两个点
- Factored  变量或者特征的集合
- Structured  事物之间互相关联

