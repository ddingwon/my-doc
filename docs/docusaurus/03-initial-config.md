# 3. Initial Config

### Codeblock 설정

[dosusaurus.config.js](../../docusaurus.config.js) 테마 설정 부분에 사용할  Language 추가

```js title="Add Highlight Language"
prism: {
    theme: prismThemes.github,
    darkTheme: prismThemes.dracula,
    additionalLanguages: ['java', 'bash'],  /* 이 부분 추가 */
},
```

- Codeblock **title** 설정

Codeblock `js title="Add Highlight Language"` 언어 다음 title을 사용해 title을 설정할 수 있다.

![결과](image.png)




### 테마설정



### Footer 설정



### Nav 설정

**1. Nav 폰트 설정**

사용자가 페이지 스크롤 다운을 시작하면 자동으로 메뉴바가 숨겨지고 스크롤을 올리면 다시 보여지는 UI 기능을 활성화할 수 있습니다.

```css
      navbar: {
        hideOnScroll: true,
      }
```

**2. Nav 검색 기능 추가**



### Header 색상 & 폰트 설정 
```css
/* 헤더 태그의 기본 스타일 설정 */
h1 {
  font-size: 2.2em; /* 글꼴 크기 */
  font-weight: bold; /* 글꼴 굵기 */
  color: #1f71ce; /* 글꼴 색상 */
}

h2 {
  font-size: 1.7em;
  font-weight: bold;
  color: #18bb25;
}

h3 {
  font-size: 1.2em;
  font-weight: bold;
  color: #dde00c;
}

/* 선택되었을 때 하이라이트 스타일 */
.highlighted {
  background-color: yellow; /* 하이라이트 배경색 */
  border-radius: 5px; /* 둥근 테두리 */
}
```

:::note 
Dark and Light 테마에 따른 적용 나중에 설정 필요함
:::

### Git 연결 설정

### Sidebar 설정

**1. Sidebar font**
`Sidebar` 폰트 굵기 설정을 위해 /src/css/custem.css 파일에 내용 추가

```css
/* 사이드바 항목의 폰트 스타일 설정 */
.menu__link {
  font-size: 15px; /* 원하는 폰트 크기 */
  font-weight: 700; /* 원하는 폰트 굵기 */
}
```

**2. Sidebar**
`Sidebar` 카테고리를 자동으로 숨기는 옵션을 적용하기 위해 아래 코드를 `docusaurus.config.js` 파일에서 추가합니다.

```css
      docs: {
        sidebar: {
          hideable: true,
          autoCollapseCategories: true,
        },
      },
```

### Site title 설정


### sidebar와 body 넓이 조정 기능 추가

### custom css 적용

1. Theme color 설정
`/src/css/customcss` 파일에서 내용 변경

```css
:root {
  --ifm-color-primary: #0C63E7;
  --ifm-color-primary-dark: #094AA8;
  --ifm-color-primary-darker: #063480;
  --ifm-color-primary-darkest: #042259;
  --ifm-color-primary-light: #5C9CE7;
  --ifm-color-primary-lighter: #8DBFF1;
  --ifm-color-primary-lightest: #C2DFF7;
  --ifm-code-font-size: 95%;
  --docusaurus-highlighted-code-line-bg: rgba(0, 0, 0, 0.1);
}

/* For readability concerns, you should choose a lighter palette in dark mode. */
[data-theme='dark'] {
  --ifm-color-primary: #75BFFF;
  --ifm-color-primary-dark: #5AA8FF;
  --ifm-color-primary-darker: #4092FF;
  --ifm-color-primary-darkest: #267CFF;
  --ifm-color-primary-light: #91D4FF;
  --ifm-color-primary-lighter: #B0E2FF;
  --ifm-color-primary-lightest: #CCF0FF;
  --docusaurus-highlighted-code-line-bg: rgba(0, 0, 0, 0.3);
}
```

2. Font size 설정
`/src/css/custom.css` 파일에 내용 추가
```css
/* General styling for the page */
body {
  font-family: Roboto, sans-serif;
  font-size: 14px;
  line-height: 1.6;
}
```

### Highlight.js 설정

`/src/components/Homepagefeatures` 폴더 안에 Highlight.js 파일을 생성하고 MDX에서 해당 함수를 호출하여 사용할 수 있다. 

- 예시

```js title='src/components/Highlight.js'
import React from 'react';

export default function Highlight({children, color}) {
  return (
    <span
      style={{
        backgroundColor: color,
        borderRadius: '2px',
        color: '#fff',
        padding: '0.2rem',
      }}>
      {children}
    </span>
  );
}
```

- Markdown 파일에서 호출하여 사용

- 예시

```md title=docs/docusaurus/01-intro.md

# <Highlight color="#25c2a0"> 1. Tutorial Intro </Highlight>

```

- 결과: 아래 이미지처럼 텍스트에 Highlight가 적용된다. 

![alt text](image-1.png)


