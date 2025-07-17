# Tanstack-Query란?

<br />

## 🏝️ Tanstack-Query

React-Query라는 이름으로 시작된 Tanstack-Query는 v4부터 Vue나 Svelte 등의 다른 프레임워크에서도 활용할 수 있도록 기능이 확장되어 Tanstack-Query라는 이름으로 변경되었습니다. Tanstack-Query는 서버로부터 데이터 가져오기, 데이터 캐싱, 캐시 제어 등 데이터를 쉽고 효율적으로 관리할 수 있는 라이브러리입니다.

대표적인 기능은 다음과 같습니다.

- 데이터 가져오기 및 캐싱
- 동일 요청의 중복 제거
- 신선한 데이터 유지
- 무한 스크롤, 페이지네이션 등의 성능 최적화
- 네트워크 재연결, 요청 실패 등의 자동 갱신

<br />

### 1. 데이터 캐싱

Tanstack-Query를 활용해 데이터를 가져올 때는 **쿼리 키(`queryKey`)** 를 지정하게 됩니다. 쿼리 키는 캐시된 데이터와 비교해 **새로운 데이터를 가져올지, 캐시된 데이터를 사용할지 결정하는 기준**이 됩니다.

```tsx
import { useQuery } from '@tanstack/react-query'

export default function Example() {
  const { data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/TanStack/query').then((res) =>
        res.json(),
      ),
  })

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```

|**쿼리 키와 일치하는 데이터가 없을 때**|**쿼리 키와 일치하는 데이터가 있을 때**|
|:---:|:---:|
|<img src="https://github.com/user-attachments/assets/f2371fd4-fe77-4f15-b76d-8301a0ea2ea1" alt="image" />|<img src="https://github.com/user-attachments/assets/2c2203e8-2c4f-4503-a9a8-7fb41b984a27" alt="image" />|

- 쿼리 키와 일치하는 데이터가 없을 때

  쿼리 키와 일치하는 데이터가 없으면 **서버에서 새로운 데이터를 가져옵니다**. 서버에서 데이터를 가져오면 그 데이터는 캐시되고, 이후 요청부터는 캐시된 데이터를 사용할 수 있습니다.

- 쿼리 키와 일치하는 데이터가 있을 때

  쿼리 키와 일치하는 데이터가 있으면 **서버에 요청하지 않고 캐시된 데이터를 사용합니다**. 따라서 같은 데이터를 가져오는 요청이 여러 번 발생해도, 캐시된 데이터를 사용하게 되어 중복 요청을 줄일 수 있습니다.

<br />

### 2. 데이터의 신선도

Tanstack-Query는 캐시한 데이터를 신선(`Fresh`)하거나 상한(`Stale`) 상태로 구분해 관리합니다. 캐시된 데이터가 신선하다면 캐시된 데이터를 사용하고, 상했다면 서버에 다시 요청해 신선한 데이터를 가져옵니다.

데이터가 상하는 데까지 걸리는 시간을 `staleTime` 옵션으로 지정할 수 있습니다. 그리고 신선한지 상했는지 여부를 `isStale` 변수로 확인할 수 있습니다.

```tsx
export default function Example() {
  const { data, isStale } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/TanStack/query').then((res) =>
        res.json(),
      ),
    staleTime: 1000 * 10 // millisecond ➡️ 10초 동안 신선함
  })

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
      <p>데이터가 {isStale ? '상했어요..' : '신선해요!'}</p>
    </div>
  )
}
```

<br />

### 3. 사용법

Tanstack-Query와 Tanstack-Query ESLint 플러그인을 설치합니다.

```bash
npm i @tanstack/react-query
npm i -D @tanstack/eslint-plugin-query
```

ESLint 플러그인이 제공하는 권장 규칙을 사용해 실수를 방지할 수 있습니다. 추가적으로, ESLint 설정 파일 `.eslintrc.cjs` 혹은 `eslint.config.js`의 `extends` 배열에 TanStack Query 권장 규칙 플러그인을 추가합니다.

```js
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    ...
    'plugin:@tanstack/eslint-plugin-query/recommended'
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true }
    ],
  }
}
```

TanStack-Query 권장 규칙은 다음과 같습니다.

- `'@tanstack/query/exhaustive-deps': 'error'`: 쿼리 함수(`queryFn`)에서 사용하는 외부 변수를 쿼리 키에 추가해야 합니다. (그렇지 않으면 에러 발생)
- `'@tanstack/query/stable-query-client': 'error'`: 애플리케이션에서 하나의 `QueryClient`를 생성해 사용해야 합니다. (그렇지 않으면 에러 발생)
- `'@tanstack/query/no-rest-destructuring': 'warn'`: 쿼리를 반환할 때 Rest Parameter(`...rest`)를 사용할 수 없습니다. (그렇지 않으면 경고)

<br />

