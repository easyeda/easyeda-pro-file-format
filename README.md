# easyeda-pro-file-format

嘉立创EDA专业版文件格式 / EasyEDA Pro file format

嘉立创EDA专业版文件格式用于描述工程、原理图、PCB、封装、符号等设计数据。该格式以日志文档的形式组织，每一行由外层一致性元数据和内层原子数据组成，支持增量存储、最终一致性和版本演进。

The EasyEDA Pro file format is used to describe design data such as projects, schematics, PCBs, footprints, and symbols. It is organized as a log document in which each line consists of outer consistency metadata and inner atomic data, supporting incremental storage, eventual consistency, and version evolution.

---

## 目录 / Table of Contents

- [文件格式版本 / Format Versions](#文件格式版本--format-versions)
- [文档约定 / Document Conventions](#文档约定--document-conventions)
- [文档类型 / Document Types](#文档类型--document-types)
- [数据格式 / Data Format](#数据格式--data-format)
- [最终一致性 / Eventual Consistency](#最终一致性--eventual-consistency)
- [删除机制 / Deletion](#删除机制--deletion)
- [格式分类 / Format Categories](#格式分类--format-categories)
- [相关链接 / Links](#相关链接--links)

---

## 文件格式版本 / Format Versions

### V3（当前版本 / Current Version）

V3 使用全新的源码格式，完全不同于之前的格式。

V3 uses a completely new source format, different from previous versions.

> 提示：嘉立创EDA 3.0 全面改造了工程格式，使用日志的概念对工程变更进行了增量存储；并且从 2.0 的按照数组偏移量的设计风格，调整成了按照 key-value 的风格，便于后期格式演进，和相关兼容代码的开发，以及提高可读性。
>
> Note: EasyEDA 3.0 completely reworked the project format, using a log-based concept for incremental storage of project changes. The design shifted from array-offset-based (in 2.0) to key-value style, making future format evolution, compatibility code development, and readability easier.

V3 文件格式文档：

- [中文 / Chinese](cn/index.md)
- [English / 英文](en/index.md)

这不是最新的文件格式规范，但包含了绝大部分的格式细节。

This is not the latest file format specification, but it contains most of the format details.


### V2.2（历史版本 / Historical Version）

> "提示"：嘉立创EDA专业版从 V3 开始已经不再使用 V2 的文件格式，当仍支持 V2 格式的导入和导出（会有细节差异），建议开发者使用 V3 格式进行开发，如果需要查看 V2 格式，具体请查阅：[easyeda/easyeda-pro-file-format-v2](https://github.com/easyeda/easyeda-pro-file-format-v2)


> "Note": Since version 3, the EasyEDA Pro version has stopped using the file format of version 2, but it still supports importing and exporting files in version 2 format (with some differences in details). Developers are recommended to use version 3 format for development. If you want to check V2 format, for specific details, please refer to: [easyeda/easyeda-pro-file-format-v2](https://github.com/easyeda/easyeda-pro-file-format-v2)


---

## 文档约定 / Document Conventions

- 工程的所有数据存在一个日志文档里，日志记录变动的首行必须是文件头 `DOCTYPE`，以区分不同类型文档，`DOCTYPE` 作为一个块级元素存在于工程日志中。
- All project data is stored in a log document; the first line of each log record must be the document header `DOCTYPE` to distinguish different document types. `DOCTYPE` exists as a block-level element in the project log.

- 以行为单位，每一行都是由两个合法的 JSON 对象拼接而成，第一个对象是用于最终一致性框架解析使用，后一个是原子结构对象。
- Each line consists of two valid JSON objects concatenated together: the first is used by the eventual consistency framework for parsing, and the second is the atomic structure object.

- 键名采用驼峰命名，每个单词的首字母大写（除了第一个单词的首字母外），并且单词之间没有下划线或其他分隔符。
- Keys use camelCase naming: the first letter of each word is capitalized except the first word, and there are no underscores or other separators between words.

- 所有图元都要带上文件内的唯一编号。
- All primitives must carry a unique ID within the document.

- `旋转角度` 以逆时针方向为正，统一使用角度制。
- Rotation angle is positive in the counter-clockwise direction, using degrees.

- 若无特殊说明，所有坐标、长度、大小统一使用 `0.01 inch` 为单位。
- Unless otherwise specified, all coordinates, lengths, and sizes use `0.01 inch` as the unit.

- 所有颜色都使用 `"#RRGGBB"` 的方式表达，如果需要表示无颜色（完全透明），则用 `""`。
- All colors are expressed as `"#RRGGBB"`. Use `""` to represent no color (fully transparent).

- 所有使用 `是否XXXX` 描述的属性，都使用 `1` 表示是，`0` 表示否。
- All properties described as `whether XXXX` use `1` for yes and `0` for no.

- 本约定中未明确描述的部分（如转义等），全部依据 [RFC 7195 The JavaScript Object Notation (JSON) Data Interchange Format](https://tools.ietf.org/html/rfc7159)。
- Parts not explicitly described (such as escaping) follow [RFC 7195 The JavaScript Object Notation (JSON) Data Interchange Format](https://tools.ietf.org/html/rfc7159).

---

## 文档类型 / Document Types

| 文档类型 / Document Type | 说明 / Description |
| --- | --- |
| `PROJECT_CONFIG` | 工程配置 / Project configuration |
| `BOARD` | 板子 / Board |
| `SCH` | 原理图 / Schematic |
| `SCH_PAGE` | 原理图页 / Schematic page |
| `PCB` | PCB 文档 / PCB document |
| `PANEL` | 面板 / Panel |
| `SYMBOL` | 符号 / Symbol |
| `FOOTPRINT` | 封装 / Footprint |
| `DEVICE` | 器件 / Device |
| `BLOB` | 真彩图 / True-color image |
| `INSTANCE` | 实例属性 / Instance attributes |

### 文档头示例 / Document Header Example

```json
{ "type": "DOCHEAD" }||{ "docType": "SCH_PAGE", "uuid": "UUID", "client": "clientID" }|
```

- `type`: 固定为 `"DOCHEAD"` / Always `"DOCHEAD"`
- `docType`: 文档类型 / Document type
- `uuid`: 文档唯一编号，工程内唯一 / Unique document ID within the project
- `client`: 最终一致性的一个终端标识 / Client identifier for eventual consistency

---

## 数据格式 / Data Format

```json
{ "type": "TYPE", "id": "UUID", "ticket": 1 }||{ ["key": string]: any }|
```

通过标识符 `||` 将一条数据分割成内外两层：

The `||` delimiter splits each data record into an outer and inner layer:

### 外层数据 / Outer Data

用于最终一致性框架，保证数据的一致性。

Used by the eventual consistency framework to ensure data consistency.

| 字段 / Field | 说明 / Description |
| --- | --- |
| `type` | 数据类型 / Data type |
| `id` | 唯一编号，需保证在文档内具体的 type 下唯一 / Unique ID, unique under the specific type within the document |
| `ticket` | 逻辑时钟，用于最终一致性框架 / Logical clock used by the eventual consistency framework |

### 内层数据 / Inner Data

图元原子数据，是一个 `key-value` 对象，具体的数据内容详见各类型对应的文档。

The atomic primitive data as a `key-value` object. See the corresponding documents for each type for details.

### 单例数据 / Singleton Data

有一些数据在文档内只保留一个，用 `type` 字段就能表示唯一，会省略掉 `id` 字段。

Some data has only one record in a document and is uniquely identified by the `type` field, omitting the `id` field.

```json
{ "type": "META", "ticket": 1 }||{"name": "名称"}|
```

---

## 最终一致性 / Eventual Consistency

- 存储 3.0 中，数据的增删改都是往日志里追加一条记录，因此日志里可能存在多条表示同一数据的数据。
- In Storage 3.0, create/update/delete operations all append a record to the log, so multiple records in the log may represent the same data.

- 最终一致性框架根据 `type`、`id`、`ticket` 字段决定保留哪一条数据。
- The eventual consistency framework decides which record to keep based on the `type`, `id`, and `ticket` fields.

### 相同 type 和 id 的数据 / Records with Same type and id

当 `type` 和 `id` 相同时，对比 `ticket`，保留 `ticket` 更大的记录：

When `type` and `id` are the same, the record with the larger `ticket` is kept:

```json
{ "type": "TYPE", "id": "UUID", "ticket": 1 }||{"data": 1}|
{ "type": "TYPE", "id": "UUID", "ticket": 2 }||{"data": 2}|
```

最终保留 `data` 为 `2` 的记录。
The record with `data` = `2` is retained.

### 相同 type、id 和 ticket 的数据 / Records with Same type, id and ticket

当 `type`、`id`、`ticket` 都相同时，比较文档头的 `client` 字段，`client` 更小的记录被保留。

When `type`, `id`, and `ticket` are all the same, the record with the smaller `client` value in the document header is kept.

```json
{ "type": "DOCHEAD" }||{ "docType": "SCH_PAGE", "uuid": "UUID", "client": "1" }|
{ "type": "TYPE", "id": "UUID", "ticket": 1 }||{"data": 1}|
```

```json
{ "type": "DOCHEAD" }||{ "docType": "SCH_PAGE", "uuid": "UUID", "client": "2" }|
{ "type": "TYPE", "id": "UUID", "ticket": 1 }||{"data": 2}|
```

最终保留 `data` 为 `1` 的记录。
The record with `data` = `1` is retained.

---

## 删除机制 / Deletion

### 原子数据删除 / Atomic Data Deletion

```json
{ "type": "TYPE", "id": "UUID", "ticket": 1 }||""
```

- 数据的删除实际上是将内层数据置为空字符串。
- Deletion actually sets the inner data to an empty string.

### 文档删除 / Document Deletion

```json
{ "type": "DELETE_DOC", "ticket": 1 }||{"isDelete": true}|
```

- `type`：`DELETE_DOC`，文档删除标识 / Document deletion marker
- `isDelete`：是否删除 / Whether deleted

- 文档删除是添加一个删除标识，并不会在日志内直接删除文档数据，以方便文档删除的撤销和数据的一致性维护。
- Document deletion adds a deletion marker rather than removing document data from the log, to support undo and maintain consistency.

- 所有删除的数据都会在日志里有所保留。用户如果要去除工程内删除的数据记录，可以克隆工程以去除这些数据。日志的快照不会清除相关删除的记录。
- All deleted data is retained in the log. To remove deleted data records from a project, clone the project. Log snapshots will not clear deletion records.

---

## 格式分类 / Format Categories

### 工程日志格式 / Project Log Format

描述属于工程但不属于画布的数据。

Describes data belonging to the project but not to a canvas.

- [公共数据 / Common Data](cn/project/common.md) · [English](en/project/common.md)
- [基本信息 / Meta](cn/project/meta.md) · [English](en/project/meta.md)
- [BLOB 真彩图 / True-color Image](cn/project/blob.md) · [English](en/project/blob.md)
- [实例值属性覆盖 / Instance Value Override](cn/project/instance.md) · [English](en/project/instance.md)
- [变体 / Variant](cn/project/variant.md) · [English](en/project/variant.md)
- [元件分组 / Component Group](cn/project/group.md) · [English](en/project/group.md)

### 原理图格式 / Schematic Format

- [通用配置 / Common Configuration](cn/schematic/common.md) · [English](en/schematic/common.md)
- [结构元素 / Structure Elements](cn/schematic/structure.md) · [English](en/schematic/structure.md)
- [属性 / Attributes](cn/schematic/attr.md) · [English](en/schematic/attr.md)
- [导线 / Wires](cn/schematic/wire.md) · [English](en/schematic/wire.md)
- [文本 / Text](cn/schematic/text.md) · [English](en/schematic/text.md)
- [形状 / Shapes](cn/schematic/shape.md) · [English](en/schematic/shape.md)
- [引脚 / Pins](cn/schematic/pin.md) · [English](en/schematic/pin.md)
- [元件 / Components](cn/schematic/component.md) · [English](en/schematic/component.md)
- [对象 / Objects](cn/schematic/obj.md) · [English](en/schematic/obj.md)
- [表格 / Tables](cn/schematic/table.md) · [English](en/schematic/table.md)

### PCB 格式 / PCB Format

- [通用格式 / Common Format](cn/pcb/common.md) · [English](en/pcb/common.md)
- [分区格式 / Partition Format](cn/pcb/partition.md) · [English](en/pcb/partition.md)
- [基础图元 / Primitive Elements](cn/pcb/primitive.md) · [English](en/pcb/primitive.md)
- [焊盘与过孔 / Pads and Vias](cn/pcb/pad_via.md) · [English](en/pcb/pad_via.md)
- [形状图元 / Shape Primitives](cn/pcb/shape.md) · [English](en/pcb/shape.md)
- [二进制对象 / Binary Objects](cn/pcb/obj.md) · [English](en/pcb/obj.md)
- [3D 外壳 / 3D Enclosure](cn/pcb/3d.md) · [English](en/pcb/3d.md)
- [文字体系 / Text System](cn/pcb/text.md) · [English](en/pcb/text.md)
- [属性 / Attributes](cn/pcb/attr.md) · [English](en/pcb/attr.md)
- [尺寸工具 / Dimension Tools](cn/pcb/dimension.md) · [English](en/pcb/dimension.md)
- [封装体系 / Component System](cn/pcb/component.md) · [English](en/pcb/component.md)
- [设计规则 / Design Rules](cn/pcb/rule.md) · [English](en/pcb/rule.md)
- [拼版 / Panelization](cn/pcb/panel.md) · [English](en/pcb/panel.md)

---

## 相关链接 / Links

- 完整格式文档 / Full format documentation：
  - 中文 / Chinese：[cn/index.md](cn/index.md)
  - English / 英文：[en/index.md](en/index.md)
