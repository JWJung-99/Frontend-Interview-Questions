# Suspense란?

<br />

## 🫸 `Suspense`

`<Suspense>`는 자식 요소를 로드하기 전까지 화면에 대체 UI<sup>Fallback</sup>를 보여줍니다.

<br />

### `<Suspense>`

```jsx
<Suspense fallback={<Loading />}>
  <SomeComponent />
</Suspense>
```

- **Props**

  - `children`

    - 궁극적으로 렌더링하려는 실제 UI입니다. `children`의 렌더링이 지연되면, Suspense는 `fallback`을 대신 렌더링합니다.
  
  - `fallback`

    - 실제 UI가 로딩되기 전까지 대신 렌더링되는 대체 UI입니다. 올바른 React 노드 형식은 무엇이든 대체 UI로 활용될 수 있지만, 보통 **로딩 스피너나 스켈레톤처럼 간단한 Placeholder**를 활용합니다. Suspense는 `children`의 렌더링이 지연되면 자동으로 `fallback`으로 전환하고, 데이터가 준비되면 다시 `children`으로 전환합니다. 만약 `fallback`의 렌더링이 지연되면, 가정 가까운 부모 Suspense가 활성화됩니다.
 
- **주의사항**

  - React는 컴포넌트가 처음으로 마운트 되기 전에 지연된 렌더링을 하는 동안의 어떤 state도 유지하지 않습니다. 컴포넌트가 로딩되면 React는 일시 중지된 트리를 처음부터 다시 렌더링합니다.
  - `Suspense`가 트리의 컨텐츠를 보여주고 있을 때 또 다시 지연되면 `startTransition`이나 `useDeferredValue`로 인한 업데이트가 아닌 한 `fallback`이 다시 나타납니다.

<br />

### 사용법

- **컨텐츠를 로딩하는 동안 UI<sup>Fallback</sup> 보여주기**

  애플리케이션의 모든 곳을 Suspense 경계로 감쌀 수 있습니다.

  ```js
  <Suspense fallback={<Loading />}>
    <Albums />
  </Suspense>
  ```

  React는 `children`에 필요한 모든 코드와 데이터를 로딩할 때까지 `loading fallback`을 보여줍니다. 데이터를 모두 로딩하면 React는 `Loading` Fallback을 숨기고 로딩된 데이터로 컴포넌트를 렌더링합니다.

<br />

> [!IMPORTANT]
>
> **Suspense가 가능한 데이터만 Suspense 컴포넌트를 활성화합니다.**
>
> - Relay와 Next.js 같이 Suspense가 가능한 프레임워크를 사용한 데이터 가져오기
> - `lazy`를 활용한 지연 로딩 컴포넌트
> - `use`를 사용해서 캐시된 Promise 값 읽기
>
> Suspense는 Effect 또는 이벤트 핸들러 내부에서 가져오는 데이터를 감지하지 않습니다.

<br />

- **중첩된 컨텐츠가 로딩될 때 보여주기**

  컴포넌트가 일시 중단되면 가장 가까운 상위 Suspense 컴포넌트가 Fallback을 보여줍니다. 이를 통해 여러 SUspense 컴포넌트를 중첩하여 로딩 순서를 만들 수 있습니다.

  ```jsx
  <Suspense fallback={<BigSpinner />}>
    <Biography />
    <Suspense fallback={<AlbumsGlimmer />}>
      <Panel>
        <Albums />
      </Panel>
    </Suspense>
  </Suspense>
  ```

  위 예시에서 `Biography`를 보여줄 때 `Albums`가 로딩될 때까지 **기다릴 필요가 없습니다**.

    1. `Biography`가 로딩되지 않은 경우 `BigSpinner`가 표시됩니다.
    2. `Biography`의 로딩이 완료되면 `BigSpinner`가 컨텐츠로 대체됩니다.
    3. `Albums`가 아직 로딩되지 않은 경우 `Albums`와 상위 `Panel` 대신 `AlbumsGlimmer`가 표시됩니다.
    4. `Albums`의 로딩이 완료되면 `AlbusGlimmer`가 컨텐츠로 대체됩니다.

<br />

## :book: 참고

- [공식문서 - Suspense](https://ko.react.dev/reference/react/Suspense)
