---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<ul class="site__posts-list">
  {% for post in site.posts %}
    <li class="site__posts-list-item">
      <article>
        <header>
          <h1>
            <a href="{{ post.url }}">{{ post.title }}</a>
          </h1>
        </header>
        <section>
          {{ post.excerpt }}
        </section>
      </article>
    </li>
  {% endfor %}
</ul>
