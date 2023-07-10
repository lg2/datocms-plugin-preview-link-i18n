# datocms-plugin-preview-link-i18n

Plugins that give your page url with i18n.
Need to organise you web app with Nuxtjs using [create dato map nuxt modules](https://github.com/lg2/nuxtmodule-create-dato-map) and [link resolver nuxt plugin](https://github.com/lg2/nuxtplugin-link-resolver)

This plugins help you create an public xml file on your web app to fit the routes on your app coralated with datoModelRecord.

If not using this plugin youcan simply create an xml file like this in you public app.
```
[
    { "path": "/", "params": [], "model": "PageHomeRecord", locale: "en" },
    { "path": "/about", "params": [], "model": "PageAboutRecord", locale: "en" },
    { "path": "/contact", "params": [], "model": "PageContactRecord", locale: "en" },
    { "path": "/:slug", "params": ["slug"], "model": "PageSlugRecord", locale: "en" },
    { "path": "/", "params": [], "model": "PageHomeRecord", locale: "en" },
    { "path": "/a-propos", "params": [], "model": "PageAboutRecord", locale: "fr" },
    { "path": "/contact", "params": [], "model": "PageContactRecord", locale: "fr" },
    { "path": "/:slug", "params": ["slug"], "model": "PageSlugRecord", locale: "fr" },
]
```



## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
