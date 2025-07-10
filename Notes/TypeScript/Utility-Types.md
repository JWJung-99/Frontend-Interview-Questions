# 유틸리티 타입이란?

<br />

## 🃏 유틸리티 타입

유틸리티 타입(Utility Types)이란 TypeScript에서 자체적으로 제공하는 특수한 타입들로, 제네릭, 맵드(mapped) 타입, 조건부 타입 등의 타입 조작 기능을 이용해 실무에서 자주 사용되는 유용한 타입들을 모아 놓은 것을 의미합니다.

![image](https://github.com/user-attachments/assets/f8e58de1-6528-4f44-aeaf-d611dcfb7771)


### `Partial` | `Required` | `Readonly`

#### `Partial<T>`

Partial은 "부분적인" 또는 "일부분"의 라는 뜻으로 **특정 객체 타입의 모든 프로퍼티를 선택적 프로퍼티로 변환**하는 유틸리티 타입입니다. 따라서 기존 객체 타입에 정의된 프로퍼티들 중 일부분만 사용할 수 있도록 도와주는 타입입니다.

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

const draft: Partial<Post> = { // ✅ 모든 프로퍼티가 없어도 OK
  title: "제목 나중에 짓자",
  content: "초안...",
};
```

위 예시처럼 `Partial<T>` 타입은 타입 변수 `T`로 전달한 `Post` 타입의 모든 프로퍼티를 선택적 프로퍼티로 변환합니다.

<br />

#### `Required<T>`

Required는 "필수의" 또는 "필수적인"이라는 뜻으로 **특정 객체 타입의 모든 프로퍼티를 필수 프로퍼티로 변환**하는 유틸리티 타입입니다. 

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

const withThumbnailPost: Required<Post> = { // ❌ thumbnailURL 프로퍼티가 없으므로 오류
  title: "한입 타스 후기",
  tags: ["ts"],
  content: "",
  // thumbnailURL: "https://...",
};
```

위 예시처럼 `Required<T>` 타입은 타입 변수 `T`로 전달한 `Post` 타입의 모든 프로퍼티를 필수 프로퍼티로 변환합니다.

<br />

#### `Readonly<T>`

Readonly는 "읽기 전용"이라는 뜻으로 **특정 객체 타입의 모든 프로퍼티를 읽기 전용 프로퍼티로 변환**하는 유틸리티 타입입니다.

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const readonlyPost: Readonly<Post> = {
  title: "보호된 게시글입니다.",
  tags: [],
  content: "",
};

readonlyPost.content = '해킹당함'; // ❌
```

위 예시처럼 `Readonly<T>` 타입은 타입 변수 `T`로 전달한 `Post` 타입의 모든 프로퍼티를 읽기 전용 프로퍼티로 변환하여 점 표기법을 이용해 특정 프로퍼티의 값을 수정하려고 하면 오류를 발생시킵니다.

<br />

### `Pick` | `Omit` | `Record`

#### `Pick<T, K>`

Pick은 "뽑다" 또는 "고르다"라는 뜻으로 **특정 객체 타입으로부터 특정 프로퍼티만을 골라내는** 유틸리티 타입입니다.

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const legacyPost: Pick<Post, "title" | "content"> = { // 추출된 타입 : { title : string; content : string }
  title: "",
  content: "",
};
```

위 예시처럼 타입 변수 `T`에 `Post` 타입을 전달하고 타입 변수 `K`에 `"title" | "content"`를 전달하면 `Post` 타입으로부터 `"title"`과 `"content"` 프로퍼티만 뽑아낸 객체 타입이 됩니다.

<br />

#### `Omit<T, K>`

Omit은 "빼다" 또는 "생략하다"라는 뜻으로 **특정 객체 타입으로부터 특정 프로퍼티만을 제거하는** 유틸리티 타입입니다.

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const noTitlePost: Omit<Post, "title"> = {
  content: "",
  tags: [],
  thumbnailURL: "",
};
```

위 예시처럼 타입 변수 `T`에 `Post` 타입을 전달하고 타입 변수 `K`에 `"title"`을 전달하면 `Post` 타입으로부터 `"title"` 프로퍼티만 제거한 객체 타입이 됩니다.

<br />

#### `Record<K, V>`

`Record<K, V>`의 **타입 변수 `K`에는 어떤 프로퍼티들이 있는지 String Literal Union 타입을 할당하고, 타입 변수 `V`에는 프로퍼티의 값을 할당**합니다.

```ts
type Thumbnail = Record<
  "large" | "medium" | "small",
  { url: string }
>;
```

위 예시는 `K`에 `"large" | "medium" | "small"`이 할당되었으므로 `large`, `medium`, `small` 프로퍼티가 있는 객체 타입을 정의하고, 각 프로퍼티 `value`의 타입은 `V`에 할당한 `{ url: string }`이 됩니다.

<br />

### `Exclude` | `Extract` | `ReturnType`

#### `Exclude<T, U>`

`Exclude` 타입은 다음과 같이 **`T`로부터 `U`를 제거**하는 유틸리티 타입입니다.

```ts
type TChar = "a" | "b" | "c" | "d";

type T01 = Exclude<TChar, "a" | "c">; // "b" | "d"
```

<br />

#### `Extract<T, U>`

`Extract` 타입은 다음과 같이 **`T`로부터 `U`를 추출**하는 유틸리티 타입입니다.

```ts
type TChar = "a" | "b" | "c" | "d";

type T01 = Extract<TChar, "a" | "c">; // "a" | "c"
```

<br />

#### `ReturnType<T>`

`ReturnType` 타입은 **타입 변수 `T`에 할당된 함수 타입의 반환값을 추출**하는 유틸리티 타입입니다.

```ts
function funcA() {
  return "hello";
}

type ReturnA = ReturnType<typeof funcA>; // string
```

<br />

## :book: 참고

- [한입타스 - 유틸리티 타입](https://ts.winterlood.com/a5b224e2-3f3e-432c-8bfb-7b338762514f)
