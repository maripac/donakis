---
layout: post
title:  "Jekyll Plugins and GitHub Pages"
date:   2014-11-13 19:21:16
categories:
---

I think Ghost and OpenShift is a good solution if you want to host a blog for free. But since I also wanted to test D3 diagrams I came across [Jekyll](http://jekyllrb.com), which is a more of a do-it-yourself kind of thing. It doesn't support writting posts online, but that wasn't something I needed, instead I wanted to be able to load json data from inline javascript inside of posts, work with collections, etc... The other thing was that if you wanted to use your own domain name in OpenShift there is no static IP that you can use. Since every domain must have an A record pointing to a static IP, you can only create a CNAME record with www. or a subdomain. With GitHub Pages there is some wayaround, even if it is not straight away: you can have a CNAME file at the root of your repository, containing yourdomian.com (without www), and then just create two A records with your domain provider: one with the appex @ which defaults to the root, and the other with www pointing to 192.30.252.153 and 192.30.252.154. The solution is further detailed in [stackoverflow](http://stackoverflow.com/a/9123911).

The only problem, when hosting a Jekyll site in GitHub Pages, has to do with the use of Jekyll plugins. But here the solutions are not very clear. In fact there are many different solutions to this problem. I will explain the one that I've found most usefull which was found reading [Your jekyll site hosted on github pages with bower support](http://www.aymerick.com/2014/07/22/jekyll-github-pages-bower-bootstrap.html), but before jumping to the solution, maybe understanding the problem will help.

Jekyll uses [YAML](http://www.yaml.org/) to store its data. The YAML front matter is used in Jekyll to declare global variables in the _config.yml file, and locally at the top of each each post or page, in blocks similar to the one below:

{% highlight livescript %}
---
layout: post
title: Blogging Like a Hacker
---
{% endhighlight %}

That data is retrieved by [Liquid](http://liquidmarkup.org/) which is the template engine runned by Jekyll. When you add to the front matter of some content, a 'layout' variable and assign to it the value 'post', as in the example above, you are telling Liquid to render the content after the YAML front matter block with the file called post.html that is found inside the _layouts folder. The files that are inside the _layouts folder are not static html files, and cannot be rendered by a web browser, they contain normal html code as well as Liquid directives that are written following the Liquid markdown syntax that looks like the code below &mdash;they can also include a YAML front matter block at the top&mdash;.

{% highlight liquid %}
{ % include head.html % }
{% endhighlight %}

A line of code like the previous one would be read and interpreted by Liquid as a placeholder for a block of code that has to be retrieved from a file called head.html that is stored in a folder named _includes. Once the block of code has been retrieved from the corresponding source file the include directive would be removed and substituted by a regular html block. So when you run `jekyll build` or `jekyll serve`, Liquid creates the estatic html files depending on the parameters set in the YAML front matter block. &mdash;The difference between running either of the two previous commands is that unlike `jekyll build` which builds the static site inside the _site folder, running `jekyll serve` will also start a local server in port 4000. That way you can view the actual site by opening your web browser and entering localhost:4000 in the address bar.&mdash; 

**Actually the syntax above is wrong, there should not be any space between the `{` and `%` characters but if I used the right syntax it would render the html block of code. So that is something to note as well, because you can also use Liquid directives inside the markdown of a post.**

So when you install Jekyll and run `jekyll new myproject`, you are installing the source files that contain all the YAML variables and Liquid templating default parameters needed to run a Jekyll site. Those files are modular, human readable, and easy to maintain, scale and migrate. But when it comes to viewing your Jekyll site in a web browser you need to generate the static site hyperlinked, but not human readable. The good part is that with Jekyll you don't need to touch any html or css, because that is generated content. Instead you work in the source folders and each time you add changes, or want to publish a new post you generate the new static code.

If you wanted to host your Jekyll site in an environment that would allow you to `jekyll buil` your site and serve the generated static files from a webserver, you would need a file system running a compatible Ruby installation where you could ssh, edit the source files and then run `jekyll build`. Otherwise, you could host the Jekyll source files in your local system, create new posts locally, build the site locally and the upload them to the webserver. 

In the second case, you'd be tied to a certain computer in order to publish new posts. The first case is a bit resource consuming, and a bit of a rare thing to have to edit your posts with nano or vim. Either way would be quite unsafe, and probably a good advice in any case to track your Jekyll installation with a version management system syncronized with a public repository. 

At this point is when GitHub Pages comes as a great candidate for a hosting solution. Moreover when you find out that GitHub Pages runs Jekyll and can even `jekyll build` your site. Well, to be more precise when GitHub deploys your site to run in the GitHub Pages hosting service that they provide for free the Jekyll engine is runned in safe mode, as in `jekyll build --safe` where the `--safe` flag prevents any arbitrary code from being runned at build time. So when using Jekyll plugins, and since the code contained inside the _plugins folder is custom code and not part of the official Jekyll repository, it will not be allowed to run when your site is being built, so it won't work.     


