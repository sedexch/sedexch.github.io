<h3>Menu</h3>
<a href="/">- Home</a>
<br />
<h3>Client V6</h3>
<a href="/v6/index">- Basics</a>
<a href="/v6/advanced-docker-options">- Advanced Docker Options</a>
<a href="/v6/sedex-client-configuration-options">- Advanced sedex Client Config</a>
<a href="/v6/orchestration">- Kubernetes &amp; Co.</a>
<br />
<h3>Client V5 (Legacy)</h3>
<a href="/v5/index">- Basics</a>


<h3>Menu</h3>
<a href="/">- Home</a>
<br />

{% for item in site.data.toc.toc %}
    <h3>{{ item.title }}</h3>
    {% for entry in item.subfolderitems %}
       <span class="floating-menu-entry" class="{% if item.url == page.url %}active{% endif %}"></span>
       <a href="{{ entry.url }}">- {{ entry.page }}</a></li>
    {% endfor %}
    <br />
{% endfor %}
