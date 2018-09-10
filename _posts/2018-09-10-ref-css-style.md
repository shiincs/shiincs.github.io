## CSS Styling Reference

- Reset.css

  ```css
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

