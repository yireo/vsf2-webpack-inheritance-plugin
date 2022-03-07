# Webpack plugin for Vue Storefront 2 theme inheritance

**DISCLAIMER: This approach has been tested only with VSF2 for Magento 2 and with the `components/` folder.**

Navigate into your custom app and install this package:

    yarn add @yireo/vsf2-webpack-inheritance-plugin

Next, open up your `nuxt.config.js` and modify your exported configuration so that it includes the following:

```js
const Vsf2ThemeInheritancePlugin = require('@yireo/vsf2-webpack-inheritance-plugin');
const path = require('path');

export default {
  build: {
    extend(config) {
      config.resolve.plugins = [
        new Vsf2ThemeInheritancePlugin({
          originalPath: path.resolve(__dirname, 'components'),
          newPath: path.resolve(__dirname, 'custom-components')
        })
      ]
    }
  }
}
```

Start overriding files. In the example above, component files like `TopBar.vue` in the folder `components/` can be overridden by adding
new files to a new `custom-components/` folder. 

This mechanism should work the same for pages, layouts and static files. Other files in other folders are best customized anyway.

