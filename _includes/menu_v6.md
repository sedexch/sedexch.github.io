<h3>Menu</h3>
<a href="/">- Home</a>
<br />

{% for item in site.data.toc.toc %}
    <h3>{{ item.title }}</h3>
    {% for entry in item.subfolderitems %}
       <span class="{% if entry.url == page.url %}active{% endif %}">
          <a href="{{ entry.url }}">- {{ entry.page }}</a>
       </span>

       (Entry.url={{ entry.url }})
       (Page.url={{ page.url }})
    {% endfor %}
    <br />
{% endfor %}
