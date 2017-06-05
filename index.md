![welcome by Kawhi Leonard from San Antonio Spurs](/assets/img/Kawhi_Leonard.jpg)

## a personal blog

<ul>
{% for post in site.posts %}
<li>
<a href="{{ post.url }}">{{ post.title }}</a>
</li>
{% endfor %}
</ul>

{% for post in site.posts %}
* ({{ post.title }})[{{ post.url }}]
{% endfor %}
