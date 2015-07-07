# Stencil

The building block for Bigcommerce theme developers to get started quickly developing premium quality themes on the Bigcommerce platform.

### Stencil Utils
[Stencil-utils](https://github.com/bigcommerce/stencil-utils) is our supporting library for our events and remote interactions. It is a module that is managed by [JSPM](http://jspm.io)
and requires installation. [Stencil-utils](https://github.com/bigcommerce/stencil-utils) is located in a private repo for now, so ensure you follow the next section.
### Installing JSPM
* Ensure that `npm` is installed.
* Open a terminal and run `npm install -g jspm` this will install JSPM globally.
* Have Fun!

### JSPM Private Repos
Create an access token [here](https://github.com/settings/applications) that will be used by JSPM to access the private Github repos.
Once you have your token run `jspm registry config github` and input your credentials. This will create a Github jspm configuration.
Once this is complete you are ready for the next step.

##JS API
When writing theme javascript there is an API in place for running javascript on a per page basis. To properly write JS for your theme you will have the following
page types available to you.

* "pages/account/addresses"
* "pages/account/add-address"
* "pages/account/add-return"
* "pages/account/add-wishlist"
* "pages/account/recent-items"
* "pages/account/download-item"
* "pages/account/edit"
* "pages/account/return-saved"
* "pages/account/returns"
* "pages/auth/login"
* "pages/auth/account-created"
* "pages/auth/create-account"
* "pages/auth/new-password"
* "pages/blog"
* "pages/blog-post"
* "pages/brand"
* "pages/brands"
* "pages/cart"
* "pages/category"
* "pages/compare"
* "pages/errors"
* "pages/gift-certificate/purchase"
* "pages/gift-certificate/balance"
* "pages/gift-certificate/redeem"
* "global"
* "pages/home"
* "pages/order-complete"
* "pages/page"
* "pages/product"
* "pages/search"
* "pages/sitemap"
* "pages/subscribe"
* "page/account/wishlist-details"
* "pages/account/wishlists"

And these page types will correspond to the pages within your your theme. Each one of these page types map to an ES6 modules that extends the base `PageManager` abstract class.
```javascript
    export default class Auth extends PageManager {
        constructor() {
            //Set up code goes here, attach to internals and use internals as you would 'this'
        }
    }
```
Within `PageManager` you will methods that are available from all your classes, but there are three really important methods. The following methods have the signature
`func (callback)` and the callback takes `callback(err)` if error.

#### before(callback)
When this method is implemented in your class the code contained will be executed after the constructor but before the `loaded()` method. This will provide
a shim for your code before your main implementation logic could run.
```javascript
    export default class Auth extends PageManager {
        constructor() {
            //Set up code goes here
        }
        before(callback) {
            //code that should be ran before anyother code in this class

            //Callback must be called to move on to the next method
            callback();
        }
    }
```
#### loaded(callback)
This method will be called when the constructor has ran and `before()` has executed. Main implementation code should live in the loaded method.
```javascript
    export default class Auth extends PageManager {
        constructor() {
            //Set up code goes here
        }
        loaded(callback) {
            //Main implementation logic here

            //Callback must be called to move on to the next method
            callback();
        }
    }
```

#### after(callback)
This method is for any cleanup that may need to happen and will be executed after `before()` and `loaded()`

```javascript
    export default class Auth extends PageManager {
        constructor() {
            //Set up code goes here
        }
        after(callback) {
            //Main implementation logic here

            //Callback must be called to move on to the next method
            callback();
        }
    }
```

### JS Template Context Injection
Occasionally you may need to use dynamic data from in the template context within your clientside theme application code.

Two helpers are provided to help achieve this.

The inject helper allows you to compose a json object with a subset of the template context to be sent to the browser.
```
{{@inject "stringBasedKey" contextValue}}
```

To retrieve the parsable json object, just call `{{jsContext}}` after all of the `{{@inject}}` calls.

For example, to setup the product name in your clientside app, you can do this if you're in the context of a product.
```html
{{@inject "myProductName" product.title}}

<script>
// Note the lack of quotes around the jsContext handlebars helper, it becomes a string automatically.
var jsContext = JSON.parse({{jsContext}}); //jsContext would output "{\"myProductName\": \"Sample Product\"}" which can feed directly into your javascript

console.log(jsContext.myProductName); // Will output: Sample Product
</script>
```

You can compose your json object across multiple pages to create a different set of clientside data depending on the currently loaded template context.

The stencil theme makes the jsContext available on both the active page scoped and global PageManager objects as `this.context`


## Static assets
Some static assets in the Stencil theme are handled with Grunt if required. This
means you have some dependencies on grunt and npm. To get started:

First make sure you have Grunt installed globally on your machine:
```
npm install -g grunt-cli
```
and run:
```
npm install
```

### Icons
Icons are delivered via a single SVG sprite, which is embedded on the page in
`templates/layout/base.html`. It is generated via a grunt task `grunt svgstore`.

The task takes individual SVG files for each icon, in `assets/icons` and bundles
them together, to be inlined on the top of the theme, inside a handlebars partial.
Each icon can then be called in a similar way to an inline image via:

```
<svg><use xlink:href="#icon-svgFileName" /></svg>
```

The ID of the SVG icon you are calling is based on the filename of the icon you want,
with `icon-` prepended. e.g. `xlink:href="#icon-fabeook"`.

Simply add your new icon SVG file to the icons folder, and run `grunt svgstore`,
or just `grunt`.

