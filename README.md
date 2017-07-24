# Skeleton Résumé

A simple, elegant online résumé created using an [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll), an implementation of Dave Gamache's Skeleton framework in Jekyll. The original Skeleton framework and docs are available from http://getskeleton.com/.

Feel free to fork the repository to create your own résumé, either hosted via GitHub pages, or your own domain.

## Getting Started

First things first: modify the data in `config.yml` to make the site your own (after forking it, of course).

### Structure

All your data should go to `_posts`; each entry in the résumé is a post. [Markdown Writer](https://atom.io/packages/markdown-writer) would help you a lot with this (markdown-writer config is included).

Ensure that each of your posts has the following frontmatter:

``` YAML
---
layout: "post"
title: "Name of the institution/organisation"
subtitle: "What you did at the organisation"
date: "2000-01-01" # Preferably the day you started
tenure: "2007 – 10" # The tenure for which you were part of the organisation
category: "Section name goes here"
---
```

Ensure that you have very few and specific categories (categories can contain spaces, but must be consistent across the site). These categories are what form the sections of the résumé, such as _Education_, _Experience_, and so on.

The sections are sorted by name. I have chosen the names that comply with this limitation. I'll work further on polishing this part so we can have custom sorting, but that's not one of my priorities. If any of you has an idea about how to do this, please _submit a pull request_, I'd be glad to add that and, obviously, attribute it to you.

### Making posts

I recommend that the post be started with bullet points. This goes well with the minimalistic design of the résumé. I also recommend that only three or four points be displayed per post. This makes it look concise.

Each of the post links is clickable. What's shown in the main page is just the summary.

The summary is decided based on the following (in this order):

1. Appearance of a `<!--more-->` tag in the post.
2. A closing `</ul>` tag.
3. The first paragraph.

You may add a `<!--more-->` tag in the middle of an unordered list---that's quite acceptable. In this case, the summary would be truncated at the appearance of the `<!--more-->`. Therefore, if the post is like:

``` markdown
- Point one
- Point two
- Point three
<!--more-->
- Point four
- Point five
```

The first three points would appear in the summary.

The idea is to not overwhelm your recruiter with too much data.

## License
All parts of the résumé site code are free to use and abuse under the [open-source MIT license](https://github.com/bradfordlynch/Skeleton-framework-jekyll/blob/master/LICENSE.md).

## Acknowledgement

Skeleton was created by [Dave Gamache](https://twitter.com/dhg) for a better web.

Skeleton-framework-Jekyll was created by [Bradford Lynch](https://twitter.com/blynch41) for better Skeleton and Jekyll harmony.

Skeleton Resume, minimalistic online résumé, was created by [Ram Iyer](https://github.com/theramiyer) based on Skeleton, built by Jekyll.
