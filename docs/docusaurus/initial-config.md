# 2. Initial Config

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



### Header 색상 & 폰트 설정 
- H1, H2, H3, H4, H5 색상 설정

### Git 연결 설정

### Sidebar 설정

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
