# TanStack-Query란?

<br />

## 🏝️ TanStack-Query

React-Query라는 이름으로 시작된 TanStack-Query는 v4부터 Vue나 Svelte 등의 다른 프레임워크에서도 활용할 수 있도록 기능이 확장되어 TanStack-Query라는 이름으로 변경되었습니다. TanStack-Query는 서버로부터 데이터 가져오기, 데이터 캐싱, 캐시 제어 등 데이터를 쉽고 효율적으로 관리할 수 있는 라이브러리입니다.

대표적인 기능은 다음과 같습니다.

- 데이터 가져오기 및 캐싱
- 동일 요청의 중복 제거
- 신선한 데이터 유지
- 무한 스크롤, 페이지네이션 등의 성능 최적화
- 네트워크 재연결, 요청 실패 등의 자동 갱신

<br />

### 1. 데이터 캐싱

TanStack-Query를 활용해 데이터를 가져올 때는 **쿼리 키(`queryKey`)** 를 지정하게 됩니다. 쿼리 키는 캐시된 데이터와 비교해 **새로운 데이터를 가져올지, 캐시된 데이터를 사용할지 결정하는 기준**이 됩니다.

```tsx
import { useQuery } from '@TanStack/react-query'

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

TanStack-Query는 캐시한 데이터를 신선(`Fresh`)하거나 상한(`Stale`) 상태로 구분해 관리합니다. 캐시된 데이터가 신선하다면 캐시된 데이터를 사용하고, 상했다면 서버에 다시 요청해 신선한 데이터를 가져옵니다.

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

TanStack-Query와 TanStack-Query ESLint 플러그인을 설치합니다.

```bash
npm i @TanStack/react-query
npm i -D @TanStack/eslint-plugin-query
```

ESLint 플러그인이 제공하는 권장 규칙을 사용해 실수를 방지할 수 있습니다. 추가적으로, ESLint 설정 파일 `.eslintrc.cjs` 혹은 `eslint.config.js`의 `extends` 배열에 TanStack Query 권장 규칙 플러그인을 추가합니다.

```js
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    ...
    'plugin:@TanStack/eslint-plugin-query/recommended'
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

- `'@TanStack/query/exhaustive-deps': 'error'`: 쿼리 함수(`queryFn`)에서 사용하는 외부 변수를 쿼리 키에 추가해야 합니다. (그렇지 않으면 에러 발생)
- `'@TanStack/query/stable-query-client': 'error'`: 애플리케이션에서 하나의 `QueryClient`를 생성해 사용해야 합니다. (그렇지 않으면 에러 발생)
- `'@TanStack/query/no-rest-destructuring': 'warn'`: 쿼리를 반환할 때 Rest Parameter(`...rest`)를 사용할 수 없습니다. (그렇지 않으면 경고)

<br />

이제 프로젝트의 `App.tsx`에서 `App` 컴포넌트를 `<QueryClientProvider>`로 랩핑하고, `queryClient` 인스턴스를 연결합니다.

