# Gen-hub

## Micro frontends
* Allows to share code dynamically between two deployed applications
* Two ways to share code between applications:
  * Build time sharing
    * You have a library that has the shared code and the application(s) import that library
    * If the shared library is updated, then you need to deploy both the library and as well as the dependent applications.
  * Runtime sharing
    * You dynamically share the code at runtime
    * The dependent application will access the new version without any redeployment.
    * Code is shared as a component via Module federation  

## Webpack
### topLevelAwait experimental feature
Webpack’s **Module Federation** feature complements **topLevelAwait** in micro frontend applications. 
Module Federation allows sharing code between micro frontends and dynamically loading remote modules. 
topLevelAwait can simplify the asynchronous loading and initialization of these dynamically imported modules.

* **import.meta.url**: returns the absolute file url of the module.
* **output.publicPath**: Allows you to specify the base path for all the assets within your application.
* **DefinePlugin**: Allows you to define:
  * Environment variables that are needed during the build process
  * When defining values for process prefer 'process.env.NODE_ENV': JSON.stringify('production')
```
const webpack = require('webpack');

module.exports = {
  // other webpack configuration options
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('production'), // Example of defining NODE_ENV as 'production'
      API_URL: JSON.stringify('https://api.example.com'), // Example of defining an API URL constant
    }),
  ],
};

```
