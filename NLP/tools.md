<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-03-04 13:45:21
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-03-04 13:45:22
-->

# NLP
一些NLP中使用的工具

## 上线部署
- 
嵌入到普通网页的div中,ref:https://discuss.huggingface.co/t/gradio-iframe-embedding/13021/2
```html
<html>
<head>
<link rel="stylesheet" href="https://gradio.s3-us-west-2.amazonaws.com/2.6.2/static/bundle.css">
</head>
<body>
<div id="target"></div>
<script src="https://gradio.s3-us-west-2.amazonaws.com/2.6.2/static/bundle.js"></script>
<script>
launchGradioFromSpaces("abidlabs/question-answering", "#target")
</script>
</body>
</html>
```