이제 프로젝트의 `App.tsx`에서 `App` 컴포넌트를 `<QueryClientProvider>`로 랩핑하고, `queryClient` 인스턴스를 연결합니다.

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import Example from '@components/Example'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}
```

<br />

### 4. 핵심 기능

- **`useQuery`**

  가장 기본적인 쿼리 훅으로, **컴포넌트에서 데이터를 가져올 때 사용**합니다.

  ```tsx
  import { useQuery } from '@tanstack/react-query';

  type ResponseValue = {
    message: string
    time: string
  }

  export default function Example() {
    const { data } = useQuery<ResponseValue>({
      queryKey: ['repoData'],
      queryFn: () =>
        fetch('https://api.github.com/repos/TanStack/query').then((res) =>
          res.json(),
        ),
      staleTime: 1000 * 10 
    })
  
    return (
      <div>
        <h1>{data?.message}</h1>
        <p>{data?.time}</p>
      </div>
    )
  }
  ```

  `useQuery`에 전달하는 `queryOptions`로 다음과 같은 옵션을 지정할 수 있습니다.

  |**옵션**|**설명**|**기본값**|**타입**|
  |---|---|---|---|
  |`queryKey`|**`필수`** 고유한 쿼리 키(식별자)||`unknown[]`|
  |`queryFn`|**필수** 데이터를 가져오는 쿼리함수로, 반드시 데이터를 반환하거나 오류를 던져야 함||`(context: QueryFunctionContext) => Promise<TData>`|
  |`staleTime`|데이터가 상하는데 걸리는 시간(ms)|`0`|`number | ((query: Query) => number)`|
  |`gcTime`|비활성 캐시 데이터(inactive)가 메모리에 남아있는 시간(ms)|`5 * 60 * 1000`|`number | Infinity`|
  |`throwOnError`|쿼리 실패 시 오류를 던질지 여부|`undefined`|`undefined | boolean | (error: TError, query: Query) => boolean`|
  |`retry`|쿼리 실패 시 재시도 횟수|`3`|`boolean | number | (failureCount: number, error: TError) => boolean`|
  |`enabled`|쿼리 자동 실행 여부. `false`인 경우, 대기 상태(`pending`)으로 시작|`true`|`boolean | (query: Query) => boolean`|
  |`refetchInterval`|데이터 자동 갱신 시간 간격(ms)||`number | false | ((query: Query) => number | false | undefined)`|
  |`refetchOnMount`|`useQuery` 연결 시 데이터 갱신 여부|`true`|`boolean | "always" | ((query: Query) => boolean | "always")`|
  |`refetchOnWindowFocus`|브라우저 화면 포커스 시 데이터 갱신 여부|`true`|`boolean | "always" | ((query: Query) => boolean | "always")`|
  |`placeholderData`|대기(`pending`) 상태에서 사용할 데이터||`TData | (previousValue: TData | undefined, previousQuery: Query | undefined) => TData`|
  |`select`|가져온 데이터를 변형(선택)하는 함수||`(data: TData) => unknown`|
  |`structuralSharing`|데이터 구조의 재사용을 최적화해 불변성을 유지하고 불필요한 리렌더링 방지|`true`|`boolean | (oldData: unknown | undefined, newData: unknown) => unknown`|
  
  - `queryKey`

    쿼리 키(`queryKey`)는 쿼리를 식별하는 고유한 값으로, **배열 형태로 지정**합니다. 다중 아이템 쿼리 키를 사용할 때는 아이템의 순서가 중요합니다.

    ```tsx
    // 단일 아이템 쿼리 키
    useQuery({ queryKey: ['hello'] })
    
    // 다중 아이템 쿼리 키
    useQuery({ queryKey: ['hello', 'world', 123, { a: 1, b: 2 }] })
    
    // 서로 같은 쿼리
    useQuery({ queryKey: ['hello', 'world', 123, { a: 1, b: 2 }] })
    useQuery({ queryKey: ['hello', 'world', 123, { b: 2, c: undefined, a: 1 }] })
    
    // 서로 다른 쿼리
    useQuery({ queryKey: ['hello', 'world', 123, { a: 1, b: 2 }] })
    useQuery({ queryKey: ['hello', 'world', 123, { a: 1, b: 2, c: 3 }] })
    useQuery({ queryKey: ['hello', 'world'] })
    useQuery({ queryKey: [123, 'world', { a: 1, b: 2, c: 3 }, 'hello'] })
    ```

    기본적으로 **쿼리 함수(`queryFn`)에서 사용하는 변수는 쿼리 키에 포함되어야 합니다**. 그러면 변수가 변경될 때마다 자동으로 다시 가져올 수 있습니다.

  - `queryFn`

    쿼리 함수(`queryFn`)는 데이터를 가져오는 비동기 함수로, **반드시 데이터를 반환하거나 오류를 던져야 합니다**. 던져진 오류는 반환되는 `error` 객체로 확인할 수 있습니다.

    ```tsx
    import { useQuery } from '@tanstack/react-query';

    type ResponseValue = {
      message: string
      time: string
    }
  
    export default function Example() {
      const { data, error } = useQuery<ResponseValue>({
        queryKey: ['repoData'],
        queryFn: () =>
          fetch('https://api.github.com/repos/TanStack/query')
            .then((res) =>
              res.json(),
            )
            .then((data) => {
              if (!data.time) {
                throw new Error('문제가 발생했습니다!');
              }

              return data;
            })
            .catch((error) => {
              throw new Error('API 호출 중 문제가 발생했습니다.');
            },
        staleTime: 1000 * 10
      })
    
      return (
        <div>
          {data &&
            <>
              <h1>{data?.message}</h1>
              <p>{data?.time}</p>
            </>
          }
          {error && <h1>{error.message}</h1>}
        </div>
      )
    }
    ```

  - `select`

<br />

- **`useMutation`**

  

<br />

## :book: 참고

- [공식문서 - Tanstack Query](https://tanstack.com/query/latest/docs/framework/react/overview)
- [TanStack Query(React Query) 핵심 정리](https://www.heropy.dev/p/HZaKIE)
