
﻿前几天接到一个需求，使用特殊的符号\`|\` 来作为字段分隔符，想了半天没搞定，后来在组长得指导下，查阅了项目之前的脚本才搞定了这件事。这给我的启示就是，当项目遇到奇怪的需求时，其实最好参阅以前的代码，因为这些问题很可能同事就已经遇到过了。一般像这种问题，网上是没有答案的。

首先我们看下db2导入导出数据的语法，也就是`export`，`import`，`load`的用法。
- `export`:作导出数据使用，一般的语法也就是`export to filename of filetype select colname from table where xx`，但大家会修改一些字符分割符和编码，格式化啥的，所以语法就稍微复杂一点。
- `import`:作导入数据使用，一般的语法也是`import from filename of filetype insert colname into table where xx`，其他方面和`export`一样。
- `load`: 作装入数据使用，语法较`import`要复杂一些，可以参考db2文档。
详细的可以参考这篇文章[DB2数据的导入（Import）导出（Export）（Load）](http://blog.itpub.net/7899089/viewspace-700191/)

简单的介绍了语法，回到我们的重点来，如何使用特殊符号\`|\`来做字段分割符导出数据呢？

首先我们可以看到db2提供了charsel来作为字段分割符，但这只能使用单字符来做分隔符，可\`|\`是一个字符串，不能这样做，我的做法是，在查询语句中先做预处理，再导出就行了。由于一般是用在脚本里使用db2语句来处理的，这里贴上处理的思路。
1. 得到字段名
colnames = `select colname from syscat.columns where tabname = '${tabname}' and tabschema = '${tabschema}' order by colno`
2. 将字段名，由列转行，并用\`|\`来做分割符，保存在sh变量中
result = `SELECT listagg(colname,`\`|\`)` C FROM ${colnames}`
3. 使用export语句来导出数据
`select  ${result}  as field from "${tabschema}.${tabname}"  where xxx`
由于md的显示问题，实际的代码和这里贴出的不一样，大家可以参考我github上的脚本：[export_data.sh导出数据](https://github.com/ragrok/LearnDB2)

这是导出的处理，导入可以反着来，处理思路是一样的，那就是如何消除特殊分割符。
