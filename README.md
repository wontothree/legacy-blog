# wontothree.github.io

[Anthony Garcia's Technical Blog](https://wontothree.github.io/techblog/)

    wontothree.github.io
    ├── _data
    │   ├── ui-text.yml
    │   └── navigation.yml                     # 1. add navigation
    │
    ├── _includes
    │   └── mathjax_support.html
    │
    ├── _layouts
    │   └── default.html
    │
    ├── _pages                                 # 2. make page
    ├── _posts                                 # 3. write post
    ├── _sass
    ├── assets
    ├── img
    ├── _config.yml
    ├── .travis.yml
    ├── banner.js
    ├── Gemfile
    ├── index.html
    ├── minimal-mistakes-jekyll.gemspec
    ├── package-lock.json
    ├── package.json
    ├── Rakefile
    ├── staticman.yml
    └── README.md

# Navigation

`_data/navigation`

    main:
    - title: "Career"
    url: https://wontothree.github.io/career/

    sidebar-category:
    - title: "Technical Blog"
        children:
        - title: "Posts"
            url: /technical-blog

    - title: "Robotics"
        children:
        - title: "Probabilistic Robotics"
            url: /probablisticrobotics
        - title: "Digital Control"
            url: /digitalcontrol
        - title: "Optimal Control"
            url: /optimalcontrol  

# Category

    ---
    title: "Technical Blog"
    layout: archive
    permalink: /technical-blog
    sidebar:
        nav: "sidebar-category"
    ---

    {% assign posts = site.categories.technical-blog %}
    {% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}

# Post

# Reference

I modified [https://mmistakes.github.io/minimal-mistakes/](https://mmistakes.github.io/minimal-mistakes/)
