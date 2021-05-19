# TypeScript in Next.js

## How to install

1. Create empty `tsconfig.json` file in your root directory.

```
touch tsconfig.json
```

2. Install TypeScript

```
npm install --save-dev typescript @types/react @types/node
```

3. Start the devlopment server

Next.js will:

- Populate the `tsconfig.json` file. You may cusomize this file.
- Create `next-env.d.ts` file, which ensures Next.js types are picked up by the TypeScript compiler. You should not touch this file.

```
npm run dev
```

## Next.js specific Types

### Static Generation and Server-side Rendering

- getStaticProps -> GetStaticProps
- getStaticPaths -> GetStaticPaths
- getServerSideProps -> GetServerSideProps

Example:

```
import {GetStaticProps, GetStaticPaths, GetServerSideProps} from 'next'

export const getStaticProps: GetStaticProps = async context => {
    // ...
}

export const getStaticPaths: GetStaticPaths = async () => {
    // ...
}

export const getServerSideProps: GetServerSideProps = async context => {

}

```

### API Routes

```
import { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
    // ...
}

```

### Custom `App`

You can convert `page/_app.js` into `page/_app.tsx` and use the build-in type `AppProps` like this:

```
import { AppProps } from 'next'

function App({ Component, pageProps }: AppProps) {
    return <Component {...pageProps} />
}

export default App


```
