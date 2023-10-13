## 1.Abstract&Info
### 1.1 Abstract
{{abstractNote}}

### 1.2 Info
Authors: {{authors}}
DOI: {{DOI}}
Publication: {{publicationTitle}}
PDF: {{pdfLink}}
Zotero: {{pdfZoteroLink}}
Data: {{date|format("YYYY-MM-DD")}}


## 2. Annotation
> 1. import annotation
{% persist "normal annotation" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
{%- for annotation in newAnnotations -%}
{% if annotation.color == "#e56eee" %}
>>{{annotation.annotatedText}}{{annotation.comment}}
>>{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif -%}
>> [{{"查看原文"}}]({{annotation.desktopURI}})
{% endif %}
{%- endfor -%}
{% endif %}
>>### Imported: {{importDate | format("YYYY-MM-DD h:mm a")}}
{% endpersist %}
---
> 2. normal annotation
{% persist "normal annotation" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
{%- for annotation in newAnnotations -%}
{% if annotation.color == "#aaaaaa" %}
>>{{annotation.annotatedText}}{{annotation.comment}}
>>{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif -%}
>> [{{"查看原文"}}]({{annotation.desktopURI}})
{% endif %}
{%- endfor -%}
{% endif %}
>>### Imported: {{importDate | format("YYYY-MM-DD h:mm a")}}
{% endpersist %}

---

> 3. [[conception]]
{% persist "normal annotation" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
{%- for annotation in newAnnotations -%}
{% if annotation.color == "#ff6666" %}
>>{{annotation.annotatedText}}{{annotation.comment}}
>>{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif -%}
>> [{{"查看原文"}}]({{annotation.desktopURI}})
{% endif %}
{%- endfor -%}
{% endif %}
>>### Imported: {{importDate | format("YYYY-MM-DD h:mm a")}}
{% endpersist %}

---

> 4. incomprehensible annotation
{% persist "normal annotation" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
{%- for annotation in newAnnotations -%}
{% if annotation.color == "#f19837" %}
>>{{annotation.annotatedText}}{{annotation.comment}}
>>{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif -%}
>> [{{"查看原文"}}]({{annotation.desktopURI}})
{% endif %}
{%- endfor -%}
{% endif %}
>>### Imported: {{importDate | format("YYYY-MM-DD h:mm a")}}
{% endpersist %}