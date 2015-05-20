# django-webpack-loader

[![Join the chat at https://gitter.im/owais/django-webpack-loader](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/owais/django-webpack-loader?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

<br>

**NOTE:** *This is very very new. Do not use in proudction yet.*

<br>

Use webpack to generate your static bundles without django's staticfiles or opaque wrappers. 


Django webpack loader consumes the output generated by [webpack-bundle-tracker](https://github.com/owais/webpack-bundle-tracker) and lets you use the generated bundles in django.


<br><br>
### Configuration

#### Assumptions

Assuming `assets/` is in `settings.STATICFILES_DIRS` like
```python
STATICFILES_DIRS = (
	os.path.join(BASE_DIR, 'assets'),
)
```

<br>
Assuming your webpack config lives at `./assets/webpack.config.js` and looks like this

```
module.exports = {
  context: __dirname,
  entry: {
    app: './js/index'
    output: {
      path: require("path").resolve('./assets/bundles/'),
      filename: "[name]-[hash].js",
  },

  plugins: [
    new BundleTracker({filename: './assets/webpack-stats.json'})
  ]
}

```


<br>


#### Default Configuration
```python
WEBPACK_LOADER = {
    'BASE_URL': 'webpack_bundles/',
    'STATS_FILE': 'webpack-stats.json',
    'POLL_DELAY': 0.5,
    'IGNORE': ['.+\.hot-update.js', '.+\.map']
}
```

<br>

##### WEBPACK_BUNDLE_URL
```python
WEBPACK_LOADER = {
	'BASE_URL': STATIC_URL + 'bundles/'
}
```

BASE_URL is used to build the complete public url to the bundle that is used in `<script>` or `<link>` tags.

If the bundle generates a file called `main-cf4b5fab6e00a404e0c7.js`, then with the above config, the `<script>` tag will look like this

```html
<script type="text/javascript" src="/assets/bundles/main-cf4b5fab6e00a404e0c7.js"/>
```

<br>

##### STATS_FILE
```python
WEBPACK_LOADER = {
	'STATS_FILE': os.path.join(BASE_DIR, 'assets/webpack-stats.json')
}
```

STATS_FILE is the filesystem path to the file generated by `webpack-bundle-tracker` plugin. If you initialize `webpack-bundle-tracker` plugin like this

```javascript
new BundleTracker({filename: './assets/webpack-stats.json'})
```

and your webpack config is located at `/home/src/assets/webpack.config.js`, then the value of `STATS_FILE` should be `/home/src/assets/webpack-stats.json`

<br>

##### IGNORE
IGNORE is a list of regular expressions. If a file generated by webpack matches one of the expressions, the file will not be included in the template.
<br><br><br>

### Usage

##### Templates

```HTML+Django
{% load render_bundle from webpack_loader %}

{{ render_bundle 'main' }}
```

`render_bundle` will render the proper `<script>` and `<link>` tags needed in your template.

<br>
Enjoy your webpack with django :)
