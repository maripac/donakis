---
layout: post
title: "Bourbon, Neat and Bitters installation"
comments: true
date: 2014-09-12 19:17:00
categories: 
---

This site runs with [Jekyll](http://jekyllrb.com/). I modified the base Jekyll theme in order to have some control over the behaviour of the site's layout throughout the widest possible range of screen sizes. The [Neat](http://neat.bourbon.io/) library framework developed at [Thoughtbot](http://thoughtbot.com/) on top of [Bourbon](http://bourbon.io/), is a great option that allows you to create truly semantic grid systems. By adding [Bitters](http://bitters.bourbon.io/) you can setup a uniform baseline grid, and control the distribution of elements on the y-axis. This way the vertical rythm of the layout would allow you to regulate the eventual disarrangements that might take place in the vertical axis derived from the redistribution of elements, depending on the screen sizes of the devices that request them. For example, when the device allowes to redistribute in different columns additional content, the baseline adjustment will help matching the outest top and bottom edges of the containers side by side with the inner pattern of falling line heights inside the primary container. 

The [Bourbon](http://bourbon.io/) library is built for [Sass](http://sass-lang.com/). It can be installed to work with [Compass](http://compass-style.org/) as well, but maybe you need to update your Compass version to 1.0.0 or newer. Previous releases of Compass do not support Sass 3.3 which is needed if you want to install the current version of the Bourbon gem. With that in mind, you might want to check which version is installed in your machine, before installing or updating any ruby gem. Just type `$ compass version` from the command line and the output should contain which version of the Compass gem you are running.  

###Not found

If the command returned a not found argument, you don't need to install Compass at all to work with Bourbon. I'm not going to do it myself in this case, so apart from the configuration and installation of the compatible Ruby gems, I won't cover any other aspect of the Compass workflow. And I must admit also that I do not see many reasons to combine these two frameworks. One of the greatest advantages I've found when working with Bourbon over Compass is the unobtrusive control and freedom that the framework provides you over any type of layout. It doesn't tie you to floating positioned layouts, or any other kind of inmutable approach for building your site. However and apart from the fact that I find myself most confortable when using a set of tools instead of another, I am aware that Compass is, and has been for some time, the framework most widely used to deploy Sass preprocessed stylesheets. This gives me a slight idea of the volume of projects that might depend on older versions of Sass, so I think that it's always a good thing to document the easy steps that can avoid you from having to troubleshoot errors.  Most of the time is a simple thing: you launch a new project, reconfigure your environment, go back to an older project and find something went wrong, check the syntax, downgrade packages, etc... But this can become a little time-consuming. 

###Compass 1.0.0
Another possibility is that `$ compass version` returned 1.0.0 or newer. In that case you are safe to go and install the Bourbon gem with `$ gem install bourbon`. 

###Older Compass (needed for this project)

If you need to run Bourbon with an older version of Compass or Sass you need to install bourbon 3.2.x. So instead of just typing <code>$ gem install bourbon</code> which defaults to the latest stable release you can specify a version as in <code>$ gem install bourbon -v 3.2.3</code> . In the case of Neat the release that is compatible with Sass 3.2 is neat 1.5.1 so running <code>$ gem install neat -v 1.5.1</code> will do. Bitters can be installed normally with <code>$ gem install bitters</code> since the gem is compatible with versions of Sass starting from Sass 3.0.0.


+ [Bourbon](https://github.com/thoughtbot/bourbon#requirements)
+ [Neat](https://github.com/thoughtbot/neat#requirements)
+ [Bitters](https://github.com/thoughtbot/bitters#requirements)

###Older Compass (needed for different projects)

If the previous command returned Compass 0.12.6 or similar, but you are starting with a new project that does not require an old version of Compass, chances are that you might still need to run an old Compass projects that depends on Compass 0.12.6 and Sass 3.2. If this is the case but you still update your installation, you won't notice any incompatibility issue untill you run an old Compass project on Sass 3.3, which will prompt you with constant deprecation warnings and syntax errors.

When collaborating on projects that require a group of people to work on the same files and track them with some kind of version control system this is a common problem. <a href="http://bundler.io/">Bundler</a> is a tool that helps solving ruby dependencies. It can be installed as a Ruby gem with <code>$ gem install bundler</code> and works in a similar way as other package management systems. If you want to specify a set of gems for a specific project, open a terminal session and cd to the root of your project, then just enter <code>$ bundle init</code> and you will find a newly created Gemfile, that you can open with read/write permissions <code>$ sudo vi Gemfile</code>. Add there the required dependencies, following the syntax below:

{% highlight livescript %}
gem 'compass', '~>1.0.0'
gem 'sass'
gem 'bourbon'
gem 'neat'
gem 'bitters'
{% endhighlight %}

This file works in Ruby in a similar way the packaje.json works inside a Node.js project. As in a Node.js project running <code>$ npm install</code> would launch npm, –node package manager– to start the installation of the dependencies especified in the package.json; with Bundler you'd have to run <code>$ bundle install</code>. This will launch the installer and solve any conflicts when matching your global Ruby installation to your local especification. Once the installation has completed you'll find a new file Gemfile.lock that contains a complete reference to the installation performed by Bundler. Add it to the staging area of your repository, so it is tracked next time you commit your changes. <code>$ git add Gemfile.lock</code>

The last thing to comment in relation to Compass is that when installed with Bundler, to be sure that you are running the correct Compass version, you should run `$ bundle exec compass watch` instead of `$ compass watch`. 

To start using the Bourbon stack of tools run `$ bundle exec bourbon install --path=path/to/sass` ,  `$ bundle exec neat install --path=path/to/sass` and  `$ bundle exec bitters install --path=path/to/sass`. Each of these three commands will create a folder containing the source files. It is important that you don't make any modifications to these files. They contain the default parameters used by each of these tools, but since the variables are declared with the following sass syntax:

{% highlight scss %}
  $variable-option: value !default; 
{% endhighlight %}

The !default termination means that if the variable has no value assigned, it should default to the one especified here. So keeping these three folders untouched will keep you from having to commit them to a repository. Since Bundler has created a Gemfile.lock with the specific versions your project is running, anyone wanting to work on the same project would only have to clone the repository, run `$ bundle install` and the three bundle exec commands mentioned above. 
