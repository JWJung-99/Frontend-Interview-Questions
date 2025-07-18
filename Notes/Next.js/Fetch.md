# Next의 fetch란?

<br />

## 📟 `fetch`

Next.js에서 fetch 함수는 데이터를 클라이언트나 서버에서 가져오기 위해 사용됩니다. Next.js는 React 기반의 프레임워크로, 서버사이드 렌더링(SSR)과 정적 사이트 생성(SSG)을 지원합니다. 이 특성에 따라 `fetch`는 클라이언트와 서버 양쪽에서 사용할 수 있지만, 각 환경에서 동작 방식이 다릅니다.

### 서버 `fetch`

Next.js의 서버 환경에서는 Node.js 네이티브 환경에서 `fetch`가 동작합니다. 따라서 `fetch`가 서버에서 실행되며, 클라이언트에 노출되지 않습니다.

```tsx
export default async function Page() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

Next.js의 `fetch`는 다음과 같은 특징을 가집니다:
- 자동 캐싱: 기본적으로 `fetch`는 같은 요청에 대해 캐싱을 수행합니다.
- 캐시 무효화 가능: 설정을 통해 캐싱을 끌 수 있습니다 (`cache: 'no-store'`).
- ISR 지원: 일정 시간마다 데이터를 갱신하도록 설정할 수 있습니다 (`next.revalidate`).

<br />

### 클라이언트 `fetch`

클라이언트에서는 브라우저의 기본 `fetch` API를 활용해 데이터를 가져옵니다. 보통 React 컴포넌트의 `useEffect`나 이벤트 핸들러 내부에서 사용됩니다.

```tsx
import { useEffect, useState } from 'react'; 

export default function ClientFetch() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetch('/...') // api url
      .then((res) => res.json())
      .then((data) => setData(data));
  }, []);
  
  return (
    <div>{data ? JSON.stringify(data) : 'Loading...'}</div>
  )
}
```

<br />

## 🏝️ TanStack-Query

Next.js의 서버 컴포넌트는 React Server Component를 기반으로 하고 있습니다. 서버 컴포넌트의 대표적인 장점은 다음과 같습니다.

<div align="center">
  <img width="50%" alt="image" src="https://github.com/user-attachments/assets/cae75594-bae4-406e-bd42-28827676afaa" />
</div>

- 데이터를 가져오는 로직을 **서버에서 처리할 수 있다**.
- DB와 더 가까이에서 데이터를 처리할 수 있다.
- 번들 사이즈가 클라이언트에 영향을 주지 않는다.

[위에서]() 살펴본 것처럼, Next.js에서는 이 서버 컴포넌트의 장점을 이용해 아래와 같은 형식으로 데이터를 가져올 수 있고, 확장 기능을 이용해 캐싱과 `revalidate`도 이용할 수 있습니다.

```tsx
export default async function Page() {
  // revalidate this data every 10 seconds at most
  const res = await fetch('https://...', { next: { revalidate: 10 } });
  const data = res.json();
  // ...
}
```

그렇다면 TanStack-Query의 장점인 캐싱, `revalidate` 등의 기능을 Next.js의 `fetch` 함수가 제공하는데 왜 TanStack-Query가 꼭 필요할까요?

두 라이브러리 / 프레임워크에서 **캐싱을 하는 목적과 방법이 상이**하기 때문입니다.

||`@tanstack/react-query`|`fetch`|
|:---:|---|---|
|**목적**|클라이언트 측에서 발생하는 다중 fetching 문제 효율화|서로 자원 공유가 어려운 서버 컴포넌트의 특성 상 발생하는 다중 fetching 문제 효율화|
|**방법**|쿼리 키를 매개로 각 fetch의 상태를 관리|엔드포인트와 옵션을 기준으로 fetch의 상태를 관리|

Next.js의 서버 컴포넌트는 "동일한 엔드포인트와 동일한 옵션을 가진 요청이라면 같은 요청이며 같은 결과가 올 것이다"라는 근거에 의존해 이를 효율적으로 관리하기 위해 캐싱하는 반면, TanStack-Query의 경우 쿼리 키를 기준으로 해당 쿼리 키에 대한 요청 사항을 처리할 뿐입니다.

또한 `fetch`는 캐싱 스토어에 접근하기 위해 매번 `fetch`를 수행해야 하지만, TanStack-Query의 경우 쿼리 키의 값만 알고 있다면 해당 쿼리 키의 값만 빼오는 행위가 가능하다는 특징이 있습니다.

서버 컴포넌트에서 TanStack-Query를 사용하는 이유는, 서버 측에서 fetching이 일어날 때 TanStack-Query가 개입하지 못한다면 데이터 fetching을 서버와 클라이언트에서 두 번 해야 하는 비효율이 발생하기 때문입니다. 이를 해결하기 위해 `initialData`, `Hydration` 등의 방식을 이용해 서버 컴포넌트에서 가져온 데이터를 클라이언트에서 사용할 수 있게 하여 비효율을 막을 수 있게 됩니다.

---

### 설치 및 설정

Next.js의 app router에서 TanStack-Query를 설치합니다.

```bash
npm i @tanstack/react-query
```

TanStack-Query를 Next.js의 app router에서 사용하기 위해서 **`providers`**를 추가해야 합니다.

```
root & src/
  ├── app/
  │   ├── providers.tsx
  │   ├── layout.tsx
  ...
