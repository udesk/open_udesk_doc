## 创建工单

> 该接口用于创建工单


### 请求方法

`POST /api/v1/tickets.json`


### 请求参数（Request Body）

| 参数名 | 类型 | 必填 |        说明        |
|--------|------|------|--------------------|
| ticket | 对象 | 是   | 工单信息，详见下文 |

#### ticket 的结构

|      参数名      |  类型  | 必填 |             说明            |
|------------------|--------|------|-----------------------------|
| subject          | 字符串 | 是   | 标题                        |
| content          | 字符串 | 是   | 内容                        |
| email            | 字符串 | 否   | 客户邮箱                    |
| cellphone        | 字符串 | 否   | 客户电话                    |
| customer_token   | 字符串 | 否   | 客户外部唯一标识            |
| status           | 字符串 | 否   | 状态中文名称，默认为打开    |
| priority         | 字符串 | 否   | 优先级中文名称， 默认为标准 |
| assignee         | 对象   | 否   | 受理客服，详见下文          |
| agent_group_name | 字符串 | 否   | 受理客服组名称              |
| ticket_field     | 对象   | 否   | 自定义字段，详见下文        |

> status、priority 的取值参见[工单数据结构](tickets.md#-)。

#### assignee 的结构

| 参数名 |  类型  | 必填 |   说明   |
|--------|--------|------|----------|
| name   | 字符串 | 否   | 客服名称 |
| email  | 字符串 | 否   | 客服邮箱 |

> 该接口会根据 name 和 email 查找客服，作为工单的受理客服。两个参数可以只提供一个，也可以全部提供。

#### ticket_field

格式为 `{"自定义字段标识名":"自定义字段值", ...}`，详见示例。

> 选择类型的值要使用选项的索引值，多选时以逗号拼接为字符串。


### 返回数据

|  属性名   |  类型  |          说明         |
|-----------|--------|-----------------------|
| status    | 整型   | 执行结果码，0代表成功 |
| message   | 字符串 | 执行结果说明          |
| ticket_id | 整型   | 新建工单的id          |


### 查找客户策略

创建工单时，查找客户的流程如下：

1. 先根据 customer_token 查找客户，如果找到将其作为工单的客户；
2. 如果找不到，再根据 email 查找客户，如果找到会尝试将 cellphone 设置给该客户，并作为工单客户；
3. 如果找不到，再根据 cellphone 查找，如果找到会尝试将 email 设置给该客户，并作为工单客户；
4. 如果都没有找到，会用 customer_token、email 和 cellphone 创建新的客户，并作为工单客户。


### 示例

```bash
curl http://demo.udesk.cn/api/v1/tickets?sign=129da7df812jdfsa9912jfdadf81 \
-X POST \
-H 'content-type:application/json' \
-d '
{
    "ticket": {
        "subject":"测试工单1",
        "content":"工单测试",
        "customer_token":"customer_test_1",
        "email":"customer@sample.com",
        "cellphone":"13123456789",
        "priority":"标准",
        "status":"解决中",
        "agent_group_name":"默认组",
        "assignee":{
            "email":"agent@sample.com"
        },
        "ticket_field":{
            "TextField_1": "普通文本内容",
            "TextField_2": "多行文本内容1\r\n多行文本内容2",
            "TextField_3": "2016-08-11",
            "TextField_4": "14:44:36",
            "TextField_5": "2017-05-03 14:44",
            "TextField_6": "http://www.sample.com",
            "TextField_7": "13",
            "TextField_8": "13.33",
            "SelectField_1": "0",
            "SelectField_2": "0",
            "SelectField_3": "0,3"
        }
    }
}'
```

返回

```json
{
    "status":0,
    "message":"工单创建成功",
    "ticket_id":2
}
```

