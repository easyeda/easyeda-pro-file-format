# 通用格式

本章节详细介绍了嘉立创EDA原理图文件的格式规范。原原理图文件主要包含以下几个部分：

- **通用配置**：文档头和画布配置信息。
- **结构元素**：PART子库和 GROUP组合。
- **属性**：图元属性定义。
- **导线**：导线、总线和总线入口。
- **文本**：文本元素。
- **形状**：矩形、多边形、圆形、圆弧、贝塞尔曲线、椭圆。●引脚：引脚定义。
- **元件**：原理图元件实例。
- **对象**：二进制对象（图片等）。
- **表格**：表格和单元格。


## 文档头

```json
{ "type": "DOCHEAD" }||{ "docType": "SCH_PAGE", "uuid": "UUID", "client": "clientID" }|
```

```json
{ "type": "DOCHEAD" }||{ "docType": "SYMBOL", "uuid": "UUID", "client": "clientID" }|
```

-   type："DOCHEAD"，文档头标识
-   docType：文档类型，"SCH_PAGE"：原理图、 "SYMBOL"：符号
-   uuid：文档唯一编号，工程内唯一
-   client：最终一致性的一个终端标识

## 画布配置

编辑器附加信息，用于数据分析等功能，目前已占用的一些字段

```json
{ "type": "CANVAS", "ticket": 1 }||
{
  "originX":0,
  "originY":0,
}|
```

1. type："CANVAS"，画布配置信息标识
2. ticket 逻辑时钟
3. originX 画布原点 X
4. originY 画布原点 Y
