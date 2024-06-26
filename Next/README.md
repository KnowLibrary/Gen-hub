# NEXT JS

## Environment Variables
* In Next.js, **process.env** is available by default in the next.config.js 
* Next.js automatically injects environment variables (in 9.4+ version) into the process.env object that is available at runtime.
* To create environment variables:
  * In the root directory of your Next.js project create a **.env** file.
  * You can create different **.env** files for different environments.
  * Next.js automatically loads variables from .env files into process.env during development.
  * Variable defined in .env file with prefix **NEXT_PUBLIC_** are exposed to the browser.
  * Avoid committing .env files by listing then in your **.gitignore** file.
  * Variables defined in .env are overridden by those defined in more environment specific .env files.
  * .env.local is used to define environment variables that are specific to your local development environment.
    
## getServerSideProps function
1. Function that can be used to fetch data and render the contents of a page at request time.
2. Runs on the server.
3. It can only be exported from a page. This means it should be exported from a file inside the pages directory alongside your React component that defines the page.      
4. Returns JSON object that contains the **props**. 
5. props passed to the page component can be viewed on the client as part of the initial HTML.
6. If you do not need to fetch the data at request time, or would prefer to cache the data and pre-rendered HTML, we recommend using getStaticProps.
7. The **context** parameter is an object that allows
   * access to request headers using **req** key
   * access to response object using **res** key
   * access to query parameters of the URL including dynamic route parameters using **query** key.
   * access to slug parameter through **context.params**


### Example
    ```
    export async function getServerSideProps(context) {
      // Fetch data from external API
      const res = await fetch('https://api.github.com/repos/vercel/next.js')
      const repo = await res.json()
      // Pass data to the page via props
      return { props: { repo } }
    }
    
    export default function Page({ repo }) {
      return (
        <main>
          <p>{repo.stargazers_count}</p>
        </main>
      )
    }
    ```

## getStaticProps (Static Site Generation)
1. Next will pre-render the page at build time using the props returned by the function.
2. Function always run on the server and never on the client.
3. It always run during next build.
4. The data required to render the page is available at build time ahead of a user’s request.
5. getStaticProps can only be exported from a page. You cannot export it from non-page files, _app, _document, or _error. This means 

### Example
```
export async function getStaticProps() {
  const res = await fetch('https://api.github.com/repos/vercel/next.js')
  const repo = await res.json()
  return { props: { repo } }
}
 
export default function Page({ repo }) {
  return repo.stargazers_count
}
```

## Client Side Fetching ( SWR)
1. Next has created a React hook library for data fetching called **SWR**.
2. It is highly recommended if you are fetching data on the client-side.
3. It handles caching, revalidation, focus tracking, refetching on intervals, and more.

### Example
```
import useSWR from 'swr'
 
const fetcher = (...args) => fetch(...args).then((res) => res.json())
 
function Profile() {
  const { data, error } = useSWR('/api/profile-data', fetcher)
 
  if (error) return <div>Failed to load</div>
  if (!data) return <div>Loading...</div>
 
  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.bio}</p>
    </div>
  )
}
```


## Server include in Next
* **next dev** command starts a development start customized by Next.js and is based on **webpack-dev-server** concept.
* **next start** command starts a production ready server customzied by Next.js and is based on **Node.js's http module**.
   

## Custom Server (Available only in Page Router)
You can use a Node.js express custom server to start the Next.js application.
* Define server.js

  ```
  const { createServer } = require('http')
  const { parse } = require('url')
  const next = require('next')
   
  const dev = process.env.NODE_ENV !== 'production'
  const hostname = 'localhost'
  const port = 3000
  // when using middleware `hostname` and `port` must be provided below
  const app = next({ dev, hostname, port })
  const handle = app.getRequestHandler()
   
  app.prepare().then(() => {
    createServer(async (req, res) => {
      try {
        // Be sure to pass `true` as the second argument to `url.parse`.
        // This tells it to parse the query portion of the URL.
        const parsedUrl = parse(req.url, true)
        const { pathname, query } = parsedUrl
   
        if (pathname === '/a') {
          await app.render(req, res, '/a', query)
        } else if (pathname === '/b') {
          await app.render(req, res, '/b', query)
        } else {
          await handle(req, res, parsedUrl)
        }
      } catch (err) {
        console.error('Error occurred handling', req.url, err)
        res.statusCode = 500
        res.end('internal server error')
      }
    })
      .once('error', (err) => {
        console.error(err)
        process.exit(1)
      })
      .listen(port, () => {
        console.log(`> Ready on http://${hostname}:${port}`)
      })
  })
  ```

* Update the scripts in package.json
  ```
  {
   "scripts": {
     "dev": "node server.js",
     "build": "next build",
     "start": "NODE_ENV=production node server.js"
   }
  }
  ```
* Custom server option is available only in Page Router functionality

## Steps to create Nextjs project 
* Nextjs team recommends using **create-next-app** to setup project.
* To create project that usee page router feature:
  * Run: npx create-next-app@latest
  * Answer below prompts:
    * What is your project named? **my-app**
    * Would you like to use TypeScript? No / Yes
    * Would you like to use ESLint? No / Yes
    * Would you like to use Tailwind CSS? No / Yes
    * Would you like to use `src/` directory? No / Yes
    * Would you like to use App Router? (recommended) **No** / Yes
    * Would you like to customize the default import alias (@/*)? No / Yes


## Other details
* The default port (3000) can be changed by using **PORT** environment variable.
```
PORT=4000 next dev
```


