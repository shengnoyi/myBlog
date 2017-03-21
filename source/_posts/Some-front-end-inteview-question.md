---
title: Solution for some front end inteview question
date: 2017-03-13 17:20:56
tags: 前端开发
---
参考资料：  
[Front-end-Developer-Interview-Questions](https://github.com/h5bp/Front-end-Developer-Interview-Questions#html-questions)

### HTML Questions:
- What does a doctype do?
- What's the difference between full standards mode, almost standards mode and quirks mode?
- What's the difference between HTML and XHTML?
- Are there any problems with serving pages as application/xhtml+xml?
- How do you serve a page with content in multiple languages?
- What kind of things must you be wary of when design or developing for multilingual sites?
- What are data- attributes good for?
- Consider HTML5 as an open web platform. What are the building blocks of HTML5?
- Describe the difference between a cookie, sessionStorage and localStorage.
- Describe the difference between `<script>`, `<script async>` and `<script defer>`.
	- `<script>` stops rendering process, and download and run a script.
	- `<script async>` don't stop rendering process while asynchronously downloading a script. When finishing download, it stops rendering and runs the script.
	- `<script defer>` don't stop rendering process while asynchronously downloading a script. When finishing rendering, it runs the script.
- Why is it generally a good idea to position CSS `<link>`s between `<head></head>` and JS `<script>`s just before `</body>`? Do you know any exceptions?
- What is progressive rendering?
- Have you used different HTML templating languages before?