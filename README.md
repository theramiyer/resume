# Skeleton Résumé

A simple, elegant online résumé created using an [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll), an implementation of Dave Gamache's Skeleton framework in Jekyll. The original Skeleton framework and docs are available from http://getskeleton.com/.

Feel free to fork the repository to create your own résumé, either hosted via GitHub pages, or your own domain.

## Getting Started

First things first: modify the data in `config.yml` to make the site your own (after forking it, of course).

### Structure

This site is implemented using Jekyll Collections.
1. Edit the `config.yml`; modify the `collections` to suit your needs.
2. Create folders within the `data` directory, named `_collectionName`.
3. Create Markdown files with the following YAML:
  ``` YAML
  ---
  layout: "post" # The post layout design is preferred here
  title: "Name of the institution/organisation"
  subtitle: "What you did at the organisation"
  tenure: "2007 – 10" # The tenure for which you were part of the organisation
  ---
  ```

Ensure that you have very few and specific collections; they'd better have one-word `name`. If you would like a longer display name, use the displayname `property`. An example is the 'Achievements and Certifications'. These form the sections of the résumé, such as _Education_, _Experience_, and so on.

Collections will by default be sorted by name. If you would like to use a specific order, use the `sequence` property. This setup works for me. If it doesn't for you, please feel free to fork the repo, polish it, and submit a pull request. I'd be glad to add the modification if it meets all requirements, and obviously, attribute it to you.

### Making posts

I recommend that the post be started with bullet points. This goes well with the minimalistic design of the résumé. I also recommend that only three or four points be displayed per post. This makes the resume look concise.

What's shown in the main page is just the summary. The _clickability_ depends on where Jekyll finds the `<!--more-->` tag. If no <!--more--> tag is found, the title will not be clickable. Of course, if you know the permalink, you can always navigate to it.

You may add a `<!--more-->` tag in the middle of an unordered list---it's acceptable. Therefore, if the post is like:

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

_Skeleton Resume_, a minimalistic online résumé, was created by [Ram Iyer](https://github.com/theramiyer) based on Skeleton, built by Jekyll.
