# 使用 Web API 分析器

分析器包会通知你执行以下操作的任何控制器操作：

- 返回未声明的状态代码。
- 返回未声明的成功结果。
- 记录不返回的状态代码。
- 包含显式模型验证检查。

### 引用分析器包

在项目文件中包含 `IncludeOpenAPIAnalyzers` 属性：

```
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

 分析器将状态代码的缺失文档报告为警告。 提供了修复此问题的选项。

