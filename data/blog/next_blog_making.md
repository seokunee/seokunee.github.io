---
title: Next.js 블로그 템플릿을 이용해서 나만의 블로그 만들기
date: '2022-10-24'
tags: ['Next.js', 'github action', 'github page']
draft: false
summary: Next.js, github action, github page을 이용해서 나만의 블로그를 만들어보자.
---

![](https://velog.velcdn.com/images/seokunee/post/04c064fc-f413-4a73-99b0-bad17e6224b4/image.png)

#### 1. 마음에 드는 블로그 템플릿을 선택 후, gh-pages 브랜치 생성.

https://github.com/timlrx/tailwind-nextjs-starter-blog

![](https://velog.velcdn.com/images/seokunee/post/d7f96438-2474-4ac0-a035-ae957bddcae7/image.png)

나는 위 템플릿을 사용했다. 선택한 이유는 내가 심플한 것을 좋아한다 ㅎㅅㅎ
마음에 드는 템플릿을 fork 해온다.

'gh-pages'의 이름을 가지는 branch를 생성해준다. 그리고 setting->pages에 branch를 gh-pages로 설정해주고 Save해준다.

![](https://velog.velcdn.com/images/seokunee/post/eeafdfc9-67ce-4fc2-b3e9-6151c696a211/image.png)

#### 2. 로컬 환경에서 실행시켜본다.

내 템플릿의 경우에는

```
npm install
npm run dev
```

를 하면 로컬 환경에서 실행을 시킬 수 있었다.
템플릿마다 ReadMe에 잘 나와있을 것이므로 그것을 잘 따라하고 실행시키면 될 것이다.
이상이 없는지 확인하고 다음으로 넘어간다.

#### 3. package.json에 export 추가하기

```
"scripts": {
    "export": "next export",
  },
```

우리는 github page에 정적으로 배포를 하는 것이기 때문에 서버가 없다. 그래서 build한 out 파일을 HTML로 내보낼 것이다.
'next export'를 사용하면 Next.js 응용 프로그램을 정적 HTML로 내보낼 수 있으며, Node.js 서버 없이도 독립적으로 실행할 수 있다. 서버가 필요한 지원되지 않는 기능이 필요하지 않은 경우에만 'next export'를 사용하는 것이 좋다.

#### 4. yml 파일 추가

root 위치에 .github/workflows/build-and-deploy.yml
파일을 만들어주고 파일 안에 아래와 같은 코드를 추가해주자.
yml 파일은 github action이 어떻게 동작을 해줘야할지 스크립트로 작성해준 것이다.

```
name: Node.js CI

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Use Node.js 16
        uses: actions/setup-node@v3
        with:
          # update the Node version to meet your needs
          node-version: 16
          cache: npm

      - name: Install and Build 🔧
        run: |
          npm ci
          npm run build
          npm run export
          touch out/.nojekyll
      - name:
          Deploy 🚀
          # https://github.com/JamesIves/github-pages-deploy-action
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          branch: gh-pages
          folder: out
```

on: 은 github action이 동작해야할 때를 알려준다. 그래서 main 브랜치에 push 되면 action이 동작하게 된다.

필자는 브랜치가 master로 되어있어서 action이 계속 동작하지 않는 에러를 겪었었는데 만약 action이 잘작동하지 않는다면 브랜치 이름을 확인하길 바란다.

워크플로우는(workflow)는 작업(job)의 상위 개념이고, 단계(step)는 하위 개념이라고 볼 수 있다. 즉, 하나 이상의 작업(job)이 하나의 워크플로우(workflow)를 구성하며, 하나의 작업(job)은 순차적으로 수행되는 여러 개의 단계(step)로 이뤄진다.

그래서jobs에는 job을 넣고 job에는 step들로 job이 어떻게 행동할지 정해줬다.

https://www.viget.com/articles/host-build-and-deploy-next-js-projects-on-github-pages/

#### 5. git push를 해준다.

git push를 해주면 잘될 수 도 있다.
하지만 나는 에러가 났다.
**1. npm ERR! `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing. **

이 에러의 경우에는 npm install을 한번 더해서 pachage-lock.json을 업데이트해준 후에 다시 커밋하여 해결하였다.

**2. Error: Image Optimization using Next.js' default loader is not compatible with `next export`.
**

이 에러같은 경우에는 image를 불러올 때 loader 설정을 안해줘서 생긴 문제같다. 그래서 next.config.js 파일에 아래처럼 추가해줬다.

```
// next.config.js

module.exports = {
	images: {
    	loader: 'akamai',
    	path: '',
  	},
 }
```

같은 템플릿을 사용한다면 위 에러만 해결해준다면 정적 next.js 블로그를 github page에 github action으로 배포할 수 있을 것이다.

템플릿이므로 자신의 입맛에 맞게 바꿔서 사용하면 될 것 같다.

#### 참고

https://stackoverflow.com/questions/69984660/npm-ci-can-only-install-packages-with-an-existing-package-lock-json-or-npm-shrin

https://nextjs.org/docs/advanced-features/static-html-export

https://www.daleseo.com/github-actions-jobs/
