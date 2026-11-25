# 中文接口文档编写

早期我让 Codex 写接口文档，最头疼的就是它能写一大堆，我看完却还得回去翻代码。比如处方明细要传一个编号，字段说明写着“来源处方标识”。那到底填处方上印的处方号，还是填推送处方主单时用的那条记录编号？文档里两个都有，偏偏没把这层关系讲清楚。

后来我翻了医保接口规范。它把一笔交易单独拎出来，先交代谁在什么情况下调用，再按输入和输出列字段。我把这个写法整理成了 `api-docs-zh`。现在让 Codex 按项目代码或第三方协议写中文 Markdown 对接文档，碰到前面那个字段，就要写到这个程度：推送处方明细时，`source_prescription_id` 填同一张处方主单请求里的 `items[].data.source_record_id`。遇到字典，就把编码和中文名称分成两列，每个取值占一行。

返回结果也顺着调用往下写。拿异步推送举例，医院上传一批记录，收到 HTTP `200`、业务码 `202`，这时先保存返回的 `data.batch_id`。接着拿这个批次号查结果，按 `item_key` 找到刚才推的那条记录。状态是 `APPLIED` 就知道这条已经写入，有问题就读同一条记录的 `errors`。第一次接手的人读到这里，手里就有了下一步要用的值。

## 安装到 Codex


```sh
npx skills add kasen1015/api-docs-skills --skill api-docs-zh -a codex -g
```


## 使用示例


> 用 $api-docs-zh 把这个接口写成给第三方联调的文档，说明上游编号取自哪个字段、如何判断成功、失败后怎么处理。


[查看写作示范](skills/api-docs-zh/references/examples.md) · [查看文档骨架](skills/api-docs-zh/references/outline.md)
