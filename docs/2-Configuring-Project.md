# Configuring Project

**Note: Each time you will change `happyplan.json` you will need to restart `happyplan` command to get updated configuration.**

## Arborescence

Here is what the tree you should have with default configuration (files are ordered manually for doc purpose):

```
    ├── node_modules               // (Happyplan is in that)
    ├── bower_components           // Bower components if you need
    ├── grunt_tasks                // Custom task or override
    │   └── config                 // Custom task config or override
    ├── src
    │   ├── _layouts               // html layouts for your documents
    │   ├── _partials              // Partials pieces
    │   ├── _helpers               // Handlebars helpers
    │   ├── posts                  // Posts for blog
    │   ├── assets                 // All about design
    │   │   ├── _glyphicons        // Svg icons to be served as webfont
    │   │   ├── _images            // Design images
    │   │   ├── _scripts           // JS
    │   │   │   └── script.js      // a JS file
    │   │   ├── _styles            // CSS
    │   │   │   ├── _icons.scss    // Used if you use grunt-webfont
    │   │   │   └── style.scss     // Where you put all your styles
    │   │   └── fonts              // Fonts
    │   └── media                  // Media for your documents
    ├── dist                       // => App ready to be distributed
    ├── build                      // magic folder you can ignore
    ├── bower.json                 // bower config
    ├── happyplan.json             // Happyplan config !
    ├── package.json               // required by NPM
    └── README.md                  // Everyone should have a README !
```

## Configuration

To override the default configuration [`happyplan.json`][defaultconf] you can create a `happyplan.json` at the root. Both will be **deeply merged** together.

_We encourage you to take a look to the [default configuration][defaultconf] before playing with it._

As you can see in the [default configuration][defaultconf], _you can change every paths you want_. If you are not happy with basepath, just replace value at the top level (`"_"`).
Just be sure to be careful when you play with that :)

By default, to configure tools used under the hood, there is a json dedicated section. You should be able to place any native parameters here of the tool in it.
_You can omit anything related to the paths in favor of the specific configuration section._
Whenever it's possible, we will use simple json to whatever is needed conversion to suit the tool requirement.

### Document Engine (HTML)

