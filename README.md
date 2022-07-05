# Windows环境构建、安装pgvector插件

## 环境准备
与[从源码安装PostgreSQL到Windows相同](https://www.postgresql.org/docs/current/install-windows-full.html)
1. Visual Studio 2015-2022
2. MSVC V140
3. Windows SDK 8-10

### 跟据已有代码创建项目与解决方案
项目参数调整
1. 项目更名为vector，最终文件输出为vector.dll
2. 打开项目属性页面，选择所有配置，所有平台，同时修改release和debug的参数
### 项目属性-配置属性-常规
|项目 | 值|
|--- | ---|
|目标平台版本 | 跟据环境选择“8-10”|
|平台工具集 | 选择“Visual Studio 2015 (v140)”（此为PostgreSQL 14使用的MSVC版本）|
|配置类型 | 选择“动态库(.dll)”|
### 项目属性-配置属性-C/C++ 
|项目 | 值|
--- | ---|
| 常规-附加包含目录 | C:\Program Files\PostgreSQL\14\include\server\port\win32_msvc|
| | C:\Program Files\PostgreSQL\14\include\server\port\win32|
| | C:\Program Files\PostgreSQL\14\include\server|
| | C:\Program Files\PostgreSQL\14\include|
| |（跟据实际情况调整，其中14是版本号，可能不同）  |
| 代码生成 | 启用C++异常 选择“否”|
| 高级 - 编译为 | 选择“编译为 C 代码 (/TC)”|
### 项目属性-配置属性-链接器
|项目 | 值|
|--- | ---|
|常规 - 附加库目录 | 填入“C:\Program Files\PostgreSQL\14\lib”（路径问题同上）|
|清单文件 - 生成清单 | 选择“否 (/MANIFEST:NO)”|
|输入 - 附加依赖项 | 填入"C:\Program Files\PostgreSQL\14\lib\postgres.lib"（路径问题同上）|

### 代码微调（项目全文替换）
1. PG_FUNCTION_INFO_V1 前添加 PGDLLEXPORT
2. void _PG_init 前添加 PGDLLEXPORT

### 构建插件库
1. 选择Release、X64，生成解决方案。生成vector.dll文件。
2. \sql\vector.sql 重命名为 vector--0.2.6.sql
3. 复制文件
- vector.dll -> C:\Program Files\PostgreSQL\14\lib（路径问题同上）
- vector.control -> C:\Program Files\PostgreSQL\14\share\extension
- \sql\目录下所有.sql文件 -> C:\Program Files\PostgreSQL\14\share\extension
### 安装、测试
查询分析工具中执行，安装完毕。
```sql
CREATE EXTENSION vector;
```
更多测试参考主版本[README](https://github.com/pgvector/pgvector#readme)

参考资料

- [Installation from Source Code on Windows](https://www.postgresql.org/docs/current/install-windows.html)
- [Postgresql on Windows and custom extensions](https://dba.stackexchange.com/a/196080)
- [Compiling PostgreSQL extensions with Visual Studio on Windows](https://www.2ndquadrant.com/en/?p=9215)
