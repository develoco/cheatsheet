# Traverse a data sub-directory

We can  generate lists from files inside `data/dl` folder:

```markdown
---
layout: invoices
---
<ul>
{% comment %}
{% for invoice_hash in site.data.dl %}
{% assign invoice = invoice_hash[1] %}
  <li>
    <a href="dl/{{ invoice_hash[0] }}">Invoice #{{invoice_hash[0]}}</a>
  </li>
{% endfor %}
{% endcomment %}
</ul>
```
