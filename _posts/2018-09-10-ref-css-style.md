---
title: "[CSS] Styling & Layout Reference"
date: 2018-09-10 10:30:00 +0900
tags:
  - CSS3
  - Reference
comments: true
---

## CSS Styling & Layout Reference

- Reset.css

  ```css
  /* ===============
      CSS RESET
  ================= */
  
  *,
  *::before,
  *::after {
    margin: 0;
    padding: 0;
    border: 0;
    box-sizing: border-box;
  }
  
  /* 스타일 초기화 */
  html, body, div, span, applet, object, iframe,
  h1, h2, h3, h4, h5, h6, p, blockquote, pre,
  a, abbr, acronym, address, big, cite, code,
  del, dfn, em, img, ins, kbd, q, s, samp,
  small, strike, strong, sub, sup, tt, var,
  b, u, i, center,
  dl, dt, dd, ol, ul, li,
  form, label, legend,
  table, caption, tbody, tfoot, thead, tr, th, td,
  article, aside, canvas, details, embed, 
  figure, figcaption, footer, header, hgroup, 
  menu, nav, output, ruby, section, summary,
  time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 1em;
    font: inherit;
    vertical-align: baseline;
  }
  /* HTML5 display-role reset for older browsers */
  article, aside, details, figcaption, figure, 
  footer, header, hgroup, menu, nav, section, main {
  	display: block;
  }
  ul {
  	list-style: none;
  }
  table {
  	border-collapse: collapse;
  	border-spacing: 0;
  }
  ```

- normalize.css
  - [normalize.css v8.0.0 github](https://github.com/necolas/normalize.css/blob/master/normalize.css)
  - [normalize.css v8.0.0 cdnjs](https://cdnjs.cloudflare.com/ajax/libs/normalize/8.0.0/normalize.min.css)

- float + clear 를 위한 clearfix 모듈

  - 부모 요소에 적용해서 쓴다.

  ```css
  /* CSS 모듈 */
  .clearfix::after {
    content:"";
    display: block;
    clear: both;
  }
  ```

- 숨김 콘텐츠

  - readable-hidden : html 구조를 잡기 위해 작성하는 text-node (heading element)
  - skip-nav : keyboard navigation을 통한 접근성 증대 향상을 위해 반복되는 navigation link를 건너뛰는 link

  ```css
  /* 숨김 콘텐츠 */
  .readable-hidden, .skip-nav {
    /* background: yellow; */
    /* display: none; */
    /* visibility: hidden; */
    position: absolute;
    width: 1px;
    height: 1px;
    /* overflow: hidden; */
    margin: -1px;
    clip: rect(0, 0, 0, 0);
  }
  .skip-nav:focus {
    width: 100%;
    height: auto;
    padding: 1em;
    background: #000;
    color: #fff;
    text-align: center;
    margin: 0;
    clip: rect(auto, auto, auto, auto);
    z-index: 100;
  }
  ```

- 반응형 웹 이미지

  ```html
  <div class="rwd-container">
    <img class="rwd-img" src="../images/normal.jpg" alt="반응형 학습 예시">
  </div>
  ```

  ```css
  .rwd-container {
    width: 50%;
    background: yellow;
  }
  .rwd-img{
    max-width: 100%;
    height: auto;
  }
  ```

- 반응형 배경 이미지

  ```html
  <div class="rwd-container">
    <div class="rwd-bg">
      <!-- 배경 이미지가 보여질 영역 -->
    </div>
  </div>
  ```

  ```css
  .rwd-container {
        width: 50%;
      }
      .rwd-bg {
        background-color: yellow;
        background-image: url("../images/title.png");
        background-repeat: no-repeat;
        background-position: 0 0;
        /* background-size: 100% 100%; 배경 이미지를 영역 안에 채우기 위해 크기를 줄이거나 늘린다. */
        /* background-size: cover; 배경 영역을 채우고 나머지는 잘라낸다. */
        /* background-size: contain; 이미지를 배경 영역 안에 온전히 채운다. */
        background-size: contain;
        width: 100%;
        height: 0;
        padding-top: 90%; /* 부모(.rwd-container) 크기(width)의 50% 만큼 차지한다. */
        transition: all 2s;
      }
  ```

- 고해상도 콘텐츠 이미지

  - srcset-pixel-ratio

  ```html
  <div class="rwd-container">
    <img class="rwd-srcset-img" src="../images/image-src.png" alt="고해상도 콘텐츠 이미지 예시" 
         srcset="../images/image-1x.png 1x,
                 ../images/image-2x.png 2x,
                 ../images/image-3x.png 3x,
                 ../images/image-4x.png 4x">
  </div>
  ```

  - srcset-sizes

  ```html
  <div class="rwd-container">
    <img class="rwd-srcset-img" src="../images/normal.jpg" alt="고해상도 콘텐츠 이미지 예시" 
           srcset="../images/small.jpg 550w,
                   ../images/medium.jpg 1024w,
                   ../images/large.jpg 1600w" sizes="550px, 1024px, 1600px">
     								    <!-- sizes="(max-width: 999px) 50vw, 100vw" -->
  </div>
  ```

- `<video>`

  ```html
  <div class="rwd-container">
    <video autoplay controls poster="../media/poster.jpg" muted>
      <source src="../media/stories.mp4">
      <track src="../media/stories-en.vtt" kind="captions" srclang="en" label="English Caption">
      <a href="../media/stories.mp4">구글 개발자 이야기</a>
    </video>
  </div>
  ```

- 반응형 비디오 콘텐츠

  ```css
  body {
    margin: 0;
  }
  .rwd-container {
    width: 50%;
    background: yellow;
  }
  .rwd-video {
    width: 100%;
    height: auto;
  }
  ```

- 미디어 쿼리

  ```css
  @charset "utf-8";
  
  /* 공통 코드 */
  body {
    background: yellow;
  }
  
  /* 모바일 디바이스 */
  @media screen and (min-width: 320px) and (max-width: 480px) {
    body {
      background: pink;
    }  
  }
  
  /* 태블릿 디바이스 */
  @media screen and (min-width: 481px) and (max-width: 1024px) {
    body {
      background: lime;
    }
  }
   c
  /* 랩탑 및 와이드 스크린 디바이스 */
  @media screen and (min-width: 1025px) {
    body {
      background: skyblue;
    }
  }
  ```
