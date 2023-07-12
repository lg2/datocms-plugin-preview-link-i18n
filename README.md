# datocms-plugin-preview-link-i18n

Plugins that give your page url with i18n.

You'll need to create a json file in your public app like below.

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

If using nuxtJs you can use the following plugins to help you creating this file
* [create dato map nuxt modules](https://github.com/lg2/nuxtmodule-create-dato-map) 
* [link resolver nuxt plugin](https://github.com/lg2/nuxtplugin-link-resolver)

This plugins help you create an public json file on your web app to fit the routes on your app coralated with datoModelRecord.