```tsx
import { QueryClient, QueryClientProvider } from '@TanStack/react-query'
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
  import { useQuery } from '@TanStack/react-query';

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

  **[옵션]**

  `useQuery`에 전달하는 `queryOptions`로 대표적으로 다음과 같은 옵션을 지정할 수 있습니다.

  |**옵션**|**설명**|**기본값**|**타입**|
  |---|---|---|---|
  |`queryKey`|**`필수`** 고유한 쿼리 키(식별자)||`unknown[]`|
  |`queryFn`|**필수** 데이터를 가져오는 쿼리함수로, 반드시 데이터를 반환하거나 오류를 던져야 함||`(context: QueryFunctionContext) => Promise<TData>`|
  |`staleTime`|데이터가 상하는데 걸리는 시간(ms)|`0`|`number \| ((query: Query) => number)`|
  |`gcTime`|비활성 캐시 데이터(inactive)가 메모리에 남아있는 시간(ms)|`5 * 60 * 1000`|`number \| Infinity`|
  |`throwOnError`|쿼리 실패 시 오류를 던질지 여부|`undefined`|`undefined \| boolean \| (error: TError, query: Query) => boolean`|
  |`retry`|쿼리 실패 시 재시도 횟수|`3`|`boolean \| number \| (failureCount: number, error: TError) => boolean`|
  |`enabled`|쿼리 자동 실행 여부. `false`인 경우, 대기 상태(`pending`)으로 시작|`true`|`boolean \| (query: Query) => boolean`|
  |`refetchInterval`|데이터 자동 갱신 시간 간격(ms)||`number \| false \| ((query: Query) => number \| false \| undefined)`|
  |`refetchOnMount`|`useQuery` 연결 시 데이터 갱신 여부|`true`|`boolean \| "always" \| ((query: Query) => boolean \| "always")`|
  |`refetchOnWindowFocus`|브라우저 화면 포커스 시 데이터 갱신 여부|`true`|`boolean \| "always" \| ((query: Query) => boolean \| "always")`|
  |`placeholderData`|대기(`pending`) 상태에서 사용할 데이터||`TData \| (previousValue: TData \| undefined, previousQuery: Query \| undefined) => TData`|
  |`select`|가져온 데이터를 변형(선택)하는 함수||`(data: TData) => unknown`|
  |`structuralSharing`|데이터 구조의 재사용을 최적화해 불변성을 유지하고 불필요한 리렌더링 방지|`true`|`boolean \| (oldData: unknown \| undefined, newData: unknown) => unknown`|
  
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
    import { useQuery } from '@TanStack/react-query';

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

    선택 함수를 사용하면 **가져온 데이터를 변형**할 수 있습니다. 쿼리 함수가 반환하는 데이터를 인수로 받아 선택 함수에서 처리하고 반환하면 최종 데이터가 됩니다. 데이터를 가공하는 경우 최종 데이터 타입을 `useQuery`의 세 번째 제네릭 타입으로 선언할 수 있습니다. 만약 가공하지 않는다면 세 번째 제네릭 타입은 자동으로 `queryFn`의 반환값으로 추론됩니다.

    ```tsx
    import { useQuery } from '@TanStack/react-query'
    
    type Users = User[]
    interface User {
      id: string
      name: string
      age: number
    }
    
    export default function UserNames() {
      const { data } = useQuery<Users, Error, string[]>({
        queryKey: ['users'],
        queryFn: async () => {
          const res = await fetch('https://api.heropy.dev/v0/users')
          const { users } = await res.json()
          return users
        },
        staleTime: 1000 * 10,
        select: data => data.map(user => user.name)
      })
      return (
        <>
          <h2>User Names</h2>
          <ul>{data?.map((name, i) => <li key={i}>{name}</li>)}</ul>
        </>
      )
    }
    ```

  - `placeholderData`

    새로운 데이터를 가져오는 과정에서 쿼리가 무효화되면서 일시적으로 데이터가 없는 상태(`undefined`)가 되면 데이터 출력 화면이 깜빡일 수 있습니다. 이러한 현상을 방지하기 위해 `placeholderData` 옵션을 사용하면 쿼리 함수가 호출되는 **대기 상태(`pending`)에서 임시로 표시할 데이터를 미리 지정**할 수 있습니다. `placeholderData` 옵션에는 함수를 지정할 수 있으며, 이 함수는 새로운 데이터를 가져오기 직전의 이전 데이터를 받을 수 있어 이를 반환해 임시 데이터로 이용할 수 있습니다.

    ```tsx
    const { data: movies } = useQuery<Movie[]>({
      queryKey: ['movies', searchText], // 검색어
      queryFn: async () => {
        const res = await fetch(`https://omdbapi.com?apikey=7035c60c&s=${searchText}`)
        const { Search: movies } = await res.json()
        return movies
      },
      placeholderData: prev => prev
    })
    ```

  - `structuralSharing`

    `structuralSharing` 옵션으로 새로운 데이터를 가져올 때 **이전 데이터와 비교해 변경되지 않은 부분은 이전 데이터를 재사용하도록 지정**할 수 있습니다. 이를 통해 메모리 사용량을 최적화하고 불필요한 리렌더링을 방지할 수 있습니다.

    `structuralSharing` 옵션이 `true`면 변경된 부분만 새롭게 업데이트하고 변경되지 않은 부분은 이전 데이터의 참조를 재사용합니다. `false`이면 모든 객체가 새로운 참조로 생성됩니다. 하지만 매우 큰 중첩 객체를 다루는 경우, **구조적인 비교 자체가 성능에 부담**이 될 수 있습니다. 따라서 이 경우에는 `structuralSharing` 옵션을 `false`로 지정하는 것이 좋습니다. 또한 데이터가 항상 새로운 참조여야 하거나 데이터가 단순해 깊은 비교가 필요하지 않은 경우에도 `false`로 지정하는 것이 좋습니다.

  <br />

  **[반환]**

  `useQuery`의 대표적인 반환값은 다음과 같습니다.

  |**반환 속성**|**설명**|**타입**|
  |---|---|---|
  |`data`|성공적으로 가져온 데이터|`TData`|
  |`error`|오류가 발생했을 때 오류 객체<br />오류가 발생하지 않았다면 `null` | `TError \| null`|
  |`fetchStatus`|`'fetching'`: 쿼리 함수가 실행 중<br />`'paused'`: 쿼리 함수의 가져오기가 일시 중단됨(`isPaused`)<br />`'idle'`: 쿼리 함수가 동작 중이지 않음|`'fetching' \| 'paused' \| 'idle'`|
  |`isError`|쿼리 함수에서의 오류 발생 여부|`boolean`|
  |`isFetching`|쿼리 함수가 실행 중|`boolean`|
  |`isLoading`|쿼리 함수의 첫 번째 가져오기가 진행 중<br />`isFetching && isPending`|`boolean`|
  |`isPending`|캐시된 데이터가 없고 쿼리가 아직 완료되지 않은 상태|`boolean`|
  |`isRefetching`|백그라운드에서 다시 가져오기가 진행 중인지 여부<br />`isFetching && !isPending`|`boolean`|
  |`isStale`|캐시된 데이터가 무효화(Invalidated)되거나 `staleTime`이 경과된 여부|`boolean`|
  |`refetch`|데이터를 새롭게 다시 가져오는 함수<br />`throwOnError: true` 옵션을 사용해야 오류 발생|`(options: { throwOnError: boolean, cancelRefetch: boolean }) => Promise<UseQueryResult>`|
  |`status`|`'pending'`: 캐시된 데이터가 없고 아직 완료되지 않은 상태<br />`'error'`: 오류가 발생한 상태<br />`'success'`: 데이터를 성공적으로 가져온 상태|`'pending' \| 'error' \| 'success'`|

  - 상태 확인

    ```tsx
    import { useQuery } from '@TanStack/react-query';
  
    type ResponseValue = {
      message: string
      time: string
    }
  
    export default function Example() {
      const { data, isFetching, isPending, isLoading, isStale } = useQuery<ResponseValue>({
        queryKey: ['repoData'],
        queryFn: () =>
          fetch('https://api.github.com/repos/TanStack/query').then((res) =>
            res.json(),
          ),
        staleTime: 1000 * 10 
      })
    
      return (
        <>
          {isLoading ? (
            <div>로딩 중..</div>
          ) : (
            <>
              <div>{data?.time}</div>
              <div>데이터가 상했나요?: {JSON.stringify(isStale)}</div>
              <div>isFetching: {JSON.stringify(isFetching)}</div>
              <div>isPending: {JSON.stringify(isPending)}</div>
              <div>isLoading: {JSON.stringify(isLoading)}</div>
            </>
          )}
        </>
      )
    }
    ```

  - 다시 가져오기

    ```tsx
    import { useQuery } from '@TanStack/react-query';
  
    type ResponseValue = {
      message: string
      time: string
    }
  
    export default function Example() {
      const { data, isStale, refetch } = useQuery<ResponseValue>({
        queryKey: ['repoData'],
        queryFn: () =>
          fetch('https://api.github.com/repos/TanStack/query').then((res) =>
            res.json(),
          ),
        staleTime: 1000 * 10 
      })
    
      return (
        <>
          {isLoading ? (
            <div>로딩 중..</div>
          ) : (
            <>
              <div>{data?.time}</div>
              <div>데이터가 상했나요?: {JSON.stringify(isStale)}</div>
              <button onClick={() => refetch()}>데이터 가져오기!</button>
            </>
          )}
        </>
      )
    }
    ```

<br />

- **`useMutation`**

  `useQuery`가 서버로부터 데이터를 가져올 때 사용한다면, `useMutation`은 **데이터 변경 작업(생성, 수정, 삭제 등)을 위한 훅**입니다. 이를 통해 데이터의 변경 작업을 처리하고 다양한 성공, 실패, 로딩 등의 상태를 얻을 수 있습니다.

  ```tsx
  const { data } = useMutaion<TodoResponse>({
    mutationFn : postTodo, 
    onSuccess: res => {}
  });
  ```

  **[옵션]**

  `useMutation`에 다음과 같은 옵션을 지정할 수 있습니다.

  |**옵션**|**설명**|**기본값**|**타입**|
  |---|---|---|---|
  |`mutationFn`|**`필수`** 실행할 비동기 변이 함수||`(variables: TVariables) => Promise<TData>`|
  |`gcTime`|비활성 캐시 데이터(inactive)가 메모리에 남아있는 시간(ms)||`number \| Infinity`|
  |`onSuccess`|변이가 성공할 때 호출되는 함수||`(data: TData, variables: TVariables, context: TContext) => Promise<unknown> \| unknown`|
  |`onError`|변이 중 오류가 발생할 때 호출되는 함수|`undefined`|`(err: TError, variables: TVariables, context?: TContext) => Promise<unknown> \| unknown`|
  |`onMutate`|변이 함수가 실행되기 전에 호출되는 함수||`(variables: TVariables) => Promise<TContext \| void> \| TContext \| void`|
  |`onSettled`|변이가 성공하거나 실패해도 항상 호출되는 함수|`(data: TData, error: TError, variables: TVariables, context?: TContext) => Promise<unknown> \| unknown`|
  |`retry`|변이 실패 시 재시도 횟수|`0`|`boolean \| number \| (failureCount: number, error: TError) => boolean`|
  |`throwOnError`|변이 실패 시 오류를 던질 지 여부|`undefined`|`undefined \| boolean \| (error: TError) => boolean`| 

  <br />
  
  **[반환]**

  `useMutation`의 대표적인 반환값은 다음과 같습니다.

  |**반환 속성**|**설명**|**타입**|
  |---|---|---|
  |`data`|성공적으로 가져온 데이터|`undefined \| unknown`|
  |`error`|오류가 발생했을 때 오류 객체<br />오류가 발생하지 않았다면 `null` | `TError \| null`|
  |`mutate`|변이 실행 함수|`(variables: TVariables, { onSuccess, onSettled, onError }) => void`|
  |`isIdle`|변이 함수가 실행되기 전의 초기 상태인지 여부|`boolean`|
  |`isSuccess`|데이터를 성공적으로 가져왔는지 여부|`boolean`|
  |`isError`|변이 함수에서의 오류 발생 여부|`boolean`|
  |`isPaused`|변이 함수가 일시 중단되었는지 여부|`boolean`|
  |`isPending`|변이 함수가 실행 중인지 여부|`boolean`|
  |`status`|변이의 현재 상태<br />`idle`: 초기 상태<br />`pending`: 실행 중<br />`error`: 오류 발생<br />`success`: 성공|`string`|

  <br />

  아래의 코드는 `useMutation`을 이용해 사용자 목록에 사용자를 추가하는 예시의 일부입니다. 

  ```tsx
  export default function AddUser() {
  const [name, setName] = useState('')
  const [age, setAge] = useState(0)
  const queryClient = useQueryClient()

  const { mutate, error, isPending, isError } = useMutation({
    mutationFn: async (newUser: User) => { // 
      const res = await fetch('https://api.heropy.dev/v0/users', {
        method: 'POST',
        body: JSON.stringify(newUser)
      })
      if (!res.ok) throw new Error('변이 중 에러 발생!') // 변이 실패!
      return res.json() // 변이 성공!
    },
    onMutate: async newUser => {
      // 낙관적 업데이트 전에 사용자 목록 쿼리를 취소해 잠재적인 충돌 방지!
      await queryClient.cancelQueries({ queryKey: ['users'] })

      // 캐시된 데이터(사용자 목록) 가져오기!
      const previousUsers = queryClient.getQueryData<Users>(['users'])

      // 낙관적 업데이트
      if (previousUsers) {
        queryClient.setQueryData<Users>(['users'], [...previousUsers, newUser])
      }

      // 각 콜백의 context로 전달할 데이터 반환!
      return { previousUsers }
    },
    onSuccess: (data, newUser, context) => {
      console.log('onSuccess', data, newUser, context)
      // 변이 성공 시 캐시 무효화로 사용자 목록 데이터 갱신!
      queryClient.invalidateQueries({ queryKey: ['users'] })
    },
    onError: (error, newUser, context) => {
      console.log('onError', error, newUser, context)
      // 변이 실패 시, 낙관적 업데이트 결과를 이전 사용자 목록으로 되돌리기!
      if (context) {
        queryClient.setQueryData(['users'], context.previousUsers)
      }
    },
    onSettled: (data, error, newUser, context) => {
      console.log('onSettled', data, error, newUser, context)
    },
    retry: 3, // 변이 실패 시 3번 재시도
    retryDelay: 500 // 0.5초 간격으로 재시도
  })
  ```

<br />

- **`useInfiniteQuery`**

  '더 보기' 버튼으로 추가 데이터를 더 가져오거나, 무한 스크롤 기능을 구현하기 위해 TanStack-Query는 `useInfiniteQuery` 훅을 제공합니다.

  ```tsx
  const 반환 = useInfiniteQuery<페이지타입>(옵션)
  ```

  **[옵션]**

  `useInfiniteQuery`에는 `useQuery`에서 사용하는 옵션에 더해 다음과 같은 옵션을 지정할 수 있습니다.

  |**옵션**|**설명**|**기본값**|**타입**|
  |---|---|---|---|
  |`getNextPageParam`|**`필수`** 새로운 다음 페이지를 가져오면, 다음 페이지의 정보로 호출되는 함수<br />다음 페이지 번호를 반환<br />다음 페이지가 없다면 `undefined` 또는 `null` 반환||`(lastPage: TPage, allPages: TPage[], lastPageParam: number, allPageParams: number[]) => TPageParam \| undefined \| null`|
  |`getPreviousPageParam`|새로운 이전 페이지를 가져오면, 이전 페이지의 정보로 호출되는 함수<br />이전 페이지 번호를 반환<br />이전 페이지가 없다면 `undefined` 또는 `null` 반환||(firstPage: TPage, allPages: TPage[], firstPageParam: number, allPageParams: number[]) => TPageParam \| undefined \| null|
  |`initialPageParam`|**필수** 첫 번째 페이지의 번호||`TPageParam`|
  |`maxPages`|저장 및 출력할 최대 페이지의 수<br />페이지가 지나치게 많은 경우에 유용|`Infinite`|`number`|
 
  <br />
  
  **[반환]**

  `useInfiniteQuery`의 대표적인 반환값은 `useQuery`에서 사용하는 반환값을 포함해 다음 속성들을 추가로 가집니다.

  |**반환 속성**|**설명**|**타입**|
  |---|---|---|
  |`fetchNextPage`|다음 페이지를 가져오는 함수|`(options?: FetchNextPageOptions) => Promise<UseInfiniteQueryResult>`|
  |`fetchPreviousPage`|이전 페이지를 가져오는 함수|`(options?: FetchPreviousPageOptions) => Promise<UseInfiniteQueryResult>`|
  |`hasNextPage`|다음 페이지가 있는지 여부|`boolean`|
  |`hasPreviousPage`|이전 페이지가 있는지 여부|`boolean`|
  |`isFetchingNextPage`|다음 페이지를 가져오는 중인지 여부|`boolean`|
  |`isFetchingPreviousPage`|이전 페이지를 가져오는 중인지 여부|`boolean`|

  <br />
  
  다음은 영화 목록을 가져와 출력하고, '더 보기' 버튼을 클릭해 추가 영화 목록을 가져올 수 있는 `useInfiniteQuery` 훅의 예시입니다.

  ```tsx
  import React, { useState, useEffect, useCallback } from 'react'
  import { useInfiniteQuery } from '@tanstack/react-query'
  
  export interface Page {
    Search: Movie[] // 검색된 영화 목록
    totalResults: string // 검색된 모든 결과의 수
    Response: string // 'True' or 'False'
  }
  export interface Movie {
    Title: string
    Year: string
    imdbID: string
    Type: string
    Poster: string
  }
  
  export default function MovieList() {
    const [searchText, setSearchText] = useState('')
    const [queryText, setQueryText] = useState('')
  
    const {
      data, // 가져온 데이터
      isLoading, // 첫 페이지 가져오는 중
      isFetching, // 다음 페이지 가져오는 중
      isFetched, // 첫 페이지 가져오기 완료
      hasNextPage, // 다음 페이지가 있는지 여부
      fetchPreviousPage, // 이전 페이지 가져오기 함수
      fetchNextPage // 다음 페이지 가져오기 함수
    } = useInfiniteQuery<Page>({
      queryKey: ['movies', queryText], // 검색어로 쿼리 키 생성!
      queryFn: async ({ pageParam }) => {
        const res = await fetch(`https://omdbapi.com/?apikey=7035c60c&s=${queryText}&page=${pageParam}`)
        return res.json()
      },
      initialPageParam: 1, // 첫 페이지 번호 초기화!
      getNextPageParam: (lastPage, pages) => {
        // 한 페이지당 최대 10개까지의 영화 정보를 가져옴!
        // 마지막 페이지 번호 계산!
        const maxPage = Math.ceil(Number.parseInt(pages[0].totalResults, 10) / 10)
  
        // 다음 페이지가 있으면, 다음 페이지 번호 반환!
        if (lastPage.Response === 'True' && pages.length < maxPage) {
          return pages.length + 1
        }
        // 다음 페이지가 없으면 undefined | null 반환!
        return undefined
      },
      enabled: false, // 검색어 입력 전까지 대기!
      staleTime: 1000 * 60 * 5 // 5분
    })
  
    useEffect(() => {
      // 검색어가 변경될 때마다, 캐시된 데이터가 있어서 그 데이터의 다음 페이지를 가져오지 않도록 이미 캐시된 이전 페이지를 가져옴!
      if (queryText) fetchPreviousPage()
    }, [queryText, fetchPreviousPage])
  
    const handleSubmit = useCallback(
      (e: React.FormEvent) => {
        e.preventDefault()
        // 검색!
        if (searchText.trim()) {
          setQueryText(searchText)
        }
        // 초기화!
        if (!searchText.trim()) {
          setSearchText('')
          setQueryText('')
        }
      },
      [searchText]
    )
  
    return (
      <>
        <form onSubmit={handleSubmit}>
          <input
            type="text"
            value={searchText}
            placeholder="영화 제목을 입력하세요."
            onChange={e => setSearchText(e.target.value)}
          />
        </form>
        <div>
          {data?.pages.map((page, index) => (
            // 각 페이지의 출력 최적화를 위해, 페이지 단위 key 속성을 추가!
            <React.Fragment key={index}>
              {page.Search &&
                page.Search.map(movie => (
                  <div key={movie.imdbID}>{movie.Title}</div>
                ))}
            </React.Fragment>
          ))}
        </div>
        {isLoading ? <div>로딩 중..</div> : null}
        {isFetched && hasNextPage && (
          <button
            disabled={isFetching}
            onClick={() => fetchNextPage()}>
            {isFetching ? '로딩 중..' : '더 보기!'}
          </button>
        )}
      </>
    )
  }
  ```

<br />

## :book: 참고

- [공식문서 - TanStack Query](https://TanStack.com/query/latest/docs/framework/react/overview)
- [TanStack Query(React Query) 핵심 정리](https://www.heropy.dev/p/HZaKIE)
