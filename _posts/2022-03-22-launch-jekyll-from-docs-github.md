---
layout: post
title: "Run Jekyll Site locally with a GitHub remote theme."
categories: [Projects]
tags: [Jekyll, VSCode, GitHub Themes, yaght]
excerpt: "<p>How do I use a Remote Theme with a GitHub Pages site that is hosted in the /docs folder of my project?</p>"
---

## Problem Description

> I want to use a Remote Theme with the GitHub Pages site that is hosted in the `/docs` folder of my project.

If you have a GitHub project and you want to host a [GitHub Pages](https://pages.github.com/) site from the `/docs` folder, the default Jekyll configuration described in the documentation will not work because the documentation assumes that you are creating a full Jekyll site locally. When creating a Jekyll site, you will want to take the following into consideration:

1. It is not necessary to host the markup locally.
2. Most/All of your theme customizations can be done through configuration.
3. You may not want to build a custom [Jekyll Theme](https://jekyllrb.com/docs/themes/).
4. You want to keep a clean GitHub repo and encapsulate all web components within the `/docs` folder.

## Solution

_note: This post assumes that you have already set up [Ruby](https://www.ruby-lang.org), [bundler](https://bundler.io) and [Jekyll](https://jekyllrb.com) in your local dev environment._

In order to run a jekyll site from the command line, you need to configure the directory as a root site of a Jekyll site.

It is a straight-forward process of configuring the folder as the root of a jekyll site.

### Step 1: Create the _config.yml file

`_config.yml` lives in the root folder of your project. It is the overall config file for your Jekyll site.

[Example](#config)

### Step 2: Create the gemspec file

The `.gemspec` file defines properties for your rubygem, including gem name, author, and contact info.

[Example](#.gemspec)

### Step 3: Create the Gemfile

The `Gemfile` file defines properties for your rubygem, including gem name, author, and contact info.

[Example](#gemfile)

### Step 4: Generate the gemfile.lock

 run `bundle install` - This will create the gemfile.lock file, which describes all of the dependencies needed to run this project. It is considered best practice to check this file into GitHub so that others are able to build and run the site in an identical ruby environment.

### Step 5: Build and run the site Locally

once you have the files configured, you can launch the site by navigating to the `/docs` folder and executing:

```terminal
  bundle exec jekyll serve
  ```

## Example: Using the yaght theme for GitHub

### Page Level [#](#page){:name="page"}

### Sample _config.yml [#](#config){:name="config"}

```yml
title: <Site Name>
email: <Contact Email Address>
description: >-
  Here is the description of your site.
baseurl: ""
url: "https://<url of your site>" # the base hostname & protocol for your site, e.g. http://example.com
favicon: /images/favicon.jpg
logo: /images/favicon.jpg

#Social [jekyll-seo-tag]
social:
  name: <social-name>
  links:
    - https://twitter.com/<twitter-username>
    - https://www.linkedin.com/in/<linkedin-username>
    - https://github.com/<github-username>
twitter:
  username: <twitter_username>
  card: summary

# Blog Config
show_excerpts: true
permalink: "/blog/:year/:month/:day/:title.html"
# paginate: 20
# paginate_path: "/blog/page:num/"

#Google Analytics
google_analytics: UA-000000000-1

# Minima date format
# refer to http://shopify.github.io/liquid/filters/date/ if you want to customize this
minima:
  date_format: "%b %-d, %Y"

  # generate social links in footer
  social_links:
    github:  <github-username>
    linkedin: <linkedin-username>
    twitter: <twitter-username>
    rss: rss

# Build settings
markdown: kramdown
remote_theme: chrishasz/yaght

```

### Sample Gemfile [#](#gemfile){:name="gemfile"}

```Gemfile
    source "https://rubygems.org"
    # Jekyll Version
    gem "jekyll", "~> 3.8.5"
    # This is the default theme for new Jekyll sites. 
    gem "github-pages", group: :jekyll_plugins
    # If you have any plugins, put them here!
    group :jekyll_plugins do
    gem "jekyll-feed", "~> 0.12"
    gem 'jekyll-octicons'
    end

    # Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
    # and associated library.
    platforms :mingw, :x64_mingw, :mswin, :jruby do
    gem "tzinfo", "~> 1.2"
    gem "tzinfo-data"
    end

    # Performance-booster for watching directories on Windows
    gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
```

### Sample .gemspec [#](#gemspec){:name="gemspec"}

```gemspec
   # frozen_string_literal: true

    Gem::Specification.new do |spec|
        spec.name          = "<project-name>"
        spec.version       = "0.1.0"
        spec.authors       = ["<your-name>"]
        spec.email         = ["<you>@<domain>.<tld>"]

        spec.summary       = "<Project description>"
        spec.homepage      = "<url of site>"
        spec.license       = "MIT"

        spec.metadata["plugin-type"] = "theme"

        spec.files = `git ls-files -z`.split("\x0").select do |f|
            f.match(%r!^(assets|_(includes|layouts|sass)/|(LICENSE|README)((\.(txt|md|markdown)|$)))!i)
        end

        spec.add_runtime_dependency "github-pages", ">= 3.8.5", "< 5.0"

        spec.add_development_dependency "bundler"
        spec.add_development_dependency "rake"
    end

```

You can read more about yaght configuration in the [yaght documentation](https://www.chrishasz.com/yaght/general/getting-started-with-yaght).

## Summary and Take-aways

Launching a Jekyll site from the `/docs` folder of a GitHub repo is pretty easy, and is cleaner than building your own site from scratch. Just:

1. Add `_config.yml`, `.gemspec`, and `Gemfile` files to your `/docs` folder
2. Run `bundle install` to generate your `Gemfile.lock`
3. Finally, run `bundle exec jekyll serve` from the `/docs` directory to launch a local web server with your site.

## References

* [yaght](https://www.chrishasz.com/yaght) - A developer optimized Jekyll theme for GitHub
* [Github Pages](https://pages.github.com) - Get started with Github Pages
* [Jekyll Github Pages](https://jekyllrb.com/docs/github-pages/) - GitHub Pages documentation on the Jekyll site
