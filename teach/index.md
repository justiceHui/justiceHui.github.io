---
layout: page
title: 과외 합니다
---

<link rel="stylesheet" href="../assets/css/opensource.css">
<div class="row">
	{% for item in site.data.teacher.defaults %}
	<div class="col-sm-6">
		<div class="card">
			<div class="card-body">
				<h2 class="card-title" style="margin:10px 0 0 0;"><a href="{{ item.intro_url }}">{{ item.name }}</a></h2>
				<h6 class="card-subtitle mb-2 text-muted" style="margin-top:10px">{{ item.title }}</h6>
				<p class="card-text">{{ item.content }}</p>
				<p class="post-tags">
					{% for tag in item.tags %}
					{% assign tag_url = '/tags/' | append: tag %}
						{% for tm in site.data.member.tagmap %}
							{% if tag == tm.tag %}
								{% assign tag_url = tm.url %}
							{% endif %}
					    {% endfor %}
					<a href="#" title="opensource" class="tag tag-opensource" style="text-decoration: none">{{ tag }}</a>
					{% endfor %}
				</p><br/>
				<a href="{{ item.intro_url }}" class="btn btn-primary" style="color:#fff; text-decoration:none;">> Introduce</a>
        {% if item.BOJ %}
        <a href="{{ item.BOJ }}" class="btn btn-primary" style="color:#fff; text-decoration:none;">> BOJ</a>
        {% endif %}
				<br><br><br>
        {% if item.Codeforces %}
        <a href="{{ item.Codeforces }}" class="btn btn-primary" style="color:#fff; text-decoration:none;">> Codeforces</a>
        {% endif %}
				{% if item.blog_url %}
				<a href="{{ item.blog_url }}" class="btn btn-primary" style="color:#fff; text-decoration: none;">> Blog</a>
				{% endif %}
			</div>
		</div>
	</div>
	{% endfor %}
</div>
