---
layout:     post
title:      Greet Blog
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
multilingual: true
tags:
    - Other
---

<!-- English Version -->
<div class="zh post-container">
    {% capture about_en %}{% include posts/2017-08-16-hello/en.md %}{% endcapture %}
    {{ about_en | markdownify }}
</div>

<!-- Chinese Version -->
<div class="en post-container">
    {% capture about_zh %}{% include posts/2017-08-16-hello/zh.md %}{% endcapture %}
    {{ about_zh | markdownify }}
</div>


