# 问答对到话术库的映射规则

本文件规定 kb-faq-generator 产出的 16 字段问答对如何转换为本技能的话术库条目（assets/faq-library-template.csv）。两个技能以此构成知识入库到知识服务的完整链路。

## 流程

1. 源文件经过 kb-faq-generator 生成模式，产出 16 字段问答对。
2. 按本文件的字段映射表转换为话术库条目。
3. needs_review 为 yes 的条目不进入正式话术库，待业务部门确认后重新生成或修改后再转。演示库可先入库，但 review_status 列必须保留待审标记。
4. 转换结果追加到话术库，保留原 source_file 和 source_location 作为溯源审计信息，对客输出不包含溯源字段。

## 字段映射表

| 问答对字段 | 话术库去向 | 转换规则 |
|---|---|---|
| question 及其问法变体 | trigger_keywords | 提取客户会使用的说法，顿号分隔，取 2 到 5 个触发词 |
| answer | fixed_reply | 逐字放入，不改写。answer 末尾如含下一步动作表述则拆出 |
| answer 末尾的下一步指引 | next_step | 指引句单独放此列。answer 本体不含动作时按话术库兜底指引规则处理 |
| category | scene_type | 收费标准、免费项目、电子银行类映射为 consultation。挂失、吞卡、交易争议类映射为 report。借记卡章程类按内容归入 consultation 或 report |
| source_file | source_file | 原样保留 |
| source_location | source_location | 原样保留 |
| needs_review | review_status | yes 时 review_status 写为待业务复核，该条目不进入正式库 |
| 无对应列 | 无 | question_type、user_profile、difficulty 两轴、customer_answer、audience、confidence 不进入话术库，仅保留在问答对文件中供评审 |

## customer_answer 与拼装公式的关系

问答对的 customer_answer 是静态预览，用于人工评审。运行时话术库只存 fixed_reply 和 next_step，开头话术、特殊提示、结尾话术由 tone-guide.md 的拼装公式在运行时叠加。两处文本如有差异，以问答对文件的 answer 为事实本体基准。

## 冲突处理

话术库条目与源文件冲突时，以源文件为准，重新走 kb-faq-generator 生成对应条目，禁止手工改写 fixed_reply 绕过溯源。价目表、章程等官方文件版本更替时，按 source_file 定位受影响条目，整批重新生成。

## 维护

kb-faq-generator 每次更新问答对后，按本文件重新执行映射，同步更新话术库。两个技能的话术文本一致性规则见两边各自的 tone-guide.md，冲突时以 kb-faq-generator 的 tone-guide.md 为准。