```

<br />

### `providers` 생성

다음과 같이 Provider를 구성합니다. 서버와 클라이언트 모두에서 사용하므로 `'use client'` 키워드를 사용해야 합니다.

```tsx
'use client'

import {
  isServer,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'

function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        // With SSR, we usually want to set some default staleTime
        staleTime: 60 * 1000,
      },
    },
  })
}

let browserQueryClient: QueryClient | undefined = undefined

function getQueryClient() {
  if (isServer) {
    // Server: always make a new query client
    return makeQueryClient()
  } else {
    // Browser: make a new query client if we don't already have one
    // This is very important, so we don't re-make a new client if React
    // suspends during the initial render. This may not be needed if we
    // have a suspense boundary BELOW the creation of the query client
    if (!browserQueryClient) browserQueryClient = makeQueryClient()
    return browserQueryClient
  }
}

export default function Providers({ children }: { children: React.ReactNode }) {
  // NOTE: Avoid useState when initializing the query client if you don't
  //       have a suspense boundary between this and the code that may
  //       suspend because React will throw away the client on the initial
  //       render if it suspends and there is no boundary
  const queryClient = getQueryClient()

  return (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  )
}
```

`getQueryClient`를 유심히 볼 필요가 있을 것 같습니다. TanStack-Query 공식문서에서는 `getQueryClient`를 **서버 환경과 브라우저 환경에서 서로 다르게 생성**합니다. 같은 서버에서 모든 사용자가 하나의 `queryClient`를 사용하게 되면 매우 큰 문제가 발생하기 때문에 서버 측면에서는 **매번 새로 생성**하고, 클라이언트 측에서 각 사용자들은 매번 `queryClient`를 생성할 필요가 없기 때문에 **존재하지 않는 경우에만 생성**합니다.

<br />

### `layout`에 `providers` 추가

`layout.tsx` 파일에서 생성한 `Providers`를 추가해 어디서든지 TanStack-Query를 사용할 수 있도록 합니다.

```tsx
import Providers from './providers'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <head />
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

### `prefetch`와 `de/hydrating`

이제 app router의 `posts` 페이지에서 해당 queryClient를 적용해 보겠습니다. 폴더 구조는 다음과 같습니다.

```
root & src/
  ├── app/
  │   ├── posts
  │   │   ├── page.tsx
  │   │   ├── posts.tsx
  │   ├── providers.tsx
  │   ├── layout.tsx
  ...
```

서버 컴포넌트인 `app/posts/page.tsx`에서 미리 Query 요청을 보낸 후(`prefetch`), `dehydrate` 처리를 하여 클라이언트 컴포넌트에 넘겨줄 수 있습니다. 그러면 클라이언트 컴포넌트에서는 미리 받아온 데이터를 즉시 꺼내와서 사용할 수 있게 되는 것입니다.

```tsx
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from '@tanstack/react-query'
import Posts from './posts'

export default async function PostsPage() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  return (
    // Neat! Serialization is now as easy as passing props.
    // HydrationBoundary is a Client Component, so hydration will happen there.
    <HydrationBoundary state={dehydrate(queryClient)}>
      <Posts />
    </HydrationBoundary>
  )
}
```

클라이언트 컴포넌트인 `Posts`에서는 다음과 같이 미리 `fetch` 된 Query를 불러와 사용하게 됩니다.

```tsx
'use client'

export default function Posts() {
  // This useQuery could just as well happen in some deeper
  // child to <Posts>, data will be available immediately either way
  const { data } = useQuery({
    queryKey: ['posts'],
    queryFn: () => getPosts(),
  })

  // This query was not prefetched on the server and will not start
  // fetching until on the client, both patterns are fine to mix.
  const { data: commentsData } = useQuery({
    queryKey: ['posts-comments'],
    queryFn: getComments,
  })

  // ...
}
```

Query를 `prefetch`하고 `dehydrate`하는 서버 컴포넌트는 다음과 같이 중첩해서 사용할 수도 있습니다. 따라서 해당 Query를 애플리케이션의 최상단에서 받아 사용하는 것이 아니라 가장 가까운 `HydrationBoundary`에서 사용할 수 있게 되는 것입니다.

```tsx
// app/posts/page.tsx
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from '@tanstack/react-query'
import Posts from './posts'
import CommentsServerComponent from './comments-server'

export default async function PostsPage() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <Posts />
      <CommentsServerComponent />
    </HydrationBoundary>
  )
}

// app/posts/comments-server.tsx
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from '@tanstack/react-query'
import Comments from './comments'

export default async function CommentsServerComponent() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery({
    queryKey: ['posts-comments'],
    queryFn: getComments,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <Comments />
    </HydrationBoundary>
  )
}
```

<br />

### 주의할 점

서버 컴포넌트에서 화면을 그리는 경우 주의할 점이 있습니다. 

