




{% for t in creators %}{{t.firstName}}{{t.lastName}}{{t.name}}{% if not loop.last %}, {% endif %}{% endfor %}

 {% if date %}{{date | format("YYYY-MM")}}{% endif %}


{% for annotation in annotations %}
{{annotation.annotatedText}}{{annotation.comment}}
{% if annotation.imageBaseName %}![[{{annotation.imageBaseName}}]]{% endif %}
{% endfor %}
