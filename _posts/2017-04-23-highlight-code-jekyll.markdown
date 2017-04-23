---
published: true
title: Code highlighting in Jekyll blog using highlight.js
layout: post
---

Adding code block in Jekyll blog should be easy. And though highlighting in markdown using back-ticks ``` worked but it didnt work as well as it should. The formatted java code was getting messed up as the lines were getting wrapped. I needed well formatted code with scrolling which should be easy to read.
 
This is where highlight.js came to rescue. There were other options and hacks but this was the clear winner because it was easy to integrate, supported many languages and comes with color themes.

- Only 1 step is needed. Add highlight js and css of the color scheme you want in the header page. Either you can add cdn url as shown below or copy it in your project and give local path. This will find and all highlight the code inside of _\<pre\>\<code\>_ tags

~~~ xml
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.11.0/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.11.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
~~~

- highlight.js provide many color schemes and all you have to do is choose the right css and refer its path.

- <a href="https://cdnjs.com/libraries/highlight.js/" target="_blank">here</a> is the list of cdn links of all color schemes

- <a href="https://github.com/isagalaev/highlight.js/tree/master/src/styles" target="_blank">here</a> is github link of all color schemes

- <a href="https://highlightjs.org/static/demo/" target="_blank">here</a> is the demo for all supported languages and schemes

