# Gen-hub

# Webpack
## topLevelAwait experimental feature
Webpack’s Module Federation feature complements topLevelAwait in micro frontend applications. Module Federation allows sharing code between micro frontends and dynamically loading remote modules. topLevelAwait can simplify the asynchronous loading and initialization of these dynamically imported modules.

# NEXT JS
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


   
