---
layout: post
title:  "Jekyll Plugins and GitHub Pages"
date:   2014-11-13 19:21:16
categories:
---

I think Ghost and OpenShift is a good solution if you want to host a blog for free. But since I also wanted to test D3 diagrams I came across [Jekyll](http://jekyllrb.com), which is a more of a do-it-yourself kind of thing. It doesn't support writting posts online, but that wasn't something I needed, instead I wanted to be able to load json data from inline javascript inside of posts, work with collections, etc... The other thing was that if you wanted to use your own domain name in OpenShift there is no static IP that you can use. Since every domain must have an A record pointing to a static IP, you can only create a CNAME record with www. or a subdomain. With GitHub Pages there is some wayaround, even if it is not straight away: you can have a CNAME file at the root of your repository, containing yourdomian.com (without www), and then just create two A records with your domain provider: one with the appex @ which defaults to the root, and the other with www pointing to 192.30.252.153 and 192.30.252.154. The solution is further detailed in [stackoverflow](http://stackoverflow.com/a/9123911).

The problem, when hosting a Jekyll site in GitHub Pages, comes when you need to use Jekyll Plugins. But here the solutions are not very clear. In fact there are many different solutions to this problem. I will explain the one that I've found most usefull which was found reading [Your jekyll site hosted on github pages with bower support](http://www.aymerick.com/2014/07/22/jekyll-github-pages-bower-bootstrap.html), but before jumping to the solution, maybe understanding the problem will help.

Jekyll uses [YAML](http://www.yaml.org/) to store its data. That data is retrieved by [Liquid](http://liquidmarkup.org/) which is the template engine runned by Jekyll. When you run `jekyll build` or `jekyll serve`, Liquid goes and processes the files containing the YAML front matter needed to create the static html files. The difference between running one of the two commands mentioned is that unlike `jekyll build` which builds the static site inside the _site folder, running `jekyll serve` will also start a local server in port 4000 so that you can view the site by opening your web browser and entering localhost:4000 in the address bar. 


There are 
The YAML front matter allows you to declare global variables in the _config.yml file, and locally at the top of each each post or page, in blocks similar to the one below:

{% highlight livescript %}
---
layout: post
title: Blogging Like a Hacker
---
{% endhighlight %}






 

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve --watch`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