We are using [assemble](https://github.com/assemble/assemble) to render html & markdown.
Checkout [Getting Started](1-Getting-Started.md) to learn more about how to write content.

#### Assemble

Assemble is similar to Jekyll: it uses layouts, partials & content files,
but right now config is very simple so there nothing you can change in the config.
Obviously if you need it, you can [override](#override-tasks--custom-tasks) assemble task
with the config you want !

##### Layouts

Default theme only have a simple HTML 5 default layout that include HappyPlan hooks.
Be sure to check it before doing your own.

##### Partials

We have included for you some partials so you can include this

###### Google Analytics

To setup Google Analytics, just add this in you configuration file:

```json
{
  "google_analytics_tracking_id": "UA-XXXX-X",
  "google_universal_analytics": true
}
```
_The last value `google_universal_analytics` needs to be true to enable new Universal Analytics tracker (be sure you account is one)._

Then just add this include at the bottom of you layout

```handlebars
{> google-analytics }
```

###### Disqus

To setup Disqus, just add this in you configuration file:

```json
{
  "disqus_short_name": "YOURDISQUSSHORTNAME",
  "disqus_developer": false,
}
```
_The last value `disqus_developer` needs to be true when you will configure or want to debug comments during development._

Then just add this include at the bottom of you layout.
This will add automatically the script if disqus comments are enabled and `page.comments` is either empty (index) or set to true (!= false).

```handlebars
{> disqus }
```

##### Helpers

The default engine (Handlebars) supports helpers. You can add pretty much what you want
to extend the engine.
By default, we use a [lots of helpers](http://assemble.io/helpers/), but you can add you own in the `src/_helpers` folder.
You'll learn quickly how to write your own just reading
[some](https://github.com/assemble/handlebars-helpers/tree/master/lib/helpers)
[helpers](https://github.com/assemble/handlebars-helpers-examples/tree/master/experimental/helpers).

###### `{{#posts pages}}`

Allow you to loop on pages that have the `type: post`.

You can pass the following parameters:

- limit

###### `{{#each_dateSorted pages}}`

Allow you to loop on pages, sorted on the date (most recent first).

You can pass the following parameters:

- limit

###### `{{url dest}}`

Allow you to transform a file destination to an url. Currently it just remove the `dist` folder name & also remove `index.html`.

###### `{{dirname dest}}`

Allow you to get the url dirname (current path) of the given destination.
Convenient to call ressource from the same place you are in.

###### `{{#handlebarception}}`

Allow you to render some handlebars content that's into a variable.

##### Plugins

[Assemble support plugins](http://assemble.io/plugins/).
They allow you to modify the process during the render of the pages.
You can plug every behavior you want on some hooks.

###### Date from filename

For now there is only one plugin by default that allow you to specify dates at the beginning of a filename.
Handy to sort blog posts ;)

`src/posts/2013-12-25-xmax-post/index.html.hbs` will become `dist/posts/xmax-post/index.html` with date `2013-12-25` available as a file meta.

#### Code Highlight

Code is highlighted by `CodeMirror` library.
To highlight some code, you can use block à la GitHub.

    ```html
    <!doctype>
    ```

Here is a list of language/alias already supported:

- html: htmlmixed
- js: javascript
- bash: shell
- zsh: shell

Note that [native CodeMirror mode names](http://codemirror.net/mode/index.html) are obviously supported.

_Tip: To see mode names, just over the pretty language name on the page above & checkout name in the url._

**Warning: Some languages (like html) can require more mode to be loaded.**
So if you want load that kind of language, please be sure to override happyplan configuration key `codeHighlightMap` (please look this key in [default configuration][defaultconf] to know how to do).

_Note: you will need to add a theme by hand. Checkout available [themes](http://codemirror.net/demo/theme.html].
[You can download themes directly from GitHub](https://github.com/marijnh/CodeMirror/tree/master/theme), or build your own ;)


### Assets

**By default, _only one stylesheet_ is included automatically**

Here is currently the kind of assets recognized.

* Scripts
* Styles
* Images
* Glyphicons (svg icons transformed as a web font)
* Fonts

Scripts & styles have some specials treatments & they can be automatically included in your html layout if you use `happyplan.assets.scripts` & `happyplan.assets.styles` array with appropriate items like this (replace `{{ type }}` by `scripts` or `styles`).

```json
{
  "assets": {
    "{{ type }}": [{
      "src": [
        "source-1.ext",
        "source-2.ext"
      ],
      "dest": "<%= happyplan.path.dist.assets.{{ type }} %>/finalname.ext",
      "hook": "head_open|head_close|head_before_stylesheets|head_stylesheets|head_after_stylesheets|body_open|body_close"
      "ifIE": true|"lt IE 9"|"whatever combo you need"
    }]
  }
}
```

Each objects in the array should at least contains `dest` property.
It's used for automatic inclusion into html hooks (eg: to include jQuery from a CDN).
_Note: `happyplan.path.dist.assets.*` paths used in `dest` key will be automatically adjusted with appropriate base url._

#### Cache-buster

> A cache-buster is a unique piece of code that prevents a browser from reusing something it has already downloaded and cached.

For us, the cache-buster is a value (`happyplan.cachebuster`) that can be used to _force_ some statics files to be updated by browsers when you publish a new version of your website.
By default, our cache-buster is just a date `"<%= grunt.template.today('yyyymmddHHMMss') %>"`, but you can change it to something like the version of you website like this:

```json
{
  "cachebuster": "<%= happyplan.version %>"
}
```

Obviously, you can just put whatever you want into the template.

For now, it's used for stylesheets (links href) & scripts (src) when using `--env=dist` option.
You can disable it by just setting it to `false`.
It's prepended at the end after `?`.

#### Hooks

Hooks are used to attach script or stylesheet (or something else) automatically in some place of your layout (see example above).

Here are default hooks that can be overrided:

```json
{
  "assets": {
    "default_hook": {
      "styles": "head_stylesheets",
      "scripts": "body_close"
    }
  }
}
```

#### Script Engine (JavaScript)

_For now, we just concat (for dev) or uglify (for dist) scripts._

If you want to add a script easily you just need to add an item to the `happyplan.assets.scripts` array.

```json
{
  "assets": {
    "scripts": [{
      "src": [
        "<%= happyplan.path.assets.scripts %>/script.js"
      ],
      "dest": "<%= happyplan.path.dist.assets.scripts %>/script.js"
    }]
  }
}
```

Here is a solid example that show you how to includes lots scripts using differents placements:

```json
{
  "assets": {
    "scripts": [{
      "src": [
        "<%= happyplan.bower_components %>/Respond/respond.src.js"
      ],
      "dest": "<%= happyplan.path.dist.assets.scripts %>/respond.js",
      "IEcond": "lt IE 9",
      "hook": "head_after_stylesheets"
    }, {
      "dest": "http://code.jquery.com/jquery-git2.js"
    }, {
      "src": [
        "<%= happyplan.bower_components %>/aight/aight.js"
      ],
      "dest": "<%= happyplan.path.dist.assets.scripts %>/ieshim.js",
      "IEcond": "lt IE 9"
    }, {
      "src": [
        "<%= happyplan.bower_components %>/picturefill/picturefill.js",
        "<%= happyplan.path.assets.scripts %>/script.js"
      ],
      "dest": "<%= happyplan.path.dist.assets.scripts %>/script.js"
    }]
  }
}
```

### Style Engine (CSS)

To avoid a Ruby dependency & keep build fast, we decide to use Sass based on [libsass](https://github.com/hcatlin/libsass) but you will be able to use whatever you want by [configuring your own task (soon)](https://github.com/happyplan/happyplan/issues/80)

#### Sass

By default, only `bower_components` is added as an include path, but you can override that in your `happyplan.json`:

```json
{
  "sass": {
    "includePaths": [
      "<%= happyplan.bower_components %>",
      "one_more_path"
    ]
  }
}
```

If you want to add a stylesheet easily you just need to add an item to the `happyplan.assets.styles` array.
**Here is what is done by default:**

```json
{
  "assets": {
    "styles": [{
      "src": "<%= happyplan.path.assets.styles %>/<%= happyplan.name %>.scss",
      "dest": "<%= happyplan.path.dist.assets.styles %>/<%= happyplan.name %>.css"
    }]
  }
}
```

For a more solid examples, please refer to the section `Script Engine` above.

#### Custom styles engine example: Styl

Enjoy the power of happyplan & override style task with what you want !
We wrote a simple example using [Styl](https://github.com/visionmedia/styl) a flexible & solid alternative to Sass.
You can see it with the [test `override_task`](https://github.com/happyplan/happyplan/tree/master/test/features/override_task).
This example can be adapted easily to **Less** or something else if you really want to use it :).

### Glyphicons

The concept is simple: you can drop .svg files in the `_glyphicons` folder & all of them will be "compiled" into a webfont.
This will create all files format to be compatible with old browsers & also create a `_icons.scss` in the `build/assets/_styles` folder
(this path is normally added as a Sass include path).
This CSS file will contain a class for each svg.
Eg: you've put `home.svg`, so `icon_home` class will be accessible.

To use glyphicons directly in your html, you need to be sure that `_icons.scss` is included in your stylesheet (`@import "icons"` should be enough)
& use appropriate html classes.
Ex: `<i class="icon icon_home"></i>` (here `<i>` is just used because it's shorter, you can obviously use whatever you want).

By default, a node engine is used. If you are not happy with the result,
you can use fontforge (you need to install it) & activate it as the default engine.
After checking fontforge is correctly installed on you machine
(check with `fontforge --version`),
you will need to specify it in your `happyplan.json`:

```js
{
  "glyphicons": {
    "engine": "fontforge"
  }
}
```

Remember that if fontforge is not available in the PATH during the process, build will be skipped
(you will see a notification about the skip).

### Images

You can put whatever you want in the default `src/assets/_images` folder, but you need to know that by default, some file formats will be ignored to make the build lighter. Here is the default configuration.

```json
{
  "assets": {
    "images": {
      "src": [
        "**",
        "!**/*.ai",
        "!**/*.eps",
        "!**/*.pdf",
        "!**/*.pxm",
        "!**/*.psd",
        "!**/*.raw",
        "!**/*.xcf"
      ]
    }
  }
}
```

If you are not confortable with this settings you can changes everything, and for example just take the opposite approche and specify file formats you want :

```json
{
  "assets": {
    "images": {
      "src": [
        "**/*.BMP",
        "**/*.gif",
        "**/*.jpg",
        "**/*.jpeg",
        "**/*.png",
        "**/*.webp"
      ]
    }
  }
}
```

#### Optimization

Every images that can be optimized will be (when using `dist` task or `--env=dist`).
This include .jpeg & .png files.
To change default option below, just take a look to the [`imagemin` task](https://github.com/gruntjs/grunt-contrib-imagemin#imagemin-task) options.

```json
{
  "imagemin": {
    "optimizationLevel": 3,
    "progressive": true
  }
}
```

## Override tasks / Custom tasks

Enjoy the power of happyplan & override whatever you want !
You can see a simple example of an override of the style engine with the [test `override_task`](https://github.com/happyplan/happyplan/tree/master/test/features/override_task).
_This example can be adapted easily override whatever you want or need._
You should even be able to plug custom tasks very easily !

The idea is that you can create a `grunt_tasks` & override/add tasks respecting original happyplan worflow.
You can even change predefined tasks config in `grunt_tasks/config`.

[defaultconf]: https://github.com/happyplan/happyplan/blob/master/happyplan.json "default config"
