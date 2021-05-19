# Next.js

## How to set up

```
npx create-next-app [name]
```

## How to run

```
npm run dev
```

### How to create new page

1. add a new directory under `pages`
2. create a file
3. create a component

The component can have any name.
The routing is based on the file name.
Don't forget to export it as a `default` export.

```
export default function FirstPost() {
  return <h1>First Post</h1>;
}
```

## Specification

## Routing

### file system routing

- When a file is added to `pages` directory, it's automatically available as a route
- Pages are associated with a route based on their file name

Examples

- `page/index.js` is associated with `/` route
- `page/blog/blog-post.js` is associated with `blog/blog-post.js` route

No routing libraries are required for Next.js.

### link component

- You use `Link` component from `next/link`
- Wrap `a` tag with `Link` component
- `<Link>` allows you to do client-navigation to a different page in the app

Eample

```
<Link href="/posts/first-post">
    <a>link</a>
</Link>
```

Client-side navigation means that the transition happens using JavaScript, which is faster than the default navigation done by the browser.
The browser doesn't load the whole page.

If you use `<a href="/">link</a>`, the brower load the full page and client-side navigation won't occure.
You can use `a` tag for an external link.
If you need to add an attribute like 'className', add it to the `a' tag

## Assets

Next.js can serve static file under the top-level `public` directory.

### Image

Next.js has support for image optimaization by default. The images are automatically resized and optimized.
Next.js optimies images on-demand, as users request them.

```
import Image from 'next/image'

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
)
```

## Metadata

### head

Next.js has `<Head></Head>` component.

## CSS Modules

Next.js supports CSS Modules using `[name].modules.css` file naming convention.

CSS allows you to use same CSS class name in a different file since CSS Modules creates a uniqu class name automatically.

## Global CSS

If you want to load CSS every page, you can create global css.
To load global CSS, you need to create a file called `pages/_app.js`.
You only can import global CSS in `pages/_app.js`.

Example

```
import '../styles/global.css'

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

## Pre-rendering

Next.js pre-renders every page by default. This means Next.js renders them in the server.
When a page is loaded by the browser, its JavaScript code runs and makes the page fully interactive.

### Static Generation vs Server-side rendering

The difference is in when it generates the HTML for a page.

#### Static Generation

The HTML is generated at _build time_.

- When to use?
  Whenever possible. It's much faster than having a server render the page on every request.

#### Server-side rendering

The HTML is generated on _each request_.
Pre-rended page is always up-to-date.

- When to use?
  When the data needs to be up-to-date with every request.
  If you cannnot pre-render a page ahead of user's request. Your page shows often updated data and the page content changes on every request.

#### Hybrid rendering

Next.js let you choose which pre-rendering form to use for each page.

### Static Generation with Data using `getStaticProps`

HTML is gegerated after fetching data.
When the data is updated, NEXT.js will be rebuilt and regenerate new HTML.

Example

```
export default function Home(props) {...}

export async function getStaticProps() {
    // get external data from DB or API
    const data = ...

    // The value of the `props` key will be passed to the Home component
    return {
        props: ...
    }
}
```

- getStaticProps runs at build time
- you can fetch data inside the function and pass it as props
- in dev mode getStaticProps runs on each request instead

`getStaticProps` _only runs on the server-side_. It will never run on the client-side.

### getServerSideProps

Example

```
export async function getServerSideProps(context) {
    return {
        props: {
            // props for your component
        }
    }
}
```

`context` contains rquest specific parameters.

## Client-side rendering

- Statically generate (pre-render) parts of the page that do not require external data
- When the page loads, fetch external data from the client using JavaScript and populate the remaining parts

You can pre-render without data and then load the external data on the client-side.

Client-side rendering works well for user dashboard. Because it's SEO irrelevant and private and user-specific data.

### SWR

- a React hook for data fetching
- this handls
  - caching
  - revalidation
  - focus traking
  - refetching on interval

```
import useSWR from 'swr'
function Profile() {
    const {data, error} = useSWR('api/user', fetch)

    if(error) return <div>failed to load</div>
    if(!data) return <div>loading..</div>
    return <div>{data.name} {data.dateOfBirth}</div>
}
```

## dynamic routes

### different page path depends on external data

1. create a page `pages/[id]`. `[]` is for dynamic routes.
2. wirte React component in the file
3. write `getStaticPaths` which returns the ids
4. write `getStaticProps` which fetches data with the ids

#### Implement `getStaticPaths`

It must be an array of object.
And the array needs to contain `params` key and the key you choose for the page.
This time it should look like this.

```
{
    params: {
        id: 'page id 1'
    },
    params: {
        id: 'page id 2'
    },
}

```

#### data flow

How to statically generate pages with dynamic routes.

The data flow:
`getStaticPath` -> `getStaticProps` -> react component

`pages/sample/[id]`

```
export async function getStaticPaths() {
  const paths = getAllPostsIds();

  return {
    paths,
    fallback: false,
  };
}
```

The `paths` parameter is passed to `getStaticProps` as `params`.
Be careful with the data shape of `paths`.

`pages/sample/[id]`

```
export async function getStaticProps({ params }) {
  const postData = getPostData(params.id);

  return {
    props: {
      postData: postData,
    },
  };
}
```

This `props` is passed to the react component in the same file as props.

```xport default function Post(props) {
  const { postData } = props;
  return (
    <Layout>
      {postData.title}
      <br />
      {postData.id}
      <br />
      {postData.date}
      <br />
    </Layout>
  );
}
```

#### What is fallback of `getStaticPaths`?

Bacically if you set fallback false, Next.js generate a 404 page and display it automatically when you access the page which does not exist.

If fallback is false, then any path not returned by getStaticPath will result in a 404 page.

If fallback is true, it behaves like this:

- the returned paths will be rendered to HTML at build time
- The paths that have not been generated at build time will not result in a 404 page. Instead, Next.js will serve a “fallback” version of the page on the first request to such a path.
- In the background, Next.js will statically generate the requested path. Subsequent requests to the same path will serve the generated page, just like other pages pre-rendered at build time.

#### Catch-all routes

Dynamic routes can catch all paths by adding `three dots ...` inside the brackets.

- `pages/sample/[...id].js` matches `posts/a`, but also `posts/a/b` and `posts/a/b/c`

To do that you have to return array of ids.

```
return [
    {
        params: {
            id: ['a', 'b', 'c']
        }
    }
]
```

#### custom 404 page

You can create `pages/404.js`. This file is created at build time.

`pages/404.js`

```
export default function Custom404() {
    return <h1>404 Page not found</h1>
}
```

## Creating API Routes

API routes let you create an API endpoint inside a Next.js app.
You can make funcitons inside the `pages/api` directory.
The api functions are deployed as a Serverless Function.
