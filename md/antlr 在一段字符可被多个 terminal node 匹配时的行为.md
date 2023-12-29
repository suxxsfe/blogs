考虑下面一段 antlr 语法  

```  
STRING: [a-zA-Z0-9]+;
NUMBER: [0-9]+;
NEWLINE: '\r'? '\n';

root: id title EOF;

id: 'id:' NUMBER NEWLINE;
title: 'title:' STRING NEWLINE;
```  

我们希望 `id:` 后面只存在数字，而 `title:` 后买可存在数字或字母，因而定义了 `NUMBER` 和 `STRING` 两个 terminal node 规则  
然而这一段语法并不能正常工作  

这就需要了解 antlr 解析的流程，其中与我们这个问题相关的有这两部分：  

- lexing：将输入的文本解析为一个个 token，进而构建一个 token stream 来作下面的处理
- parsing：将 token 构建成 parse tree  

对于 lexing：  

- 所有以大写字母开头规则对应着 parse tree 中的 terminal node，有且只有它们被用来进行 lexing，这些规则是 lexer rule
- antlr 会找到一个以当前的位置为起点、极长的、满足任意 lexer rule 的字符串，也就是如果将找到的这个字符串长度再加一，那么不存在任何 lexer rule 
- 如果只有一个 lexer rule 能和这个字符串匹配，那么本次匹配找到的 token 自然就是这个 lexer rule 对应的 token
- 如果有多个，那么找到的 token 是**按照语法文件定义顺序第一个** lexer rule 对应的 token  

因此，`title: abc12345` 的 `abc12345` 可以被 `STRING` 匹配，而 `id: 12345` 仍然会被 `STRING` 匹配，因为 `STRING` 被定义在 `NUMBER` 前面。而 `id` 这条规则又要求匹配一个 `NUMBER`，这样就造成了错误  
如果把 `NUMBER` 和 `STRING` 的顺序调换，那么 `title: 12345` 又会匹配到 `NUMBER`，依然存在错误  

正确做法：  

- 让所有 lexer rule 不交，转而使用非终止节点对应的规则，并允许其匹配多个 lexer rule，例如：`string: (LETTER | DIGIT)+; number: DIGIT+;`
- 将规则分散到多个语法文件中，再导入  


