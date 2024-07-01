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


## React.lazy

* React.lazy is a React API that allows you to lazily load components. Lazily loading components means that the component is loaded only when it’s needed, typically when it’s about to be rendered.

* React.lazy is primarily used in React applications where you want to split your bundle and load components asynchronously to improve initial load time.

* It’s commonly used with React Suspense to handle loading states while the component is being fetched.

## import()

* The import() function is a dynamic import mechanism introduced in ECMAScript (ES6) for dynamically loading modules

* It allows you to import modules conditionally or lazily (on-demand) at runtime.

* React.lazy with import() is used for dynamic, on-demand loading of React components. import is used for static, synchronous loading of modules.