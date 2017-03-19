<<<<<<< HEAD
# console-theme

[![Gem Version](https://badge.fury.io/rb/console-theme.svg)](https://badge.fury.io/rb/console-theme)

For a demo, please [click here](http://jaehee0113.github.io/console).

![alt tag](http://jaehee0113.github.io/console/screenshot.png)

This is a simple yet powerful theme that will make your website look really stylish. This theme is especially suitable for users who would want to focus on writing blogs instead of working on front-end stuffs.

The primary features of this theme are:
* Gulp
* Post search functionality
* svg symbol functionality (plugin)
* string original type check functionality (plugin)
* Rake to create a post
* Disqus integration (with each post having its unique identifier)
* Color customization functionality
* Categorization (data-driven)
* Offcanvas menu
* Pagination functionality (utilises jekyll-paginate as well as jquery paginator)
* Internationalization (uses jekyll-polyglot)
* SEO (uses jekyll-seo-tag)

There are more features to come. Stay tuned!

# Table of Contents

* [Installation](#installation)
  * [Gulp Settings](#gulp-settings)
  * [Using a BrowserSync instead of Jekyll generated local server](#why-browsersync)
* [Usage](#usage)
  * [Creating a post](#create-post)
  * [Integrating Disqus with your website](#integrate-disqus)
  * [Using svg symbol](#use-svg-symbol)
  * [Adding more languages](#add-languages)
* [Categorization](#categorization)
* [Layouts and Blocks](#layout-block)
* [Stylesheets](#stylesheets)
* [Why some pages need to use folder structure](#why-use-folder-structure)
* [Contributing](#contributing)
* [Development](#development)
* [License](#license)

<div id='installation'></div>
## Installation

Add this line to your Jekyll site's Gemfile:

```ruby
gem "console-theme"
```

And add this line to your Jekyll site's `_config.yml`:

```yaml
theme: console-theme
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install console-theme

<div id='gulp-settings'></div>
### Gulp settings

To be able to use Gulp, you will need to install Node.js as well as its package manager (i.e. npm). Once you have installed npm. Go to the folder where the package.json is located and run `npm install` and it will install all the dependencies including Gulp.

    $ npm install

When running `gulp` command, it will run the default gulp task. Make sure to run the following command when you are in the folder that has `gulpfile.js`.

    $ gulp

This task would run several other tasks defined in `gulpfile.js.` To run individual tasks, please type `gulp [task name]`. For example:

    $ gulp minify-css

<div id='why-browsersync'></div>
### Using a BrowserSync instead of Jekyll generated local server.

When running Jekyll serve, it is possible to run a server. However, I chose to use BrowserSync instead of that for few reasons:

* BrowserSync is a de-facto standard nowadays.
* It is used with Gulp and Gulp provides bundling as well as minifying, which based on my knowledge is not possible with Jekyll generated server.

Therefore, please do use gulp!

<div id='usage'></div>
## Usage

<div id='create-post'></div>
### Creating a post

Please use `rake` command to create a post. Using this command would automatically generate Jekyll front matter with a unique Disqus identifier. The syntax for rake command is [assuming that you are in the root folder]:

```ruby
rake post title="Title" [date="2017-01-13"] [category="category"]
```

[] are optionals.

<div id='integrate-disqus'></div>
### Integrating Disqus with your website.

You will need to first have Disqus account. Once the account is ready, please modify `config.yml` file by adding your shortname for disqus like below:

```yaml
disqus_shortname: [your short name. Remove the bracket!]
```

By doing this, every disqus script would use that information and disqus identifier to fetch relevant data.

<div id='use-svg-symbol'></div>
### Using svg symbol

Using svg symbol is a good practice. By doing this, we can organize svgs better while not losing the caching functionality. Make sure you change your svg file to the file that conforms to the svg symbol style:

```html
<svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
  <symbol id="beaker" viewBox="214.7 0 182.6 792">
    <circle cx="344.8" cy="20.2" r="20.2"/>
    <circle cx="344.8" cy="92.9" r="20.2"/>
    <circle cx="320.5" cy="169.7" r="24.2"/>
    <circle cx="243" cy="141.4" r="24.2"/>
    <circle cx="284.2" cy="56.6" r="36.4"/>
  </symbol>
</svg>
```

If we would like to use this, we use svgicon tag by:

```liquid
{% svgicon beaker %}
```

This would display beaker on the screen! Examples are available.

This external svg file is located in: `assets/css/images/graphics/svg-symbols.svg`

<div id='add-languages'></div>
### Adding more languages

This theme uses `jekyll-polyglot` plugin with the plugin I developed. I have already initiated the internationalization work. The partially translated languages are: English, German and Korean. To add languages, simply add the language acronym (e.g. German is de ('Deutsch') but really you can define your own acronym) to languages variable in `_config.yml` (e.g. `languages: ["en", "de", "ko"]`). To add more languages, you would need to do the following:

1. **`localization.json`**: this is a nested json object file. The key represents the id for each translation and the value would have another key-value object with its key repesenting language acronym and the value being the translated word.

2. **`search.js`**: this may later be integrated with `localization.json` file but at the moment it is separate. In this js file, you will see `populateJSON()` function. The translation json in this function will be used in search.html. To add more languages, you need to modify this function.

The above files will then be used by the plugin called **Localization** (i.e. `_plugins/localize.rb`) and to use this simply:

```liquid
{% localize [key_id] %}
```

Explore the files and you will see plenty of examples. The plugin will automatically detect the language currently being used and then translate the word according to the one defined in `localization.json`.

<div id='categorization'></div>
## Categorization

This theme uses data-driven categorization, which makes the construction of categoization simple and succinct. The json file for category is located in _data/categories.json. Each category has three attributes: title, href and id (used to uniquely identify them). Please view the sample file to get a sense of it.

To create the category pages, you need to create a 'category' folder and subfolders would be the name of categories. They can be further nested (i.e. sub categories). Each folder would have index.md (as we will be using folder structure for creating the page for category.) You can reference my website or refer to the examples provided.

<div id='layout-block'></div>
## Layouts and Blocks

This theme values simplicity. As such, every layout would look extremely analogous with each other. However, for extensibility there are about 7 layouts:

* category
* default
* main
* page
* post
* search
* home

These layouts share same blocks, which are defined in _includes folder. There are about 10 blocks:

* **category**: the block that displays the available categories.
* **comment**: the comment block, which would be visible if comment: true in the front matter for posts.
* **footer**: the footer area.
* **global**: the global menu area.
* **head**: corresponds to the head tag in html.
* **header**: the header area. This area usually shows the location of particular page.
* **home**: corresponds the index.html
* **navigation**: the block for the menu.
* **not_found**: for 404 page.
* **search**: the block for search.

<div id='stylesheets'></div>
## Stylesheets

Jekyll uses sass, which is a scripting language that would be interpreted into css files. They are largely divided into three usages:

* **blocks**: the rest. The files are well separated. ( I think. )
* **configurations**: color settings and foundation styling will be here.
* **mixins**: self-explanatory. The breakpoint would be set here for responsive web design.

Based on your needs, you may modify these files.

<div id='why-use-folder-structure'></div>
## Why some pages need to use folder structure

To create a page, there are few ways to achieve it. One of the solutions would be to use folder structure. For example, if we were to create a page called 'archive', then you can create the folder called 'archive' and then include index.html.  For pages that use jekyll-paginate functionality, it is mandatory to use this. Otherwise, the functionality would not work. Please do not use .md extension. Use `.html` only as it would not work if this extension is not used.

<div id='contributing'></div>
## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/jaehee0113/console. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

<div id='development'></div>
## Development

To set up your environment to develop this theme, run `bundle install`.

Your theme is setup just like a normal Jekyll site! To test your theme, run `bundle exec jekyll serve` and open your browser at `http://localhost:4000`. This starts a Jekyll server using your theme. Add pages, documents, data, etc. like normal to test your theme's contents. As you make modifications to your theme and to your content, your site will regenerate and you should see the changes in the browser after a refresh, just like normal.

When your theme is released, only the files in `_layouts`, `_includes`, and `_sass` tracked with Git will be released.

<div id='license'></div>
## License

The theme is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
=======
# <a name="jalpc"></a>Jalpc. [![Analytics](https://ga-beacon.appspot.com/UA-73784599-1/welcome-page)](https://github.com/Jack614/jalpc_jekyll_theme)

[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.svg?v=103)](https://opensource.org/licenses/mit-license.php)
[![stable](http://badges.github.io/stability-badges/dist/stable.svg)](http://github.com/badges/stability-badges)
[![Open Source Love](https://badges.frapsoft.com/os/v1/open-source.png?v=103)](https://github.com/ellerbrock/open-source-badge/)

<http://www.jack003.com>

![Blog](readme_files/blog.gif)

* [Ad](#ad)
* [Getting Started](#getting-started)
    * [Fork, then clone](#fork-then-clone)
    * [Modify _config.yml](#modify-configyml)
    * [Index page](#index-page)
    * [Modify _data/\*.yml](#mofify-datayml)
    * [Jekyll Serve](#jekyll-serve)
    * [Using Github Pages](#using-github-pages)
    * [Categories in blog page](#categories-in-blog-page)
    * [Pagination](#pagination)
    * [Page views counter](#page-views-counter)
    * [Multilingual Page](#multilingual-page)
    * [Web analytics](#web-analytics)
    * [Comment](#comment)
    * [Share](#share)
    * [Search engines](#search-engines)
    * [CNAME](#cname)
    * [Put in a Jalpc Plug](#put-in-a-jalpc-plug)
    * [Enjoy](#enjoy)
* [Upgrading Jalpc](#upgrading-jalpc)
    * [Ensure there's an upstream remote](#ensure-theres-an-upstream-remote)
    * [Pull in the latest changes](#pull-in-the-latest-changes)
* [Thanks to the following](#thanks-to-the-following)
* [Contributing](#contributing)

This is a simple, beautiful and swift theme for Jekyll. It's mobile first, fluidly responsive, and delightfully lightweight.

It's pretty minimal, but leverages large type and drastic contrast to make a statement, on all devices.

The landing page of the blog is multilingual page.

It is my pleasure to contact me, you can give me your website or some advice about my website. Let's build a wonderful Jekyll theme together!

## <a name="ad"></a>Ad

[Jalpc-A](https://github.com/Jack614/Jalpc-A): another Jekyll theme written by [AngularJS](https://angularjs.org/).

##  <a name="getting-started"></a>Getting Started

If you're completely new to Jekyll, I recommend checking out the documentation at <http://jekyllrb.com> or there's a tutorial by Smashing Magazine.

### <a name="fork-then-clone"></a> Fork, then clone

**Fork** the repo, and then **clone** it so you've got the code locally.

```
$ git clone https://github.com/<your githubname>/jalpc_jekyll_theme.git
$ cd jalpc_jekyll_theme
$ gem install jekyll # If you don't have jekyll installed
$ rm -rf _site && jekyll server
```

### <a name="modify-configyml"></a>Modify `_config.yml`

The _config.yml located in the root of the jalpc_jekyll_theme directory contains all of the configuration details for the Jekyll site. The defaults are:

``` yml
# Website settings
title: "Jalpc"
description: "Jack's blog,use Jekyll and github pages."
keywords: "Jack,Jalpc,blog,Jekyll,github,gh-pages"

baseurl: "/"
url: "http://www.jack003.com"
# url: "http://127.0.0.1:4000"

# author
author:
  name: 'Jack'
  first_name: 'Jia'
  last_name: 'Kun'
  email: 'me@jack003.com'
  facebook_username: 'jiakunnj'
  github_username: 'Jack614'
  avatar: 'static/img/landing/Jack.jpg'
...
```

### <a name="#index-page"></a>Index page

The index page is seprated into several sections and they are located in `_includes/sections`,the configuration is in `_data/landing.yml` and section's detail configuration is in `_data/*.yml`.

#### <a name="mofify-datayml"></a>Modify `_data/*.yml`

These files are used to dynamically render pages, so you almost don't have to edit *html files* to change your own theme, besides you can use `jekyll serve --watch` to reload changes.

The following is mapping between *yml file* to *sections*.

* landing.yml ==> index.html
* language.yml ==> index.html
* careers.yml  ==>  _includes/sections/career.html
* skills.yml  ==>  _includes/sections/skills.html
* projects.yml  ==>  _includes/sections/projects.html
* links.yml  ==>  _includes/sections/links.html

This *yml file* is about blog page navbar

* blog.yml ==> _includes/header.html

The following is mapping between *yml file* to *donation*

* donation.yml ==> blog/donate.html
* alipay.yml  ==>  blog/donate.html
* wechat_pay.yml ==> blog/donate.yml

### <a name="jekyll-serve"></a>Jekyll Serve

Then, start the Jekyll Server. I always like to give the --watch option so it updates the generated HTML when I make changes.

```
$ jekyll serve --watch
```

Now you can navigate to localhost:4000 in your browser to see the site.

### <a name="using-github-pages"></a>Using Github Pages

You can host your Jekyll site for free with Github Pages. [Click here](https://pages.github.com) for more information.

A configuration tweak if you're using a gh-pages sub-folder

In addition to your github-username.github.io repo that maps to the root url, you can serve up sites by using a gh-pages branch for other repos so they're available at github-username.github.io/repo-name.

This will require you to modify the _config.yml like so:

``` yml
# Welcome to Jekyll!

# Site settings
title: Website Name

baseurl: "/"
url: "http://github-username.github.io"
# url: "http://127.0.0.1:4000"

# author
author:
  name: nickname
  first_name: firstname
  last_name: lastname
  email: your_email@example.com
  facebook_username: facebook_example
  github_username: 'github_example'
  head_img: 'path/of/head/img'

# blog img path
img_path: '/path/of/blog/img/'
```

If you start server on localhost, you can turn on `# url: "http://127.0.0.1:4000"`.

### <a name="categories-in-blog-page"></a>Categories in blog page

In blog page, we categorize posts into several categories by url, all category pages use same template html file - `_includes/category.html`.

For example: URL is `http://127.0.0.1:4000/python/`. In `_data/blog.yml`, we define this category named `Python`, so in `_includes/category.html` we get this URL(/python/) and change it to my category(Python), then this page are posts about **Python**. The following code is about how to get url and display corresponding posts in  `_includes/category.html`.

```html
<div class="row">
    <div class="col-lg-12 text-center">
        <div class="navy-line"></div>
        {% assign category = page.url | remove:'/' | capitalize %}
        {% if category == 'Html' %}
        {% assign category = category | upcase %}
        {% endif %}
        <h1>{{ category }}</h1>
    </div>
</div>
<div class="wrapper wrapper-content  animated fadeInRight blog">
    <div class="row">
        <ul id="pag-itemContainer" style="list-style:none;">
            {% assign counter = 0 %}
            {% for post in site.categories[category] %}
            {% assign counter = counter | plus: 1 %}
            <li>
```



### <a name="pagination"></a>Pagination

The pagination in jekyll is not very perfect,so I use front-end web method,there is a [blog](http://www.jack003.com/html/2016/06/04/jekyll-pagination-with-jpages.html) about the method and you can refer to [jPages](http://luis-almeida.github.io/jPages).

### <a name="page-views-counter"></a>Page views counter

Many third party page counter platforms are too slow,so I count my website page view myself,the javascript file is [static/js/count.min.js](https://github.com/JiaKunUp/jalpc_jekyll_theme/blob/gh-pages/static/js/count.min.js) ([static/js/count.js](https://github.com/JiaKunUp/jalpc_jekyll_theme/blob/gh-pages/static/js/count.js)),the backend API is written with flask on [Vultr VPS](https://www.vultr.com/), detail code please see [jalpc-flask](https://github.com/JiaKunUp/jalpc-flask).

### <a name="multilingual-page"></a>Multilingual Page

The landing page has multilingual support with the [i18next](http://i18next.com) plugin.

Languages are configured in the `config.yml` file.

> If you don't need this feature, please clear content in file `_data/language.yml` and folder `static/locales/`.

#### Step 1

Add a new language entry

```yml
languages:
  - locale: 'en'
    flag: 'static/img/flags/United-States.png'
  - locale: '<language_locale>'
    flag: '<language_flag_url>'
```

#### Step 2

Add a new json (`static/locales/<language_locale>.json`) file that contains the translations for the new locale.

Example `en.json`

```json
{
  "website":{
    "title": "Jalpc"
  },
  "nav":{
    "home": "Home",
    "about_me": "About",
    "skills": "Skills",
    "career": "Career",
    "blog": "Blog",
    "contact": "Contact"
  }
}
```

#### Step 3

Next you need to add html indicators in all place you want to use i18n.(`_includes/sections/*.html` and `index.html`)

Example:

``` html
<a class="navbar-brand" href="#page-top" id="i18_title"><span data-i18n="website.title">{{ site.title }}</span></a>
```

#### Step 4

Next you need to initialise the i18next plugin(`index.html`):

``` javascript
$.i18n.init(
    resGetPath: 'locales/__lng__.json',
    load: 'unspecific',
    fallbackLng: false,
    lng: 'en'
}, function (t)
    $('#i18_title').i18n();
});
```

### <a name="web-analytics"></a>Web analytics

I use [Baidu analytics](http://tongji.baidu.com/web/welcome/login), [Google analytics](https://www.google.com/analytics/) and [GrowingIO](https://www.growingio.com/) to do web analytics, you can choose either to realize it,just register a account and replace id in `_config.yml`.

### <a name="comment"></a>Comment

I use [Changyan](http://changyan.kuaizhan.com/) and [Disqus](https://disqus.com/) to realize comment.

#### Changyan
To configure Changyan, get the appid and conf in <http://changyan.kuaizhan.com/>. Then, in `_config.yml`, edit the changyan value to enable Changyan.

#### Disqus
To configure Disqus,you should set disqus_shortname and get public key and then, in `_config.yml`, edit the disqus value to enable Disqus.

### <a name="share"></a>Share

I use [bshare](http://www.bshare.cn/) to share my blog on other social network platform. You can register a count and get your share uuid.

### <a name="search-engines"></a>Search engines

I use javascript to realize blog search,you can double click `Ctrl` or click the icon at lower right corner of the page,the detail you can got to this repo: <https://github.com/androiddevelop/jekyll-search>.

Just use it.

![search](readme_files/search.gif)

### <a name="cname"></a>CNAME

Replace your website domain in **CNAME** file.

### <a name="put-in-a-jalpc-plug"></a>Put in a Jalpc Plug

If you want to give credit to the Jalpc theme with a link to my personal website <http://www.jack003.com>, that'd be awesome. No worries if you don't.

### <a name="enjoy"></a>Enjoy

Hope you enjoy using Jalpc. If you encounter any issues, please feel free to let me know by creating an issue. I'd love to help.

## <a name="upgrading-jalpc"></a>Upgrading Jalpc

Jalpc is always being improved by its users, so sometimes one may need to upgrade.

### <a name="ensure-theres-an-upstream-remote"></a>Ensure there's an upstream remote

If `git remote -v` doesn't have an upstream listed, you can do the following to add it:

```
git remote add upstream https://github.com/Jack614/jalpc_jekyll_theme.git
```

### <a name="pull-in-the-latest-changes"></a>Pull in the latest changes

```
git pull upstream gh-pages
```

There may be merge conflicts, so be sure to fix the files that git lists if they occur. That's it!

## <a name="thanks-to-the-following"></a>Thanks to the following

* [Jekyll](http://jekyllrb.com)
* [Bootstrap](http://www.bootcss.com)
* [jPages](http://luis-almeida.github.io/jPages)
* [i18next](http://i18next.github.io/i18next)
* [pixyll](https://github.com/johnotander)
* [androiddevelop](https://github.com/androiddevelop)

## <a name="contributing"></a>Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
>>>>>>> a4f89d881d051eeb194c8319f90ee43497aebee1
