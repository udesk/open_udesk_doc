# Udesk H5离线推送

## 功能状态

[开发中]

## 更新记录

| 内容  | 日期        | 更新人 | 版本   |
|-----|-----------|-----|------|
| 创建  | 201700803 | 韦昭  | v0.1 |
| 更新  | 20171229 | 王明乐  | v0.2 |

## 概述

  **服务对象**: 嵌入 UdeskIM H5的APP

  **为什么**: 当 APP 被放入后台运行时, webview 中的H5随机失效,收不到消息.

  **方案**: UdeskIM 提供把客服离线消息推送给 APP的功能

  **如何**: UdeskIM 将离线消息推送给 用户设置的推送地址

  **可选**: 由于移动端的特殊性, UdeskIM并不能准确地识别用户是否离线, Udesk提供手动控制是否推送的接口

## 配置

  管理员 -> 设置 -> 网页插件 -> 其他设置 -> H5 推送地址

## 接口

### H5接入文档

[文档](http://www.udesk.cn/website/doc/thirdparty/webim/)

**注意**

为了防止推送滥用,需要在 APP webview 嵌入H5时加上 h5push=true 的参数,H5推送才会执行

创建会话后的客服消息才会推送

### 推送开关

 H5推送接口遵循 [Udesk API V2 接口](http://www.udesk.cn/website/doc/apiv2/intro/)

`POST open_api_v1/im/h5push`


| 参数名              | 必填  | 说明             |
|------------------|-----|----------------|
| customer_token   | 否   | OpenApi 客户唯一标识 |
| web_token        | 否   | web 客户唯一标识[推荐] |
| sdk_token        | 否   | sdk 客户唯一标识     |
| im_web_plugin_id | 是   | 会话插件id         |
| h5push           | 否   | true/false 是否  |

**注** 用户唯一标识有且只有一个

返回

```json
{
  code: 1000,
  message: '请求成功',
  status: 无推送 推送中 无效客户
  status_code: 0 1 3
}

```

### 离线消息推送

`POST #配置的h5push地址`

#### 推送消息格式

| 参数名              | 类型   | 必填  | 说明         |
|------------------|------|-----|------------|
| web_token        | 字符串  | 是   | 应用端客户唯一标识  |
| im_web_plugin_id | 数字   | 否   | 区别不同的会话的插件 |
| messages         | 对象数组 | 是   | 消息         |

**messages** [文档内容请参考](https://github.com/udesk/open_udesk_doc/blob/master/doc/im.md#%E5%9B%9E%E5%A4%8D%E6%B6%88%E6%81%AF%E9%80%9A%E7%9F%A5) 


### 1)关闭会话事件推送
![交互图](im/h5_pushclose.png)

`POST #配置的h5push地址`
**注** 关闭事件只有在开启离线推送前提下生效

#### 推送消息格式

| 参数名              | 类型   | 必填  | 说明         |
|------------------|------|-----|------------|
| web_token        | 字符串  | 是   | 应用端客户唯一标识  |
| im_web_plugin_id | 数字   | 否   | 区别不同的会话的插件 |
| messages         | 对象数组 | 是   | 消息         |

**messages** [文档内容请参考](https://github.com/udesk/open_udesk_doc/blob/develop/doc/im.md#%E5%9B%9E%E5%A4%8D%E6%B6%88%E6%81%AF%E9%80%9A%E7%9F%A5) 

### 2)关闭会话事件推送后客户评价

[文档内容请参考](https://github.com/udesk/open_udesk_doc/blob/master/doc/im.md#%E4%BC%9A%E8%AF%9D%E8%AF%84%E4%BB%B7)