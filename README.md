# SciCloj Blog website

A blog website for sharing articles on specific topics.


### Running the Server

The web server can be started using either Leiningen:

```bash
lein serve # or lein serve-fast
```

or tools-deps:

```bash
clojure -X:serve # or clojure -X:serve-fast
```

The server will watch for changes in the `content` and `themes` folders and recompile the content automatically. The `*-fast` variants perform [fast but partial compilation](https://cryogenweb.org/docs/fast-compilation.html) of only the changed page/post.

You can also generate the content without bringing up a server either via:

```
lein run
```

or via:

```
clojure -M:build
```

### Site Configuration

The site configuration file is found at `content/config.edn`, this file looks as follows:

```clojure
{:site-title                   "My Awesome Blog"
 :author                       "Bob Bobbert"
 :description                  "This blog is awesome"
 :site-url                     "http://blogawesome.com/"
 :post-root                    "posts"
 :page-root                    "pages"
 :post-root-uri                "posts-output"
 :page-root-uri                "pages-output"
 :tag-root-uri                 "tags-output"
 :author-root-uri              "authors-output"
 :public-dest                  "public"
 :blog-prefix                  "/blog"
 :rss-name                     "feed.xml"
 :rss-filters                  ["cryogen"]
 :recent-posts                 3
 :post-date-format             "yyyy-MM-dd"
 :archive-group-format         "yyyy MMMM"
 :sass-src                     []
 :sass-path                    "sass"
 :theme                        "blue"
 :resources                    ["img"]
 :keep-files                   [".git"]
 :disqus?                      false
 :disqus-shortname             ""
 :ignored-files                [#"\.#.*" #".*\.swp$"]
 :previews?                    false
 :posts-per-page               5
 :blocks-per-preview           2
 :clean-urls                   :trailing-slash
 :collapse-subdirs?            false
 :hide-future-posts?           true
 :klipse                       {}
 :description-include-elements #{:p :h1 :h2 :h3 :h4 :h5 :h6}
 :debug?                       false}
```

For information about each key please see the ["Configuration"](http://cryogenweb.org/docs/configuration.html) portion of the Cryogen documentation site.

### Switching between Markdown and AsciiDoc

Cryogen comes with Markdown support as default. If you want to use AsciiDoc instead, open the `project.clj` in your created blog (e.g. `my-blog`), and change the line in `:dependencies` that says `cryogen-markdown` to `cryogen-asciidoc` (and ensure the right version).
Instead of looking for files ending in `.md` in the `content/md` directory, the compiler will now look for files ending in `.asc` in the `content/asc` directory.

### Selecting a Theme

The Cryogen template comes with three themes in the `themes` folder. To change your blog's theme, change the value of the `:theme` key in `config.edn`. Note that the Nucleus theme is obtained from [downloadwebsitetemplates.co.uk](http://www.downloadwebsitetemplates.co.uk/template/nucleus/) that requires you to keep the footer, unless you make a donation on their website.

### Customizing Layouts

Cryogen uses [Selmer](https://github.com/yogthos/Selmer) templating engine for layouts. Please refer to its documentation to see the supported tags and filters for the layouts.

The layouts are contained in the `themes/{theme}/html` folder of the project. By default, the `base.html` layout is used to provide the general layout for the site. This is where you would add static resources such as CSS and JavaScript assets as well as define headers and footers for your site.

Each page layout should have a name that matches the `:layout` key in the page metadata and end with `.html`. Page layouts extend the base layout and should only contain the content relevant to the page inside the `content` block.
For example, the `tag` layout is located in `tag.html` and looks as follows:

```xml
{% extends "/html/base.html" %}
{% block content %}
<div id="posts-by-tag">
    <h2>Posts tagged {{name}}</h2>
    <ul>
    {% for post in posts %}
        <li>
            <a href="{{post.uri}}">{{post.title}}</a>
        </li>
    {% endfor %}
    </ul>
</div>
{% endblock %}
```

### Code Syntax Highlighting

Cryogen uses [Highlight.js](https://highlightjs.org/) for code syntax highlighting. You can add more languages by replacing `themes/{theme}/js/highlight.pack.js` with a customized package from [here](https://highlightjs.org/download/).

The ` initHighlightingOnLoad` function is called in `themes/{theme}/html/base.html`.

```xml
<script>hljs.initHighlightingOnLoad();</script>
```

## Deploying Your Site

The generated static content will be found under the `public` folder. Simply copy the content to a static
folder for a server such as Nginx or Apache and your site is now ready for service.

A sample Nginx configuration that's placed in `/etc/nginx/sites-available/default` can be seen below:

```javascript
server {
  listen 80 default_server;
  listen [::]:80 default_server ipv6only=on;
  server_name localhost <yoursite.com> <www.yoursite.com>;

  access_log  /var/log/blog_access.log;
  error_log   /var/log/blog_error.log;

  location / {
    alias       /var/blog/;
    error_page  404 = /404.html;
  }
}
```

Simply set `yoursite.com` to the domain of your site in the above configuration and
ensure the static content is available at `/var/blog/`. Finally, place your custom error page
in the `/var/blog/404.html` file.

More information on deployment can be found [here](http://cryogenweb.org/docs/deploying-to-github-pages.html).
