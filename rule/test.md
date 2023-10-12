

可用

{%- for annotation in annotations -%}
{% if annotation.color == "#aaaaaa" %}
>>{{annotation.annotatedText}}{{annotation.comment}}
>>{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif %}
{% endif %}
{%- endfor -%}



> 1.normal annotation
{% persist "normal annotation" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
{%- for a in newAnnotations -%}
{% if a.color == "#aaaaaa" %}
>>{{a.annotatedText}}{{annotation.comment}}
>>{%- if a.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif -%}
{% endif %}
{%- endfor -%}
{% endif %}
>>### Imported: {{importDate | format("YYYY-MM-DD h:mm a")}}
{% endpersist %}


> 1.import annotation
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