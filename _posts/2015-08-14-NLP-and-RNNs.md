---
layout: post
titile: NLP and RNNs
html_name: NLP_and_RNNs.html
---

{% capture includeGuts %}
{% include {{ page.html_name }} %}
{% endcapture %}
{{ includeGuts | replace: '<!DOCTYPE html>', ''}}
