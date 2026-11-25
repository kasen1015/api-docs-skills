# 中文接口文档编写技能与 Markdown 接口文档模板

今天让 Codex 帮我写接口文档，光删废话就删烦了。一个“获取机构信息”，它从业务背景讲到接入流程，恨不得把这辈子的话都写在里面。我往下翻，入参表倒是有，翻过一页，直接到下一个接口了。出参呢？我让它补，它贴了一段 JSON 就算交差。可我要拿这段 JSON 写代码啊，哪个字段是机构编码，状态有哪些取值，查不到机构会返回什么？还得挨个问。我让它把字段讲清楚，它又给每个字段配一段套话，“机构名称，用于表示该机构的名称”。就这么补了删、删了补，反反复复折腾了一天。后来我干脆把要求写成了 `api-docs-zh`。以后谁翻到“获取机构信息”，这一节里就该有完整的入参和出参，字段看表，报文看例子，拿不准的地方不用再回去猜。

## 以前的写法（简化示意）

> **获取机构信息**  
> 根据机构编号查询机构详情。必填参数为机构编号，查询成功后返回机构名称、状态等基本信息。

## 现在的写法：获取机构信息

下面用一个虚构接口演示。

### 1. 接口说明

用户在机构列表选中一家机构后，业务系统向机构目录服务查询详情。已停用的机构也能查询，供查看历史业务时使用。

| 项目 | 说明 |
| --- | --- |
| 请求方式 | `GET` |
| 接口路径 | `/v1/organizations/{org_code}` |
| 响应格式 | JSON |

### 2. 输入参数

| 路径参数 | 类型 | 必填 | 取值来源 |
| --- | --- | --- | --- |
| `org_code` | 字符串 | 是 | 取机构列表响应中所选记录的 `data.items[].org_code`。列表里的 `id` 是内部主键。 |

### 3. 报文示例

假设选中的是“青禾社区卫生服务中心”，列表给出的机构编码为 `QH-SQ-017`。将这个编码放进路径，请求无需正文。

```http
GET /v1/organizations/QH-SQ-017 HTTP/1.1
Host: directory.example
```

HTTP `200` 表示查到了机构。本例返回：

```json
{
  "org_code": "QH-SQ-017",
  "org_name": "青禾社区卫生服务中心",
  "status_code": "INACTIVE",
  "status_name": "停用"
}
```

### 4. 返回参数

以下字段在 HTTP `200` 时均返回，值均为非空字符串。

| 字段 | 含义 | 调用方怎么用 |
| --- | --- | --- |
| `org_code` | 机构编码 | 与所选机构核对 |
| `org_name` | 机构全称 | 显示在详情页 |
| `status_code` | 机构状态编码 | 判断能否用于新业务，详见[机构状态字典](#机构状态字典) |
| `status_name` | 机构状态名称 | 展示“启用”或“停用” |

#### 机构状态字典

| 编码 | 中文名称 | 使用规则 |
| --- | --- | --- |
| `ACTIVE` | 启用 | 可用于新业务 |
| `INACTIVE` | 停用 | 可查看历史记录，新业务不可选 |

### 5. 失败原因与重试处理

HTTP `404` 表示目录中查不到这个机构编码。错误响应示例如下。

```json
{"code": "ORG_NOT_FOUND", "message": "未找到机构编码 QH-SQ-099"}
```

遇到这个错误，先核对请求路径里的值是否取自列表的 `org_code`。如果拿成了内部 `id`，换成正确编码后再查。用同一个错误编码反复重试，结果仍是 `404`。

## 安装

在终端运行对应工具的命令。`-g` 表示安装到个人目录，方便跨项目使用。

| 编程助手 | 安装命令 |
| --- | --- |
| Codex | `npx skills add kasen1015/api-docs-skills --skill api-docs-zh -a codex -g` |
| Claude Code | `npx skills add kasen1015/api-docs-skills --skill api-docs-zh -a claude-code -g` |
| Cursor | `npx skills add kasen1015/api-docs-skills --skill api-docs-zh -a cursor -g` |

其他工具可查阅 [skills 安装工具支持列表](https://github.com/vercel-labs/skills/blob/main/docs/agents.md)。

## 使用示例

> 使用 api-docs-zh 技能，根据项目代码编写获取机构信息的中文接口文档，说明入参从哪里取、响应怎么看、查不到机构时怎么处理。

[查看 Markdown 接口文档模板](skills/api-docs-zh/references/outline.md) · [查看其他联调文档示例](skills/api-docs-zh/references/examples.md)
