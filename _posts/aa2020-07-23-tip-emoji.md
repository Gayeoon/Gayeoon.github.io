---
layout: post
title:  "Welcome to Gayeon's Blog!"
subtitle:   "마크다운 이모티콘"
categories: tip
tags: Blog welcome
comments: false
---

## Welcome!!

블로그 개설!

### Code Snippets

코드를 넣으려면 [highlight.js][highlight]를 사용한다.
`{% raw %}{% highlight <language> %}{% endraw %}` 태그 안에 코드를 넣는다!

For Example...
{% highlight java %}
System.out.println("Hello World!");
{% endhighlight %}

<br>
<hr>
### Images

이미지를 넣으려면 

<code>&lt;a&gt;</code> 태그 안에 <code>&lt;img&gt;</code> 태그를 넣는다.
<code>data-lightbox</code>와 <code>data-title</code> 속성도 추가한다. 

For Example...
<a href="//bencentra.com/assets/images/falcon9_large.jpg" data-lightbox="falcon9-large" data-title="이미지 예시">
  <img src="//bencentra.com/assets/images/falcon9_small.jpg" title="Check out the Falcon 9 from SpaceX">
</a>

<br>
<hr>
### Emojis

|Emoji|Code|Emoji|Code|Emoji|Code|
|:---:|:---:|:---:|:---:|:---:|:---:|
|&#128516;|`&#128516;`|&#128582;|`&#128582;`|&#10060;|`&#10060;`|
|&#128519;|`&#128519;`|&#128581;|`&#128581;`|&#10071;|`&#10071;`|
|&#128518;|`&#128518;`|&#128586;|`&#128586;`|&#10067;|`&#10067;`|
|&#128521;|`&#128521;`|&#9995;|`&#9995;`|&#127802;|`&#127802;`|
|&#128536;|`&#128536;`|&#9996;|`&#9996;`|&#127803;|`&#127803;`|
|&#128532;|`&#128532;`|&#9997;|`&#9997;`|&#127813;|`&#127813;`|
|&#128543;|`&#128543;`|&#128073;|`&#128073;`|&#127812;|`&#127812;`|
|&#128544;|`&#128544;`|&#127873;|`&#127873;`|&#127850;|`&#127850;`|
|&#128580;|`&#128580;`|&#128073;|`&#128073;`|&#128149;|`&#128149;`|
|&#128561;|`&#128561;`|&#128079;|`&#128079;`|&#128150;|`&#128150;`|
|&#128545;|`&#128545;`|&#127912;|`&#127912;`|&#128276;|`&#128276;`|
|&#128552;|`&#128552;`|&#10024;|`&#10024;`|&#128226;|`&#128226;`|
|&#128549;|`&#128549;`|&#127881;|`&#127881;`|&#127844;|`&#127844;`|
|&#128551;|`&#128551;`|&#127942;|`&#127942;`|&#127849;|`&#127849;`|
|&#128559;|`&#128559;`|&#127984;|`&#127984;`|&#127866;|`&#127866;`|
|&#128565;|`&#128565;`|&#128150;|`&#128150;`|&#128293;|`&#128293;`|
|&#128577;|`&#128577;`|&#10025;|`&#10025;`|&#128036;|`&#128036;`|
|&#128164;|`&#128164;`|&#10084;|`&#10084;`|&#128162;|`&#128162;`|
|&#128166;|`&#128166;`|&#10140;|`&#10140;`|&#128165;|`&#128165;`|


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
[highlight]:   https://highlightjs.org/
[lightbox]:    http://lokeshdhakar.com/projects/lightbox2/
[jekyll-archive]: https://github.com/jekyll/jekyll-archives
[liquid]: https://github.com/Shopify/liquid/wiki/Liquid-for-Designers

<script>
window.tooltips = window.tooltips || []
window.tooltips.push(['#someId', { content: "This is the text of the tooltip!" }])
window.tooltips.push(['#someOtherId', { content: "{% include tooltips/example.html %}", placement: "right" }])
</script>
