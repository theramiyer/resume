---
layout: page
title: "Making"
category: blog
date: "2017-07-25"
subtitle: "Behind the scenes"
permalink: '/making.html'
---

Building this online résumé was a personal challenge. I wanted to build my first from-scratch site. While it is not really practical to build pretty much everything from scratch, I picked [Skeleton](http://getskeleton.com/) boiler plate. Having set up a few blogs using [Jekyll](https://jekyllrb.com/), the site generator, I was confident using Jekyll. So [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll) became the starting point. After spending a couple of hours in setting up the logic to programmatically generate the site from the code, the basic setup was ready.

As an Infrastructure person, setting up the site on the cloud intrigued me---this is the first time I'm doing this. Here's a gist of what happens behind the scenes:

1. I add data to the code (basically Markdown files) as posts. These changes are pushed to BitBucket, the source control provider for the site.
2. Wercker (a Docker-based continuous delivery platform) is triggered as soon as there's a push; it picks up the code from BitBucket.
3. Wercker has two pipelines, `build` and `deploy`, which, of course, builds the site using Jekyll (on a Ruby box), and deploys it to the AWS&nbsp;S3 bucket that hosts the site.
4. CloudFront is the CDN that delivers the contents of the S3 bucket.
5. Amazon's Certificate Manager provides the SSL certificate that encrypts your connection to this site.
6. WordPress is my DNS provider---I basically bought this domain from them for blogging, but it can be used with AWS.

Now to the detailed picture. (This part has been published as a GitHub Gist as well.)

**Setting up Jekyll**

This document has been created for Windows&nbsp;10 Pro. While this is not entirely necessary, it helps when you need to build the site locally to test out its working.

Enable the [Linux Subsystem on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) feature, and install the necessary components required to be able to build sites using Jekyll.

While there may be seemingly less-complicated ways to set up Jekyll, setting it up with WSL is much less hassle. [This post](http://daverupert.com/2016/04/jekyll-on-windows-with-bash/) by Dave Rupert helps a great deal with it. Also, some of the [Nokogiri binaries](http://daverupert.com/2016/06/ruby-on-rails-on-bash-on-ubuntu-on-windows/) need to be installed.

**Building the site locally**

There are really many ways to do this, the easiest way being to fork one of the existing templates and going forward by simply customising the content.

I wanted to build an uncomplicated site, so I used the [Skeleton](http://getskeleton.com/) boilerplate to build the site. There are two ways to go forward from here:

1. Use the `jekyll new` command to get the basic Jekyll site, and then add the Skeleton components.
2. Use the [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll) and rebuild `index.html` and move forward.

I chose the latter. Sure, I had to still make a few changes to optimise the site, but hey, what's life without adventures?

**Pushing the repository to BitBucket**

**Configuring the Amazon&nbsp;S3 bucket**

**Configuring Wercker to build and deploy the site to Amazon&nbsp;S3**

**Configuring CloudFront to deliver the site**

**Configuring DNS routing**
