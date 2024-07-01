# Webpack Module Federation

* Webpack Module Federation is a powerful feature introduced in Webpack 5 that enables developers to dynamically share modules across multiple applications at runtime. It allows you to build a system of micro-frontends where each part of your application is developed and deployed independently.

* Each micro-frontend application is built independently using Webpack configuration.

* Webpack Module Federation allows you to split your application into multiple bundles (or modules) 

* A container in Module Federation is essentially a webpack build that exposes specific modules or chunks to be consumed remotely by other webpack builds. It acts as a provider of shared or standalone modules that can be dynamically loaded and used by remote applications.

* The container specifies which modules or chunks are available for consumption by other webpack builds. These modules are typically defined in the **exposes** configuration of the webpack configuration

* The **exposes** configuration in the Module Federation plugin (ModuleFederationPlugin) tells webpack which modules or chunks from the container should be made available for remote consumption. These modules are typically JavaScript files that export specific components, functions, or other resources.

* The modules specified in exposes are bundled into the container's output during webpack **build time**. This means webpack processes these modules to generate the necessary output files.

* While the modules themselves are bundled into the container’s build output during webpack build time, they are not "loaded" in the sense of being executed or consumed by other applications until runtime.

* The **remote** configuration in the Module Federation plugin (ModuleFederationPlugin) of a consuming application specifies which remote containers to integrate modules from. 

* During webpack build time of the consuming application, webpack processes the remote configuration to understand where to fetch remote modules from. It checks the specified URLs or paths to gather information about what modules are available remotely.

* The actual loading of modules from remote containers occurs at runtime, not during build time. 

* Containers can also define shared modules that they provide to ensure that multiple consuming applications use the same version of certain dependencies (**shared** configuration).

* Containers also need to initialize module sharing (__webpack_init_sharing__) and define share scopes (__webpack_share_scopes__) to specify which modules are available and how to load them dynamically.

* Every file in your project is a **Module**.
* By using each other, the modules form a graph.
* During the bundling process, modules are combined into **chunks**. 
* Chunks combine into chunk groups and form a group interconnected through modules.
* One chunk group with **main** default name get created.
* Chunks come in two forms:
  * **initial** is the main chunk for the entry point.
  * **non-initial** is a chunk that may be lazy-loaded.

## Dynamically Load Micro Frontend (JS file)
```
const loadDynamicScript = (url, globalName) => {
    return new Promise<any>((resolve, reject) => {
        const script = document.createElement('script');
        script.src = url;
        script: onerror = () => {
            reject(new Error(`Failed to fetch remote: ${globalName}`));
        }
        script.onload = () => {
            const proxy = {
                get: (request) => window[globalName].get(request),
                init: (arg) => {
                    try {
                        return window[globalName].init(arg);
                    } catch (e) {
                        reject(e);
                    }
                }
            }
            resolve(proxy);
        }
        document.head.appendChild(script);
    })
}

const loadRemoteModule = (bundleFileName, basePath, moduleName) => async () => {
    const { hostName } = window.location.hostname;
    const url = `https://${hostName}/${basePath}/${bundleFileName}`;

    // Initializes the shared scope. Fills it with known provided modules from this build and all remotes
    await __webpack_init_sharing__('default');
    const container = await loadDynamicScript(url, moduleName); // or get the container somewhere else
    // Initialize the container, it may provide shared modules
    await container.init(__webpack_share_scopes__.default);
    const factory = await window['default'].get(module);
    const Module = factory();

    return Module;
}
```

## Dynamically Load Micro Frontend (TS file)
```
const loadDynamicScript = (url: string, globalName: string) => {
    return new Promise<any>((resolve, reject) => {
        const script = document.createElement('script');
        script.src = url;
        script: onerror = () => {
            reject(new Error(`Failed to fetch remote: ${globalName}`));
        }
        script.onload = () => {
            const proxy = {
                get: (request: any) => (window as any)[globalName].get(request),
                init: (arg: any) => {
                    try {
                        return (window as any)[globalName].init(arg);
                    } catch (e) {
                        reject(e);
                    }
                }
            }
            resolve(proxy);
        }
        document.head.appendChild(script);
    })
}

const loadRemoteModule = (bundleFileName: string, basePath: string, moduleName: string) => async() => {
    const { hostName } = window.location.hostname;
    const url = `https://${hostName}/${basePath}/${bundleFileName}`;
    
     // Initializes the shared scope. Fills it with known provided modules from this build and all remotes
    await __webpack_init_sharing__('default');
    const container = await loadDynamicScript(url, moduleName); // or get the container somewhere else
    // Initialize the container, it may provide shared modules
    await container.init(__webpack_share_scopes__.default);
    const factory = await window['default'].get(module);
    const Module = factory();

    return Module;
}
```


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
