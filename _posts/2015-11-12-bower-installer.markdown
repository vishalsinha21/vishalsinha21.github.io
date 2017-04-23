---
published: true
title: Power of Bower Installer
layout: post
---
I like Bower, simply because it does away with visiting individual sites to download latest java scripts. It's been used mostly for java scripts though it is very generic and can handle any package. Bower is a fine package manager but it has its short comings. The main pain point for me is that it downloads the whole distribution with many files which are not needed for the project. That's the reason most people dont check-in the bower_components in their repositories.

This is where Bower Installer comes to rescue. I came across this project around a month back and was really excited to use in my projects.

Before Bower Installer, my bower.json used to look like this.

~~~ json
{
  "name": "explore-bower",
  "version": "0.0.1",
  "dependencies": {
    "backbone": "latest",
    "requirejs": "2.1.1",
    "jquery": "2.1.4",
    "bootstrap": "~3.3.5"
  }
~~~


After Bower Installer, I had to make some changes to my bower.json

~~~ json
{
  "name": "explore-bower",
  "version": "0.0.1",
  "dependencies": {
    "backbone": "latest",
    "requirejs": "2.1.1",
    "jquery": "2.1.4",
    "bootstrap": "~3.3.5"
  },
  "install": {
    "path" : {
      "css": "src/main/webapp/css",
      "js": "src/main/webapp/js"
    },
    "sources" : {
      "backbone" : [
        "bower_components/backbone/backbone.js",
        "bower_components/backbone/backbone-min.js"
      ],
      "bootstrap" : [
        "bower_components/bootstrap/dist/css/bootstrap.css",
        "bower_components/bootstrap/dist/css/bootstrap.min.css",
        "bower_components/bootstrap/dist/js/bootstrap.js"
      ]
    }
  }
}
~~~

- `install` is the new section for bower installer
- with `path` you can define the destination folder individually for css and js folder.
- even without `sources` section, bower installer can move the main files from bower componets to your defined destination folder, but with `sources` section you get greater control of what and how many files you want to move to your destination folder

Result: I found a very clean way to selectively choose the files that I need and move into my projects.

~~~ bash
src
└── main
    └── webapp
        ├── css
        │   └── bootstrap
        │       ├── bootstrap.css
        │       └── bootstrap.min.css
        └── js
            ├── backbone
            │   ├── backbone-min.js
            │   └── backbone.js
            ├── bootstrap
            │   └── bootstrap.js
            ├── jquery
            │   └── jquery.js
            ├── requirejs
            │   └── require.js
            └── underscore
                └── underscore.js
~~~

The documentation of this project is very good, for complete reference and usage:

<a href="https://github.com/blittle/bower-installer" target="_blank">https://github.com/blittle/bower-installer</a>
