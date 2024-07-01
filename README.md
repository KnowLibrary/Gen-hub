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
