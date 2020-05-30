# The Blog
## 04/23/2020
I finally got around to implementing the part of the website I wanted the most, the blog. As of right now the blog works via loading in markdown files from my static/blog_posts folder. Afterwards the backend then lays the md file out as some nice html like so:
```html
<h1> nice header here </hi>
<h2> I'd probably put the date here</h2>
<pre> <code> code block </pre> </code>
```
followed by loading it into the template and serving it to the user.

It works fairly well, although I can't add my own classes to the paragraph tags and the like in the actual post\'s HTML outout, meaning that I either 
need to make the blog posts have a container tag around it (in my case a ul), then access the tag I want through the container in css.
(Think something like `.class_name > li > tag.`)

The website itself seems really sparse when it comes to the blog page. Styling is not finished however we\'ll get to that hopefully soonish. 
Overall I\'m liking the architecutre/design Idea I had for using .md files, it makes everything a lot more convenient then otherwise.
Development isn\'t *too* much harder and it\'s mostly manageable so far.

The one down side is while pulldown-cmark (my markdown parser) is blazingly fast, it isn\'t as powerful as some of the alternatives. 
It dosen\'t support all of the extensions on markdown and the like, although so far that dosen\'t seem to be a problem for something as simple as a blog. 
The furthest I\'d ever use is likely a table.

<small>*Burnout sucks, I've had it for like half a week straight...*</small>