> [!NOTE]
> 
> 아래 예시 코드에서는 반환된 `posts` 데이터를 사용해야 하므로 **데이터를 반환하지 않는 `prefetchQuery` 대신 `fetchQuery`를 사용**합니다.

<br />

```tsx
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from '@tanstack/react-query'
import Posts from './posts'

export default async function PostsPage() {
  const queryClient = new QueryClient()

  // Note we are now using fetchQuery()
  const posts = await queryClient.fetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      {/* This is the new part */}
      <div>Nr of posts: {posts.length}</div>
      <Posts />
    </HydrationBoundary>
  )
}
```

위 코드에서 `staleTime` 1분이 지나서 클라이언트에서 재검증이 일어나는 경우 어떻게 할까요?

서버 컴포넌트에서는 발생한 Query를 재검증할 방법이 없습니다. 따라서 클라이언트에서 데이터를 새로 가져와 렌더링하게 되면 서버 컴포넌트에서 그리는 `post.length`와 클라이언트에서 컴포넌트에서 그리는 `post.length`가 일치하지 않는 상황이 발생하는 것입니다.

따라서 에러를 `catch` 해야 하는 경우가 아니라면, `queryClient.fetchQuery` 사용을 피하고 실행 결과를 서버 컴포넌트나 심지어 다른 클라이언트 컴포넌트에서 렌더링 하는 것을 피해야 합니다.

<br />

### 서버 컴포넌트 스트리밍

Next.js app router는 애플리케이션의 준비된 부분을 가능한 한 빨리 브라우저에 스트리밍하여, 아직 완료되지 않은 콘텐츠를 기다리지 않고 즉시 완료된 콘텐츠를 표시할 수 있도록 자동으로 처리합니다. 이는 `loading.tsx`를 생성하면 자동으로 생성되는 `<Suspense>` 바운더리를 따릅니다. 각 Suspense 바운더리에서 데이터를 성공적으로 얻게 되면 Next.js는 완료된 항목을 브라우저에 렌더링하고 스트리밍합니다. 즉, `await`를 사용하여 `prefetch`를 기다릴 때 컴포넌트가 “대기 상태”에 들어가고, 데이터가 준비되면 해당 콘텐츠가 렌더링됩니다.

하지만 이제 React-Query 5.40.0 버전부터는 `pending` 상태의 Query들도 `dehydrate` 처리하여 클라이언트로 보낼 수 있기 때문에 `prefetch`를 `await` 할 필요가 없습니다.

```tsx
// app/get-query-client.ts
import {
  isServer,
  QueryClient,
  defaultShouldDehydrateQuery,
} from '@tanstack/react-query'

function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000,
      },
      dehydrate: {
        // include pending queries in dehydration
        shouldDehydrateQuery: (query) =>
          defaultShouldDehydrateQuery(query) ||
          query.state.status === 'pending',
        shouldRedactErrors: (error) => {
          // We should not catch Next.js server errors
          // as that's how Next.js detects dynamic pages
          // so we cannot redact them.
          // Next.js also automatically redacts errors for us
          // with better digests.
          return false
        },
      },
    },
  })
}

let browserQueryClient: QueryClient | undefined = undefined

export function getQueryClient() {
  if (isServer) {
    // Server: always make a new query client
    return makeQueryClient()
  } else {
    // Browser: make a new query client if we don't already have one
    // This is very important, so we don't re-make a new client if React
    // suspends during the initial render. This may not be needed if we
    // have a suspense boundary BELOW the creation of the query client
    if (!browserQueryClient) browserQueryClient = makeQueryClient()
    return browserQueryClient
  }
}
```

React에서 Promise를 직렬화 할 수 있기 때문에 `pending` 상태인 Query까지 함께 `dehydrate` 처리할 수 있는 것입니다! 이제 남은 작업은 `HydrationBoundary`를 적용하는 것입니다. 이 과정에서 더이상 `await`를 사용해 `prefetch` 할 필요가 없어집니다.

```tsx
import { dehydrate, HydrationBoundary } from '@tanstack/react-query'
import { getQueryClient } from './get-query-client'
import Posts from './posts'

// the function doesn't need to be `async` because we don't `await` anything
export default function PostsPage() {
  const queryClient = getQueryClient()

  // look ma, no await
  queryClient.prefetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <Posts />
    </HydrationBoundary>
  )
}
```

클라이언트 컴포넌트에서는 `useSuspenseQuery`를 이용해 `prefetch` 한 데이터를 호출할 수 있습니다.

```tsx
'use client'

export default function Posts() {
  const { data } = useSuspenseQuery({ queryKey: ['posts'], queryFn: getPosts })

  // ...
}
```

<br />

## 📖 참고

- [TanStack-Query 공식문서](https://tanstack.com/query/v5/docs/framework/react/guides/advanced-ssr#experimental-streaming-without-prefetching-in-nextjs)
- [RSC란?](https://saengmotmi.netlify.app/react/what-is-rsc/)
- [Next.Js에서 fetch말고 React-Query 사용하기](https://velog.io/@haryan248/next-with-react-query)
- [next.js에서 react query가 필요할까?](https://xionwcfm.tistory.com/339)
