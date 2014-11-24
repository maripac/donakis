---
layout: post
title:  "Jekyll Plugins and GitHub Pages"
date:   2014-11-13 19:21:16
comments: true
categories:
---

I think Ghost and OpenShift combine to make a good solution for a blogging platform. But since I also wanted to test D3 diagrams I came across [Jekyll](http://jekyllrb.com), which is a more of a do-it-yourself kind of thing. It doesn't support writting posts online, but that wasn't something I needed, instead I wanted to be able to load json data from inline javascript inside of posts, work with collections, etc... There was also another inconvenience when using your own domain with an OpenShift application because you are not provided with a static IP, over to which you can point an A record in order for your domain to be resolved to the OpenShift application. Then you are forced to create a CNAME record with the appex www or either use a subdomain. With GitHub Pages there is some way around, even if it is not straight away: you can have a CNAME file at the root of your repository, containing yourdomian.com (without www), and then just create two A records with your domain provider: one with the appex @ which defaults to the root, and the other with www pointing to 192.30.252.153 and 192.30.252.154. The solution is further detailed in [stackoverflow](http://stackoverflow.com/a/9123911).

The only problem, when hosting a Jekyll site in GitHub Pages, has to do with the use of Jekyll plugins. But here the solutions are not very clear. In fact there are many different solutions to this problem. I will explain the one that I've found most usefull which was found reading [Your jekyll site hosted on github pages with bower support](http://www.aymerick.com/2014/07/22/jekyll-github-pages-bower-bootstrap.html), but before jumping to the solution, maybe understanding the problem will help. Still if you prefer, you can jump straight to the [solution](#solution). 

###Some Jekyll notions

Jekyll uses [YAML](http://www.yaml.org/) to store its data. The YAML front matter is used in Jekyll to declare global variables in the _config.yml file, and locally at the top of each each post or page, in blocks similar to the one below:

{% highlight yaml %}
---
layout: post
title: Blogging Like a Hacker
---
{% endhighlight %}

That data is retrieved by [Liquid](http://liquidmarkup.org/) which is the template engine runned by Jekyll. When you add to the front matter of some content, a 'layout' variable and assign to it the value 'post', as in the example above, you are telling Liquid to render that file by mapping its content into the corresponding area of the html template that is named post.html, which is stored in the _layouts folder. The files that are inside the _layouts folder are not static html files, and cannot be rendered by a web browser, they contain normal html code as well as Liquid directives that are written following the Liquid markdown syntax that looks like the code below &mdash;they can also include a YAML front matter block at the top&mdash;.

{% highlight liquid %}
{{ "{% include head.html "}}%}
{% endhighlight %}

A line of code like the previous one would be read and interpreted by Liquid as a placeholder for a block of code that has to be retrieved from a file called head.html that is stored in a folder named _includes. Once the block of code has been retrieved from the corresponding source file the include directive would be removed and substituted by a regular html block. So when you run `jekyll build` or `jekyll serve`, Liquid creates the estatic html files depending on the parameters set in the YAML front matter block. &mdash;The difference between running either of the two previous commands is that unlike `jekyll build` which builds the static site inside the _site folder, running `jekyll serve` will also start a local server on port 4000. That way you can view the actual site by opening your web browser and entering localhost:4000 in the address bar.&mdash; 

<b><s>Actually the syntax above is wrong, there should not be any space between the <code>{</code> and <code>%</code> characters but if I used the right syntax it would render the html block of code. So that is something to note as well, because you can also use Liquid directives inside the markdown of a post.</s></b> <<&mdash; [Display Liquid code in Jekyll](http://truongtx.me/2013/01/09/display-liquid-code-in-jekyll/) 


So when you install Jekyll and run `jekyll new myproject`, you are installing the source files that contain all the YAML variables and Liquid templating default parameters needed to run a Jekyll site. Those files are modular, human readable, and easy to maintain, scale and migrate. But when it comes to viewing your Jekyll site in a web browser you need to generate the static site hyperlinked, but not human readable. The good part is that with Jekyll you don't need to touch any html or css, because that is generated content. Instead you work in the source folders and each time you add changes, or want to publish a new post you generate the new static code.

###Deploying Jekyll to a webserver

If you wanted to host your Jekyll site in an environment that would allow you to `jekyll buil` your site and serve the generated static files from a webserver, you would need a file system running a compatible Ruby installation where you could ssh, edit the source files and then run `jekyll build`. Otherwise, you could host the Jekyll source files in your local system, create new posts locally, build the site locally and finally upload them to the webserver. 

In the second case, you'd be tied to a certain computer in order to publish new posts. The first case is a bit resource consuming, and a bit of a rare thing to have to edit your posts with nano or vim. Either way would be quite unsafe, and probably a good advice in any case to track your Jekyll installation with a version management system synchronized with a public repository. 

###Jekyll with GitHub Pages

At this point GitHub Pages comes as a great candidate for a hosting solution. Moreover when you find out that GitHub Pages runs Jekyll and can even `jekyll build` your site. Well, to be more precise when GitHub deploys your site to run in the GitHub Pages hosting service that they provide for free, the Jekyll engine is runned in safe mode, as in `jekyll build --safe` where the `--safe` flag prevents any arbitrary code from being runned at build time. So when using Jekyll plugins, and since the code contained inside the _plugins folder is custom code and not part of the official Jekyll repository, it will not be allowed to run when your site is being built, so it won't work. 

So far, I think the problem has been laid out. The way around consists in preventing the Jekyll engine at GitHub from building your site, this means that you first build the static files locally and push them over to the gh-pages branch of your repository, excluding from that branch any other Jekyll source files. If GitHub found any of the files that are needed by Jekyll to build your site, and that means files containing Liquid syntax or YAML front matter, it would understand that it needs to run `jekyll build --safe` in order for your repository to be served from the GitHub Pages webserver. That way the code living in the _plugins directory will not be runned at build time, so your site will be accesible from the url you've been assigned by GitHub Pages, but the plugins will not work. 

###Two ways to host content on GitHub Pages

There are two ways in which you can take advantage of the GitHub Pages service. You can check the reference [here](https://help.github.com/articles/user-organization-and-project-pages/). One of them allows users or groups to host one website per user/group, in that case the site would be accesible from the url http://username.github.io/
. The other way allows you to host as many websites as repositories you have with GitHub, but those websites would be published and accesed from the url http://username.github.io/nameofrepository/, unless you configure a custom domain. The first case will not work with the solution that will be explained in more detail. This option requires the name of the repository to match the url that GitHub Pages will assign to your user/organization, as in username.github.io. Which means that the files that GitHub will publish to GitHub Pages will be the ones that are tracked by the master branch. This is not the case when dealing with project pages, since you cannot name all the repositories username.github.io. So the way in which GitHub has automated the request to make use of the GitHub Pages service, requires you to add a branch to your repository and identify that branch under the name gh-pages. This second option allows you to track the source files in the master branch, which is a more rational approach and serve to GitHub the generated static files from the gh-pages branch. 

Otherwise, if you are still bound to the first option you can check out [jekyll-tools](https://www.npmjs.org/package/jekyll-tools) which can be installed as a Node.js module. This tool allows you to build your site normally inside the master branch, then initialize a second one from the master one `$ git branch src`, move to the second one, `$ git checkout src` and run `$ jek deploy` which leaves the master branch free from any files containing Jekyll code. I haven't tested this approach enough so I cannot cover in much detail how to proceed in order to integrate it inside a git workflow.
<a name="solution"></a>

###The way I've managed to setup.

The option that I've been able to implement is the second one, that allowes me to host a website per repository. I've complicated things a bit further in order to be able to generate two estatic sites. One to be hosted in GitHub pages that would serve as a testing/staging environment allowing me to experiment with the implementation of new features, and a second static site configured to live in another host. <s>That way, I can ssh into my vps and clone the third branch with: <code>$ git clone -b third-branch-name https://github.com/username/myrepository.git html</code>.</s>

There is a better solution for managing the deployment into a third production environment, without having to run clones an pulls from that environment. Click here

####Requirements:
The following steps work from a newly created Jekyll installation, or from a Jekyll installation that doesn't yet have an initialized local git repository. If you haven't yet runned `git init` from the root of your repository, you can just delete the _site folder and follow along. If otherwise you are already tracking with git your Jekyll site, I'm not much of a git expert, but in my world of unexpected calamities, I think that instead of deleting the whole .git directory tree, a better solution could be to create a new Jekyll installation. Once you have set up the new project with its own local git index, and synchronized it to a GitHub repository with the following configuration of branches, you can move the source files from the original Jekyll project over to the new one and regenerate your site. 

I understand that you have already installed the jekyll ruby gem, if so open a terminal window, cd to the directory where you usually store your local projects and initialize a new jekyll site with:

{% highlight livescript %}
$ jekyll new mysitename
{% endhighlight %}

To complete the next section you need a fresh new empty GitHub repository. You can create one following the steps indicated [here](https://help.github.com/articles/creating-a-new-repository/) up to the sixth point, remember not to add yet a .gitignore file, as suggested in the fifth step. Once you have completed the previous steps you'll be provided with a ssh or https link that you will need in order to add to your local repository the newly created public one at GitHub as its remote origin. Copy the https or ssh link to your clipboard and back at the terminal go over the next section. It is important that you go over the following steps before running `jekyll build` or `jekyll serve` at all, so I understand that there is no _site folder yet living inside your local files.

{% highlight livescript %}
$ git init
$ git add .
$ git commit -m "Jekyll source files" 
$ git remote add origin git@github.com:username/repositoryname.git
$ git push -u origin master
{% endhighlight %}   

The following section initializes a new orphan branch, which is a branch that doesn't share the history with the master branch, then removes from the new branch the entire git directory tree. Since the branch is left tracking no content at all, &mdash;you still haven't `jekyll build` the static files&mdash; the second part goes over the creation of an index.html file that will work as a placeholder that you can add to your staging area, then commit and finally push. Note here, that instead of pushing it to the master branch, you push to a gh-pages branch.

{% highlight livescript %}
$ git checkout --orphan gh-pages
$ git reset .
$ rm -r *
$ rm .gitignore
$ echo 'Coming soon' > index.html
$ git add index.html
$ git commit -m "init gh-pages"
$ git push -u origin gh-pages
{% endhighlight %} 

I repeated this block after going back to the master branch. But this time I initialized a branch called prod-pages, because in my case I wanted to be able to generate two different static sites.

{% highlight livescript %}
$ git checkout master
$ git checkout --orphan prod-pages
$ git reset .
$ rm -r *
$ rm .gitignore
$ echo 'Coming soon' > index.html
$ git add index.html
$ git commit -m "init prod-pages"
$ git push -u origin prod-pages
{% endhighlight %} 

Since those branches are now live at your GitHub public repository, you can go back to the master branch and map each of them to a folder, just by running a git clone directive with the -b branch-name and the indication of the folder where the clone will copy the files. I mapped the gh-pages clone to crete a _site folder, which is the default folder where Jekyll would build your static files as long as you don't indicate a different destination for your site when running the build command. And the second clone mapped the prod-pages branch to a folder named _site-prod.

{% highlight livescript %}
$ git checkout master
$ git clone -b gh-pages git@github.com:username/repositoryname.git _site
$ git clone -b prod-pages git@github.com:username/repositoryname.git _site-prod
{% endhighlight %} 

Now you can open your .gitignore file. It should already include the _site folder, add a new line that excludes also the _site-prod folder. After adding the folder to the .gitignore directives, you can finally build your site, but for the static files that you want to access from the url that GitHub provides for project pages, you should add in the _config.yml file inside the baseurl variable, the subdirectory part of url where your project will be accesible once uploaded to GitHub Pages. So it shoul look like this `baseurl: "/repositorname"`. Now you can finally build the static files, and push them to the live GitHub repository.

{% highlight livescript %}
$ jekyll build
$ cd _site
$ git add .
$ git commit -m 'generated static files'
$ git push
{% endhighlight %} 

Go back to the root.

{% highlight livescript %}
$ cd ..
{% endhighlight %} 

Open again the _config.yml, remove the subdirectory name previously added to the baseurl, safe the file and repeat the previous steps as follows:

{% highlight livescript %}
$ jekyll build --destination _site-prod
$ cd _site-prod
$ git add .
$ git commit -m 'generated static site-prod'
$ git push
{% endhighlight %}

At this point you can view your site from the browser at the corresponding url. 

###The Workflow

From this point it is very easy to engage with some kind of structured workflow. Maybe taking a look at the distribution of branches can help. If you enter `git branch` git will output the current branch you're at. Whenever you move to the root folder you'll be working in the master branch, and if you cd to the _site folder you'll be working in the gh-pages branch, in the same way cd'ing to the _site-prod would return the prod-pages branch. So instead of jumping from branch to branch you can just move to the corresponding directory to add, commit, and push to the remote site. 

###Update

Ssh to your vps I suppose the git-core is installed. In a Debian system `# apt-get install git-core` would install the corresponding packages. The important thing now is to choose the directory for the  git repository. I've created in it inside the /var  as /var/repo/site.git .

Once you run `git init --bare` you can `ls` to see contents and `cd` into the hooks folder which stores samples that are not yet active of diferent git customizable she-bang scripts that you can get git to launch before of after the execution of git tasks. These tasks are launched by git in response to the commands you run from another environment. Once the two systems have established the appropriate connections.

{% highlight livescript %}
cd /var
mkdir repo
cd repo
mkdir site.git
cd site.git
git init --bare
ls
cd hooks
{% endhighlight %}

Create a new file:

{% highlight livescript %}
nano post-receive
{% endhighlight %}

Enter (matching /var/www/html with your apache virtual host or DocumentRoot of your live site):

{% highlight livescript %}
#!/bin/sh
git --work-tree=/var/www/html --git-dir=/var/repo/site.git checkout -f
{% endhighlight %}

At your local system run `jekyll build --destination _site-prod`, cd to _site-prod. If you want to check just in case, but running git branch at this point shoul return prod-pages. Next you can add a new remote but instead of origin refer it as live.

{% highlight livescript %}
git remote add live ssh://usernamet@vpIPordomain/path/to/repo.git
{% endhighlight %}

Now you want to git add . whichever new additions that branch is holding, git commit -m 'a message'
and the push directive is important to understand it. Because the remote live is now tracking your prod-pages branch as its master branch. So every time you want to update the changes to the production server you must push new commits to the master branch of the server's git repository. That way the bash script will be launched and the files loades to the apache virtual server.  

{% highlight livescript %}
git push live prod-pages:master
{% endhighlight %}
