# Webpack Module Federation

Webpack Module Federation allows you to split your application into multiple bundles (or modules) that can be independently developed and deployed.

## __webpack_init_sharing__

__webpack_init_sharing__ is a function provided by Webpack 5's Module Federation feature. Its primary purpose is to facilitate the sharing of code between independently deployed applications or micro-frontends that are bundled using Webpack.

__webpack_init_sharing__ enables modules to share code with each other dynamically at runtime, rather than statically at build time.

This function is typically called with a **scope** name, which defines a boundary or namespace for shared modules.



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
