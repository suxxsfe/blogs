尝试写 [mooc 自动播放脚本时](https://github.com/suxxsfe/mooc-autoplayer)，页面的某元素始终无法 get 到  
一般听到这种问题都会想加上 `window.onload=function(){}` 解决，但实际上比没有作用  

一个现象是如果手动在 dev tool 中对该元素审查，则可以正常 get 到  
原因在于元素被包含进了 `<ifram></iframe>` 标签中  

iframe 用于为页面嵌入另一个 html 文档  
而想对这个被嵌入的文档的元素进行操作，需要先 get 这个 iframe 元素  
浏览器会为每个页面创建一个 window，每个被嵌入的页面也是  
所以用 `_frame.contentWindow` 来获取这个被嵌入的页面的 window  
而他的 dom 就可以用 `_frame.contentWindow.document` 访问  

具体的：  

```javascript  
var _frame=document.getElementById("myIframe");
var _document=_frame.contentWindow.document;
var _div=_document.getElementById("myDiv");
```  


