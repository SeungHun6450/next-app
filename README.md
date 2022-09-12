# Next.js 개념 정리

## 생성

CRA로 생성: npm create-next-app 폴더이름 --typescript

일반적으로 CSR을 사용하면 disabled js시 화면이 나오지 않는다.
setting에서 dsiabled js를해도 SSR은 나온다.
네트워크에서 doc를 보면 (html파일)이미 그려져서 들어온 것이 보인다.

## Pre-rendering과 SSR, SSG

### pre-rendering

SSR, SSG의 기반이 됨

받은 js가 브라우저에서 실행이 되면서 인터렉티브하게 될 직전까지의 단계?
next.js항상 pre-rendering을 한다. js받기전 로드, SEO가 가능하며 확인하기 위해 js를 disabled 해보면 안다.
pre-rendering이면 html이 먼저 보여지고 이후 js가 로드된다.

※ hydration: 페이지가 브라우저에 로드되고 자바스크립트 코드가 실행되면서 페이지가 인터렉티브하게 동작할 상태가 되는 과정이다.
요약하자면 js실행으로 interactvie할 준비 과정이다.

### SSR

빌드 때 클라이언트를 요청할 때(서버에 요청) 마다 불러온다.
Request time에 동작한다.

이 때문에 TTFB(Time to first byte)는 getStaticProps보다 느리다.
each request (npm run dev는 무조건 each request)

기존 함수에 export default를 해주고 그 아래에 쓴다.

```js
export async function getServerSideProps(context) {
  // 가져올 데이터 ex) const res = await axios....
  return {
    props: {}, // 가져올 데이터 넣기 ex) res.data
  };
}
```

### SSG(generation)

빌드 때 단 한번만 하는(미리만듬) getStaticProps는 다시 오지 않는다.

Build time에 서버에서만 동작한다.

데이터가 바뀌지 않는다면 이것을 사용해보자!
블로그 같은 곳에서 사용하는것이 좋음!
buildtime

```js
export async function getStaticProps(context) {
  return {
    props: {},
  };
}
```

## Dynamic Routes

- getStaticPaths
- dynamic routes

### getStaticPaths

동적 라우팅 페이지 중, 빌드 시에 static하게 생성할 페이지를 정한다.
getStaticProps에서 id를 데이터로 가져온다

```js
export async function getStaticPaths() {
  const paths = getIds();
  // [
  // {
  //   params: {
  //     id:'ssg-ssr'
  //   }
  // },
  // {
  //   params: {
  //     id: 'pre-rendering'
  //   }
  // }
  // ]
  return (
    // paths: [{params: {id: 'ssr-ssg'}}],
    paths,
    fallback: false,
  )
}
return;
```

여기서 fallback을 false로 하면 주소 뒤에 아무거나 입력 후 해당 경로로 들어갈 시 404에러 페이지가 나온다.

true면 아래와 같이 getStaticPaths에서 동작할 수 있다.

```js
import { useRouter } from "next/router";

export default function test() {
  const router = useRouter();

  if (router.isFallback) {
    return <div> Loading </div>;
  }
}
```

404page는 커스텀이 가능하다.
pages/404.js를 수정하면 된다.

### dynamic routes

getStaticProps와 getStaticPaths에서 사용할 수 있다.
pages/posts/[...id]를 사용하면 아이디에 다중의 값을 들어올 수 있다.

### 요약

key 값은 동적으로 만들어 대응을한다.
fallback이 false면 404, true면 router.isFallback을 사용하여 만들 수 있다.
API Routes 외부에있는 것을 호출하지 않고 내부에서 특정 값을 만들어 놓고 api를 호출한다.
static generation페이지는 dynamic routes에서 할 수 있다.
/posts/[id]로 접근한다. id는 dynamic하게 바뀔 수 있다.

## Head

head태그에 원하는 것을 넣어줄 수 있다.

```js
import Head from "next/head";

export default test () {
  return (
    <div>
      <Layout>
        <Head>
          <title>title change</title>
      </Layout>
    </div>
  )
}
```
