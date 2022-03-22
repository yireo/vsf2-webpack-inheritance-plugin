# Webpack plugin for Vue Storefront 2 theme inheritance

**DISCLAIMER: This approach has been tested only with VSF2 and the configurations outlined below.**

The following platforms are tested:

- VSF2 for Magento 2
- Shopware PWA (aka VSF2 for Shopware 6)

### Installation

Navigate into your custom app and install this package:

    yarn add @yireo/vsf2-webpack-inheritance-plugin

### Usage with VSF2 for Magento 2
Open up your `nuxt.config.js` and modify your exported configuration so that it includes the following:

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

### Overriding Storefront UI components in Shopware PWA
With Shopware PWA, components of the Nuxt Default Theme are already to be overridden in the `src/` folder. However, there is no default override mechanism for Storefront UI components. To add this via this Webpack plugin, open up the `nuxt.config.js` file and modify your exported configuration so that it includes the following:

```js
import extendNuxtConfig from "@shopware-pwa/nuxt-module/config"
const Vsf2ThemeInheritancePlugin = require('@yireo/vsf2-webpack-inheritance-plugin');
const path = require('path');

export default extendNuxtConfig({
  build: {
    extend(config) {
      config.resolve.plugins = [
        new Vsf2ThemeInheritancePlugin({
          originalPath: path.resolve(__dirname, 'node_modules', '@storefront-ui', 'vue', 'src', 'components'),
          newPath: path.resolve(__dirname, 'src', 'storefrontui')
        })
      ]
    }
  }
})
```

### Example Storefront UI component override with Shopware PWA
To override the original `SfBreadcrumbs` component with your own, copy the file `node_modules/@storefront-ui/vue/src/components/atoms/SfBreadcrumbs/SfBreadcrumbs.vue` to `sr/storefrontui/atoms/SfBreadcrumbs/SfBreadcrumbs.vue`. Beware that the Vue component contains an import for another component by using a relative path which no longer works:

```js
import SfLink from "../SfLink/SfLink";
```

This needs to be modified into:
```js
import { SfLink } from "@storefront-ui/vue"
```

### Disabling plugin slots with Shopware PWA
Shopware PWA offers the ability for local or remote PWA plugins to insert data into a dynamic component `SwPluginSlot`. But what if you don't want this behaviour at all? By using this Webpack plugin, you could rewrite the `SwPluginSlot` component to your own version:

```js
import extendNuxtConfig from "@shopware-pwa/nuxt-module/config"
const Vsf2ThemeInheritancePlugin = require('@yireo/vsf2-webpack-inheritance-plugin');
const path = require('path');

export default extendNuxtConfig({
  build: {
    extend(config) {
      config.resolve.plugins = [
        new Vsf2ThemeInheritancePlugin({
          originalPath: path.resolve(__dirname, '.shopware-pwa', 'sw-plugins', 'SwPluginSlot.vue'),
          newPath: path.resolve(__dirname, 'sw-plugins', 'SwPluginSlot.vue')
        })
      ]
    }
  }
})
```

And then, a new component `sw-plugins/SwPluginSlot.vue` could be created:
```vue
<template>
  <div v-text="name"></div>
</template>
<script>
export default {
  props: {
    name: {
      type: String,
      default: "",
    },
    slotContext: {
      type: Object | Array | String,
      default: null
    }
  }
};
</script>
```

This will destroy lots of functionality, but is just a demo of that the Webpack override works.

### Overriding Vue components of PWA plugins
You can also override the Vue components offered by remote PWA plugins. (With local PWA plugins, this makes sense: Just modify the files directly.) 

```js
import extendNuxtConfig from "@shopware-pwa/nuxt-module/config"
const Vsf2ThemeInheritancePlugin = require('@yireo/vsf2-webpack-inheritance-plugin');
const path = require('path');

export default extendNuxtConfig({
  build: {
    extend(config) {
      config.resolve.plugins = [
        new Vsf2ThemeInheritancePlugin({
          originalPath: path.resolve(__dirname, '.shopware-pwa', 'pwa-bundles-assets'),
          newPath: path.resolve(__dirname, 'sw-plugins')
        })
      ]
    }
  }
})
```

A Vue component `example.vue` located in the PWA plugin folder `.shopware-pwa/pwa-bundles-assets/swag-example/` can now be overriden in `sw-plugins/swag-example/example.vue`.
