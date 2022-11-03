---
title: yarn berry를 사용해보자!
date: '2022-11-03'
tags: ['yarn berry', 'node-modules', 'javascript']
draft: false
summary: node_modules보다 성능 좋은 yarn berry를 사용해보자!
---

### Yarn Berry?

yarnn berry란, node_modules 없이 node를 사용할 수 있도록 해주는 것이다.
https://toss.tech/article/node-modules-and-yarn-berry

### 적용법

먼저 react 파일들을 만든다.

```
npx create-react-app <설치를 윈하는 폴더> --template typescript
cd <설치를 윈하는 폴더>
```

먼저 리액트 디렉토리를 만들어준다.

- 타입스트립트가 아니어도 된다.

```
cd my-app
yarn set version berry // yarn 이 깔려있는다는 전제하에
```

해당 레포지토리에 .yarnrc.yml과 .yarn/releases 폴더 아래에 yarn-berry.js 또는 yarn-3.0.2.cjs (확장자명은 설정에 따라 다릅니다.) 파일, .pnp.cjs가 생성됩니다.
node_modules와 package.lock.json는 필요없으므로 지워준다.

```
rm -rf node_modules
rm -rf package.lock.json
```

만약 .yarnrc.yml파일에 아래와 같이 nodeLinker가 node-modules를 가리키고 있다면, Yarn berry의 PnP 방식의 zip 아카이브로 관리되는 것이 아닌 기존의 node_modules 의존성 폴더 방식으로 관리되게 됩니다. 그러므로 이 속성을 지워주어야 합니다.

```
# nodeLinker: node-modules 삭제!

yarnPath: .yarn/releases/yarn-berry.js
```

```
yarn install
```

그럼 끝~

#### Zero-installs 적용

```
# /.gitignore

.yarn/*
!.yarn/cache
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

#### Zero-installs 미적용

```
# /.gitignore

.pnp.*
.yarn/*
!.yarn/cache
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

## Typescript 패키지 설치

pachage.json에 보면 @types/node @types/react @types/react-dom @types/jest 가 설치 되어있을 것이다. 없으면 이런 식으로 설치하면 된다.

```
yarn add -D typescript @types/node @types/react @types/react-dom @types/jest
```

## Typescript 적용하기

위까지 했는데 vscode를 보면(vscode 환경이라고 가정) 빨간 지렁이가 코드 곳곳에 표시되어있는 것을 볼 수 있다. zip 아카이브에서 직접 파일을 읽을 수 있도록 VSCode 지원해주는 extension "zipfs"을 깔고 터미널에

```
yarn dlx @yarnpkg/sdks vscode
```

을 입력해주면 깔끔하게 사라지는 것을 볼 수 있다.
https://yarnpkg.com/getting-started/editor-sdks

추가적으로 typescript plugin을 import 시켜줍니다. 이 플러그인은 자체 types를 포함하지 않는 패키지를 추가할 때 @types/ 패키지를 package.json폴더에 종속성으로 자동으로 추가해줍니다. 이 플러그인 설치는 선택사항이지만 매우 유용하기 때문에 설치하겠습니다.

```
yarn plugin import typescript
```

마지막으로 command + shift + p키 (맥북 기준)를 눌러 TypeScript 버전 선택..을 검색해 Use Workspace Version을 선택해 workspace의 typescript sdk로 변경해줍니다.

## bebel 설치

yarn start를 했는데 "ERROR in ./src/index.tsx" 컴파일 에러 메시지가 뜬다면

```
yarn add -D babel-loader@latest @babel/core@latest @babel/preset-env@latest
```

https://velog.io/@skdusdl8804/React-React-Typescript-Webpack-%EC%B4%88%EA%B8%B0-%EC%85%8B%ED%8C%85%ED%95%98%EA%B8%B01

## eslint 설정

```
yarn add -D eslint

yarn eslint --init

yarn npm info "eslint-config-airbnb@latest" peerDependencies

yarn add -D eslint-config-airbnb-typescript
```

eslintrc 파일에

```
"extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended",
        "airbnb",
        "airbnb/hooks",
        "aribnb/typescript"
    ],
```

을 추가 작성해준다.

https://velog.io/@qhgus/React-ESLint-Prettier-Typescript-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0
https://velog.io/@9rganizedchaos/%EA%B0%9C%EB%B0%9C-%EC%B4%88%EA%B8%B0-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0-ESLint-eslint-config-airbnb-typescript-Prettier-React-TypeScript

## prettier 설정

```
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

설치 후에 .prettierrc 파일을 만들고

```
{
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": true,
  "useTabs": false,
  "printWidth": 80
}
```

라고 작성해준다. 규칙은 마음대로 정할 수 있다.

setting 에서 format on save를 체크해주고 또 Default Formatter 또한 prettier로 정해주면 된다.

## Zero install, PnP(plug n play)란?

참고
https://egas.tistory.com/107
https://haranglog.tistory.com/28
https://kod4284.github.io/2020/03/03/create-react-app-typescript-setting/
https://koras02.tistory.com/m/106
https://velog.io/@altmshfkgudtjr/yarn2%EC%99%80-%ED%95%A8%EA%BB%98-Plug-n-Play%EB%A5%BC-%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EC%9E%90
Elin 설정
https://junhojang.github.io/eslint/nodejs/eslint/
package.json
https://im-nc2u.tistory.com/entry/npm-yarn-%ED%8C%A8%ED%82%A4%EC%A0%80%EC%99%80-packagejson-%ED%8C%8C%EC%9D%BC%EC%97%90-%EB%8C%80%ED%95%B4
https://www.holaxprogramming.com/2017/12/21/node-yarn-tutorials/
