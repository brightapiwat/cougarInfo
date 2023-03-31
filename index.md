---
permalink: /
---

{% capture readme %}{% include_relative docs/index.html %}{% endcapture %}
{{ readme | markdownify }}