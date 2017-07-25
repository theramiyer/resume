---
layout: page
title: "Making"
date: "2017-07-25"
subtitle: "Behind the scenes"
---

Building this online résumé was a personal challenge. I wanted to build my first from-scratch site. While it is not really pratical to build pretty much everything from scratch, I took up [Skeleton](http://getskeleton.com/) as a starting point. Having set up a few blogs using [Jekyll](https://jekyllrb.com/), I was confident using it.

I managed to build the site along with building the logic and the layouts almost from scratch, and host the same on GitHub Pages---a pretty standard thing when it comes to Jekyll blogs/sites.

But the real challenge wasn't that.

Granted, I'm not the first one to host an online résumé, nor am I the first to do this complete setup---details of which will follow soon. But that's no reason not to do what I was about to. So here's the behind-the-scenes insight into this site. I'm pretty satisfied with the outcome.

Here's a gist of what happens behind the scenes:

1. I add data to the code (basically Markdown files) as posts. These changes are pushed to BitBucket, which is the control provider I've chosen.
2. Wercker (a Docker-based continuous delivery platform) is configured to pick up the code from BitBucket as soon as something is pushed.
3. Wercker has two pipelines, `build` and `deploy`, which, of course, builds the site using Jekyll, and deploys it to an AWS&nbsp;S3 bucket.
4. CloudFront is the CDN that delivers the contents of the S3 bucket.
5. Amazon's Certificate Manager provides the SSL certificate that encrypts your connection to this site.
6. WordPress is my DNS provider---I basically bought this domain from them for blogging, but it can be used with AWS.

Now to the detailed picture.

##### Setting up Jekyll

I use a Windows&nbsp;10 Pro computer. I enabled the Linux Subsystem for Windows feature on it, and installed the necessary components required to be able to build sites using Jekyll.

Yes, there's a less-complicated way to set up Jekyll, but trust me, setting it up with WSL is much less hassle. [Dave Rupert's post](http://daverupert.com/2016/04/jekyll-on-windows-with-bash/) helps a great deal with it. Oh yeah, some of the [Nokogiri stuff](http://daverupert.com/2016/06/ruby-on-rails-on-bash-on-ubuntu-on-windows/) seems to have been missed in the former post.

That settles the Jekyll part.

##### Building the site locally

Now I used the [Skeleton](http://getskeleton.com/) boilerplate to build the site. That gives me a near-from-scratch experience: it covers all (and only) the necessary parts of a website. There are two ways to go forward from here:

1. Use the `jekyll new` command to get the basic Jekyll site, and then add the Skeleton components.
2. Use the [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll) and rebuild `index.html` and move forward.

I chose the latter. Sure, I had to still make a few changes to optimise the site, but hey, what's life without adventures?

I wouldn't go into the logic of how the site was built. The home page basically shows the excerpt of each of the sections.

##### Pushing the repository to BitBucket

##### Configuring the Amazon&nbsp;S3 bucket

##### Configuring Wercker to build and deploy the site to Amazon&nbsp;S3

##### Configuring CloudFront to deliver the site

##### Configuring DNS routing
