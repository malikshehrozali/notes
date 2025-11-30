# Next.js — The Complete Guide (TypeScript)

> **Scope:** A comprehensive guide to Next.js (both Pages Router and App Router), written with TypeScript examples. Covers routing, params, search/query handling, API routes, data fetching, rendering modes, middleware, deployment, and advanced topics.

---

## Table of contents

1. [Introduction](#introduction)
2. [Getting started & setup (TypeScript)](#getting-started--setup-typescript)
3. [Project structure and conventions](#project-structure-and-conventions)
4. [Pages Router (legacy) — routing, data fetching, APIs](#pages-router-legacy)
5. [App Router (recommended) — layouts, server components, routing](#app-router-recommended)
6. [Routing deep dive (static, dynamic, catch-all)](#routing-deep-dive)
7. [URL params, useParams, search params, query strings](#url-params-useparams-search-params-query-strings)
8. [Data fetching strategies and caching](#data-fetching-strategies-and-caching)
9. [API endpoints: Pages API routes & App Route Handlers](#api-endpoints-pages-api-routes--app-route-handlers)
10. [Server vs Client Components & hydration](#server-vs-client-components--hydration)
11. [Middleware, rewrites, redirects, headers](#middleware-rewrites-redirects-headers)
12. [Authentication patterns](#authentication-patterns)
13. [Image optimization, static assets, and fonts](#image-optimization-static-assets-and-fonts)
14. [Internationalization (i18n)](#internationalization-i18n)
15. [Testing & tooling (TypeScript, ESLint, Prettier, vitest/Jest)](#testing--tooling)
16. [Deployment & Hosting tips](#deployment--hosting-tips)
17. [Examples and recipes](#examples-and-recipes)
18. [References & further reading](#references--further-reading)

---

## Introduction

Next.js is a React framework that adds file-based routing, server-side rendering (SSR), static site generation (SSG), API routes, image optimization, and more. As of modern Next.js, there are **two routing systems**:

- **Pages Router** (`pages/`) — the traditional approach. Uses `getStaticProps`, `getServerSideProps`, `getStaticPaths` and `pages/api` for APIs.
- **App Router** (`app/`) — the newer, recommended approach introducing React Server Components, nested layouts, and a different data fetching model.

This guide uses TypeScript for all examples.

---

## Getting started & setup (TypeScript)

### Create a new Next.js app (with TypeScript)

```bash
# Using create-next-app
npx create-next-app@latest my-next-app --ts
# or
pnpm create next-app my-next-app --ts
# or using yarn
yarn create next-app --typescript
```

This bootstraps a Next.js + TypeScript project.

### Key `tsconfig.json` tips

- Keep `strict: true` for stricter type safety.
- Enable `forceConsistentCasingInFileNames`.

Example snippet:

```json
{
  "compilerOptions": {
    "target": "es2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "strict": true
  }
}
```

---

## Project structure and conventions

Common files/folders you'll see in Next.js apps:

```
my-next-app/
├─ app/             # App Router (recommended)
├─ pages/           # Pages Router (legacy)
├─ public/          # static assets served from root
├─ styles/          # css / globals
├─ next.config.js   # Next.js config
├─ package.json
└─ tsconfig.json
```

**Note:** You can have both `app/` and `pages/` in the same project but prefer one router to avoid confusion.

---

## Pages Router (legacy)

> Useful when migrating older apps. Examples:

### Basic page

`pages/index.tsx`:

```tsx
import React from 'react';

const HomePage: React.FC = () => {
  return <div>Welcome to Next.js Pages Router (TypeScript)</div>;
};

export default HomePage;
```

### Data fetching: `getStaticProps`

Used for static generation at build time.

```tsx
// pages/posts.tsx
import { GetStaticProps } from 'next';

type Post = { id: string; title: string };

export const getStaticProps: GetStaticProps = async () => {
  const posts: Post[] = [{ id: '1', title: 'Hello' }];
  return { props: { posts } };
};

const PostsPage: React.FC<{ posts: Post[] }> = ({ posts }) => (
  <ul>{posts.map(p => <li key={p.id}>{p.title}</li>)}</ul>
);

export default PostsPage;
```

### `getServerSideProps` (SSR)

```tsx
// pages/ssr.tsx
import { GetServerSideProps } from 'next';

export const getServerSideProps: GetServerSideProps = async (context) => {
  const userAgent = context.req.headers['user-agent'] ?? '';
  return { props: { userAgent } };
};

const SSRPage: React.FC<{ userAgent: string }> = ({ userAgent }) => (
  <div>User agent: {userAgent}</div>
);

export default SSRPage;
```

### `getStaticPaths` for dynamic routes

```tsx
// pages/posts/[id].tsx
import { GetStaticPaths, GetStaticProps } from 'next';

export const getStaticPaths: GetStaticPaths = async () => {
  return {
    paths: [{ params: { id: '1' } }],
    fallback: false
  };
};

export const getStaticProps: GetStaticProps = async (context) => {
  const id = context.params?.id;
  return { props: { id } };
};

const PostPage = ({ id }: { id: string }) => <div>Post {id}</div>;
export default PostPage;
```

### API routes (Pages)

`pages/api/hello.ts`:

```ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default function handler(req: NextApiRequest, res: NextApiResponse) {
  res.status(200).json({ message: 'Hello from Next API (pages)' });
}
```

---

## App Router (recommended)

> Uses `app/` directory with nested folders, layouts, server components, client components, and `route.ts` for API endpoints.

### Basic page and layout

```
app/
├─ layout.tsx      # root layout
├─ page.tsx        # routes root page
├─ dashboard/
   ├─ layout.tsx   # nested layout
   └─ page.tsx
```

`app/layout.tsx` (root layout):

```tsx
import './globals.css';
import React from 'react';

export const metadata = {
  title: 'My App',
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

`app/page.tsx`:

```tsx
export default function Home() {
  return <main>Welcome to the App Router Home</main>;
}
```

### Server components vs Client components

- Files under `app/` are **Server Components** by default. They run on the server and can fetch data directly.
- To create a Client Component, add `'use client'` at the top.

Example client component:

```tsx
// app/components/Counter.tsx
'use client';
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>Count: {count}</button>
    </div>
  );
}
```

### Fetching in App Router (server components)

```tsx
// app/users/page.tsx
async function getUsers() {
  const res = await fetch('https://jsonplaceholder.typicode.com/users', { cache: 'no-store' });
  return res.json();
}

export default async function UsersPage() {
  const users = await getUsers();
  return (
    <div>
      <h1>Users</h1>
      <ul>{users.map((u: any) => <li key={u.id}>{u.name}</li>)}</ul>
    </div>
  );
}
```

**Note:** `fetch` in server components can use `cache: 'no-store'`, `cache: 'force-cache'`, or `next: { revalidate }` to control ISR.

### Route Handlers (app API)

`app/api/hello/route.ts`:

```ts
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ message: 'Hello from app route' });
}

export async function POST(request: Request) {
  const body = await request.json();
  return NextResponse.json({ data: body });
}
```

---

## Routing deep dive

### Static routes

Files `app/about/page.tsx` or `pages/about.tsx` map to `/about`.

### Dynamic routes (App Router)

Create a folder with square brackets: `app/posts/[id]/page.tsx`.

```tsx
// app/posts/[id]/page.tsx
import { notFound } from 'next/navigation';

type Props = { params: { id: string } };

export default async function PostPage({ params }: Props) {
  const { id } = params;
  // fetch post
  if (!id) notFound();
  return <div>Post {id}</div>;
}
```

### Catch-all and optional catch-all

- `app/[...slug]/page.tsx` captures `/a/b/c`
- `app/[[...slug]]/page.tsx` is optional; captures `/` or `/a/b`

### Parallel routes and segment configs

App router supports advanced patterns like parallel routes and intercepting routes — check docs for `@` prefixed parallel folders.

---

## URL params, useParams, search params, query strings

### `params` (App Router)

In server components under `app/`, `params` is passed into the page component.

```tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <div>{params.slug}</div>;
}
```

### `useParams` (client components)

`useParams` only works in client components. Example:

```tsx
'use client';
import { useParams } from 'next/navigation';

export default function MyClient() {
  const params = useParams();
  return <div>id: {params?.id}</div>;
}
```

### `useSearchParams` (client)

```tsx
'use client';
import { useSearchParams } from 'next/navigation';

export default function Searcher() {
  const searchParams = useSearchParams();
  const q = searchParams.get('q');
  return <div>search q = {q}</div>;
}
```

### Reading search params on the server

In server components you can access search params passed in `searchParams` prop of the page (if you define the page to accept it) or via `URL` objects when using route handlers.

Example:

```tsx
// app/products/page.tsx
export default async function ProductsPage({ searchParams }: { searchParams: { q?: string } }) {
  const q = searchParams.q;
  return <div>Query: {q}</div>;
}
```

### Query strings (client side navigation)

Use `useRouter` (pages router) or `useSearchParams` + `useRouter` (app router client components) to push URL changes.

```tsx
// app/components/SearchForm.tsx
'use client';
import { useRouter } from 'next/navigation';
import { useState } from 'react';

export default function SearchForm() {
  const [q, setQ] = useState('');
  const router = useRouter();
  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        router.push(`/products?q=${encodeURIComponent(q)}`);
      }}
    >
      <input value={q} onChange={e => setQ(e.target.value)} />
      <button type="submit">Search</button>
    </form>
  );
}
```

---

## Data fetching strategies and caching

### Pages Router

- `getStaticProps` — static generation at build time (SSG).
- `getStaticPaths` — generate dynamic routes for SSG.
- `getServerSideProps` — server-side rendering on every request.
- `Incremental Static Regeneration (ISR)` — use `revalidate` with `getStaticProps` to regenerate.

### App Router

- **Server Components**: fetch server-side using `fetch()` or direct DB calls.
- `fetch` options: `{ cache: 'no-store' }`, `{ cache: 'force-cache' }`, or `{ next: { revalidate: 60 } }` for ISR.
- `use` and React Suspense: integrate with streaming and suspense to progressively render.

Example ISR in App Router:

```tsx
const res = await fetch('https://api...', { next: { revalidate: 60 } });
```

### Client-side fetching

Use React Query, SWR, or `useEffect` for client-side fetching. Example with SWR (TypeScript):

```tsx
'use client';
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(r => r.json());

export default function ClientData() {
  const { data, error } = useSWR('/api/data', fetcher);
  if (error) return <div>Error</div>;
  if (!data) return <div>Loading</div>;
  return <div>{JSON.stringify(data)}</div>;
}
```

---

## API endpoints: Pages API routes & App Route Handlers

### Pages API route (pages/api)

```ts
// pages/api/todos.ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default function handler(req: NextApiRequest, res: NextApiResponse) {
  if (req.method === 'GET') {
    res.status(200).json([{ id: 1, title: 'Do this' }]);
  } else {
    res.status(405).end();
  }
}
```

### App route handler (app/api)

```ts
// app/api/todos/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json([{ id: 1, title: 'From app route' }]);
}

export async function POST(req: Request) {
  const body = await req.json();
  return NextResponse.json({ created: true, body });
}
```

**Differences:** App routes use Web Fetch API (`Request`, `Response`, `NextResponse`) and run as edge/Node depending on config.

---

## Server vs Client Components & hydration

- **Server Components** run on server, can access server-only secrets, and don't include client JS by default.
- **Client Components** include `'use client'` and can use state/hooks/effects, but increase bundle size.

**Rule of thumb:** Keep as much logic as possible in server components; mark only interactive parts as client.

### Example split

Server component (fetches data) renders a client `LikeButton`:

```tsx
// app/post/[id]/page.tsx (server)
import LikeButton from './LikeButton'; // client component

export default async function Post({ params }: { params: { id: string } }) {
  const post = await fetchPost(params.id);
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
      <LikeButton postId={params.id} />
    </article>
  );
}
```

```tsx
// app/post/[id]/LikeButton.tsx (client)
'use client';
import { useState } from 'react';

export default function LikeButton({ postId }: { postId: string }) {
  const [liked, setLiked] = useState(false);
  return <button onClick={() => setLiked(l => !l)}>{liked ? 'Unlike' : 'Like'}</button>;
}
```

---

## Middleware, rewrites, redirects, headers

### `middleware.ts` example

```ts
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(req: NextRequest) {
  const url = req.nextUrl.clone();
  // Example: enforce trailing slash
  if (!url.pathname.endsWith('/')) {
    url.pathname = `${url.pathname}/`;
    return NextResponse.redirect(url);
  }
  return NextResponse.next();
}

export const config = { matcher: ['/(.*)'] };
```

### Rewrites and redirects (next.config.js)

```js
// next.config.js
module.exports = {
  async rewrites() {
    return [
      { source: '/api/:path*', destination: 'https://external.api/:path*' }
    ];
  }
};
```

---

## Authentication patterns

Common patterns:

- Use `next-auth` (popular) — integrates with both routers.
- JWT based auth — sign tokens in API routes or auth server, store in httpOnly cookies.
- Session cookies plus server components reading cookies via `cookies()` from `next/headers`.

Example `next-auth` adapter usage (high level):

```ts
// [...nextauth].ts (pages) or app route using NextAuth handler
```

**Server component reading cookie**:

```tsx
import { cookies } from 'next/headers';

export default function Page() {
  const c = cookies().get('session');
  return <div>Cookie: {c?.value}</div>;
}
```

---

## Image optimization, static assets, and fonts

- Put static files in `/public` and reference them as `/logo.png`.
- Use `next/image` for optimized images.

```tsx
import Image from 'next/image';

export default function Logo() {
  return <Image src="/logo.png" alt="logo" width={120} height={40} />;
}
```

Fonts: use `@next/font` (or `next/font` in newer releases) to load fonts.

---

## Internationalization (i18n)

`next.config.js` example:

```js
module.exports = {
  i18n: {
    locales: ['en', 'fr', 'es'],
    defaultLocale: 'en'
  }
}
```

App router also supports localized routing through segment configs.

---

## Testing & tooling

- TypeScript: `tsc --noEmit` to typecheck.
- ESLint: use `next lint` or an ESLint config for Next.js + React.
- Unit tests: `vitest` or `jest` + `@testing-library/react`.
- End-to-end: `playwright` or `cypress`.

Example vitest test:

```ts
import { render } from '@testing-library/react';
import Home from '../app/page';

test('renders', () => {
  const { getByText } = render(<Home />);
  expect(getByText(/Welcome to the App Router Home/i)).toBeTruthy();
});
```

---

## Deployment & Hosting tips

- Vercel is the reference platform (best-integrated features: ISR, Edge functions, image optimization).
- You can deploy to other platforms (Netlify, AWS, Cloud Run) but some features like Image Optimization may differ.
- For edge functions and middleware, be mindful of Node APIs.

**Env vars:** store secrets in environment variables — on Vercel use the dashboard; locally use `.env.local`.

---

## Examples and recipes

### Full example: blog using App Router (simplified)

```
app/
├─ layout.tsx
├─ page.tsx
├─ posts/
   ├─ layout.tsx
   ├─ page.tsx         # lists posts
   └─ [slug]/
      └─ page.tsx      # post page using params
```

`app/posts/page.tsx`:

```tsx
export default async function PostsPage() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts', { next: { revalidate: 60 } });
  const posts = await res.json();
  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.slice(0, 10).map((p: any) => (
          <li key={p.id}>
            <a href={`/posts/${p.id}`}>{p.title}</a>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

`app/posts/[slug]/page.tsx`:

```tsx
type Props = { params: { slug: string } };

export default async function PostPage({ params }: Props) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.slug}`, { next: { revalidate: 60 } });
  if (!res.ok) return <div>Not found</div>;
  const post = await res.json();
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </article>
  );
}
```

### Handling forms & CSRF

- Use SameSite cookies and server-side session tokens.
- For sensitive forms, POST to an app route or API route and validate server-side.

---

## Advanced topics (short overview)

- **Edge Functions:** App route or middleware can be run at the Edge for low-latency.
- **Streaming & Suspense:** Stream HTML to client and show skeletons while awaiting server data.
- **Incremental Adoption:** You can migrate pages gradually from `pages/` to `app/`.
- **Custom servers:** Rarely needed; Vercel/Next built-in server is recommended.

---

## Cheatsheet: Common APIs & helpers

- `next/link` — client navigation.
- `next/image` — optimized images.
- `next/navigation` — `useRouter`, `useParams`, `useSearchParams`, `redirect`, `notFound`.
- `next/headers` — `cookies()`, `headers()` in server components.
- `next/config` / `process.env` — env variables.

---

## References & further reading

- Official docs: https://nextjs.org/docs
- App Router: https://nextjs.org/docs/app
- Pages Router: https://nextjs.org/docs/pages

---

## Appendix — common code snippets

### Link & navigation (client)

```tsx
import Link from 'next/link';

export default function Nav() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/posts">Posts</Link>
    </nav>
  );
}
```

### Head / Metadata (App Router)

```tsx
export const metadata = {
  title: 'My Page',
  description: 'Description here',
};
```

### Using `cookies()` (server)

```tsx
import { cookies } from 'next/headers';

export default function Page() {
  const all = cookies();
  const session = all.get('session');
  return <div>Session: {session?.value}</div>;
}
```

---

## Final notes

This document aims to be a one-stop reference. Next.js evolves frequently — check official docs for the latest features. If you'd like, I can:

- Expand specific sections into full examples (auth, e-commerce, GraphQL, testing suites).
- Generate a starter repo with the patterns above.
- Convert this markdown into a downloadable file (README.md / PDF).

Tell me which parts you want expanded or turned into a runnable starter repository.

