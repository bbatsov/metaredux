---
layout: post
title: Migrating from Disqus to Hyvor Talk
date: 2020-12-07 12:28 +0200
tags:
- Disqus
- Hyvor Talk
- Jekyll
---

For a very long time[^1] I've been using Disqus as the
commenting service for all of my Jekyll blogs.[^2] Today that's
no longer the case.

I've never been particularly happy with Disqus for various reasons:

* The service is tracking its users for marketing purposes
* Their JS payload is huge and slows down your site
* The formatting options they support are limited, especially if you run a blog
focused on programming and people are often discussing code snippets in the comments.

I'm guessing the readers of my blogs were not particularly fond of Disqus either,
as I'd rarely get any Disqus comments, even on articles that would generate a
lot of feedback on third-party platform like Reddit, HN or Lobsters.

**Note:** This [great article](https://fatfrogmedia.com/delete-disqus-comments-wordpress/) digs deeper into all
the issues with Disqus.

I did tolerate them, however, because Disqus is the only commenting
service supported by Jekyll out-of-the-box, and because they were
free. Lately, however, Disqus started to insert some horrible ads in
my blogs automatically, without giving me any options to opt-out of
this in advance. I've managed to disable the ads for a couple of my
sites (based on them being small and non-commercial), but for some
reason I couldn't disable them for Meta Redux and for me this was it.
Obviously I could have paid Disqus to disable the ads, but given my
other concerns about their service I preferred to pay someone else if I had to.

I looked around for [Disqus
alternatives](https://www.andreasrein.net/posts/hyvor-talk-hugo-commenting-systems/)
and several articles suggested the privacy focused [Hyvor
Talk](https://talk.hyvor.com/) as a great option. As a bonus it
supports [Markdown-like markup](https://talk.hyvor.com/docs/formatting) in the comments and works well with code
snippets.

**Note:** Hyvor is a [small
startup](https://talk.hyvor.com/blog/100-paying-customers/) that
reached a 100 paying customers only a month ago. Normally, I'm
cautious about such small companies, but given my very positive
experience with their product I'm feeling optimistic about their
future.

Now, let's the discuss the actual migration process.

## Migration Steps

The migration itself is pretty simple and literally took me 5 minutes.[^3]
The instructions I wrote are for Jekyll in particular, but the process is fairly generic,
so I'm confident the bulk of them are applicable to most cases.

### Export Data from Disqus

First you need to export your comments from Disqus. The process is described in [their docs](https://help.disqus.com/en/articles/1717164-comments-export).
If you're too lazy to read them just go [here](http://disqus.com/admin/discussions/export/) and export the data for each site you have with them individually.

They'll send you the exported data for each site as a gzipped XML via
email. For me this happened almost immediately, but for people running
popular sites with lots of comments the process will probably take
longer.

### Setup Hyvor Account

At this point you have to [sign up for Hyvor Talk](https://talk.hyvor.com?aff=14023), create a site there and import the Disqus data.

The import process is pretty simple, but there are a couple of things to keep in mind:

* You need to upload the export data unzipped (first I tried to upload the export in its gzipped form)
* You have to specify "Absolute URL" as the identifier used by Disqus to map URLs to comments (I've never changed this myself, so I assume that's their default)

After the import is successful you are now ready to enable Hyvor Talk for your Jekyll site.

### Add Hyvor to Jekyll

First, remove any Disqus configuration from your `_config.yml`. Normally you'd have there something like:

``` yaml
disqus:
  shortname: sitename
```

Now you need to add the following HTML snippet to every Jekyll layout that you want Hyvor comments enabled for:

``` html
{% raw %}
<div id="hyvor-talk-view"></div>
<script type="text/javascript">
 var HYVOR_TALK_WEBSITE = YOUR_SITE_ID; // DO NOT CHANGE THIS
 var HYVOR_TALK_CONFIG = {
     url: '{{ page.url | absolute_url }}',
     id: '{{page.url | absolute_url }}'
 };
</script>
<script async type="text/javascript" src="//talk.hyvor.com/web-api/embed"></script>
{% endraw %}
```

That's the trickiest part of the migration process, as you'll have to tweak some Jekyll layouts manually.
Normally, you want comments only for posts, so you'll need to modify the `post.html` layout. The layout files are bundled with
Jekyll's themes so you have to create a `_layouts` directory in your site, and copy the `post.html` from your site's theme (e.g. `minima`) there.
I usually just grab the file from the theme's GitHub repo, you can also get it from the locally installed theme:

```console
$ cd mysite
$ mkdir _layouts
$ bundle info --path minima
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1
$ cd /home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1
$ tree
.
├── LICENSE.txt
├── README.md
├── _includes
│   ├── disqus_comments.html
│   ├── footer.html
│   ├── google-analytics.html
│   ├── head.html
│   ├── header.html
│   ├── icon-github.html
│   ├── icon-github.svg
│   ├── icon-twitter.html
│   ├── icon-twitter.svg
│   └── social.html
├── _layouts
│   ├── default.html
│   ├── home.html
│   ├── page.html
│   └── post.html
├── _sass
│   ├── minima
│   │   ├── _base.scss
│   │   ├── _layout.scss
│   │   └── _syntax-highlighting.scss
│   └── minima.scss
└── assets
    ├── main.scss
    └── minima-social-icons.svg
$ cp /home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_layouts/post.html _layouts/
```

That's it! Now just add the Hyvor snippet to the end of the layout file and publish your updated site.
All of your old comments should be attached to their respective blog posts.

One thing to keep in mind is that by default the Hyvor snippet for Jekyll looks like:

```
{% raw %}
url: '{{ page.url | absolute_url }}',
id: '{{page.id}}'
{% endraw %}
```

This implies that comments are mapped to URLs by using relative URLs. However, the exported data from Disqus was using
absolute URLs.
I needed to change it the default, so the Hyvor IDs would match what Disqus was using in my case (absolute instead of relative page URLs):

```
{% raw %}
url: '{{ page.url | absolute_url }}',
id: '{{page.url | absolute_url }}'
{% endraw %}
```

## Closing Thoughts

I told you that the process was pretty easy. I really regret not doing this earlier, but then again - if I had left
Disqus earlier probably I wouldn't have discovered Hyvor Talk (as it wouldn't have existed). There's always some silver lining.

> If you are not paying for it, you're not the customer; you're the product being sold.

Hyvor Talk doesn't have a free plan, which might be a deal-breaker for some, but I'm more than happy to pay $5/month for
a service that is respecting the privacy of my users and could be used on unlimited number of sites. If you'd like to give Hyvor a try I'd appreciated it if you used my [affiliate link](https://talk.hyvor.com?aff=14023)
to sign up.

That's all I have for you today. Keep hacking!

[^1]: Something like 10 years.
[^2]: I currently have 4 of those.
[^3]: Writing this article took way longer than the actual migration.
