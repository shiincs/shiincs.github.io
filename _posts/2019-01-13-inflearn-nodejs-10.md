---
title: "[Node.js] 패키지 매니저"
date: 2019-01-13 10:00:00 +0900
tags:
  - Node.js
  - Inflearn
comments: true
---

# 패키지 매니저

## npm 설명과 패키지 만들기 (package.json)

npm === node package manager (노드 패키지 관리자)

패키지는 다른 사람들이 만들어 놓은 모듈(module)을 의미한다.

내 패키지를 만들려면 `npm init`을 한 뒤 필요한 내용들을 입력한다. --> 패키지에 대한 정보들이 들어있는 package.json 파일이 생긴다. 

package.json 에서 "main" 속성은 패키지의 진입점이되는 파일(가장 중요한 파일)을 의미한다.



## 패키지 설치하기

내 패키지에 다른 사람들의 패키지를 설치할 수 있다.

`npm install express` 등으로 패키지를 설치하면 package-lock.json 파일과 node_modules 폴더가 생긴다.

- node_modules 폴더에는 express 패키지와 express 패키지가 필요로 하는 또 다른 패키지들이 담긴다.
- package-lock.json 파일에는 express 패키지가 필요로 하는 또 다른 패키지들이 담긴다.

`npm install` 옵션으로는 

1. `--save-dev` 또는 `-D` : 배포환경에서는 사용하지 않고, 개발환경에서만 사용하겠다는 옵션 (개발을 원활하게 돕기 위한 패키지를 설치할 때 이 옵션을 붙여서 설치한다.) --> 이렇게 설치하면 package.json 에서 `"devDependencies"`  객체의 속성으로 저장된다.  "devDependencies" 에 담긴 패키지들은 배포 환경에서는 설치되지 않는다.
2. `--global` 또는 `-g` : 명령 프롬프트에서 명령어로 쓸 수 있는 패키지를 설치할 때에는 글로벌 옵션을 붙여서 설치한다. 



## SemVer 버전 이해하기

패키지의 버전 네이밍은 `SemVer(Semantic Versioning)`  에 따라서 세자리로 구성된다. 

첫째 자리는 major 버전, 둘째 자리는 minor 버전, 셋째 자리는 patch 버전이다. 즉, `major.minor.patch` 형식으로 버전 네이밍이 이루어진다.

- major 버전 : 대규모 변화 (이전 버전과의 호환 문제로 인한 고장 확률이 있다.)
- minor 버전 : 신기능 추가
- patch 버전 : 버그 수정

버전 네임 앞에 붙는 부호들은 각각의 의미를 지닌다.

- `^`(carot) : minor, patch 업데이트는 해도 된다.
- `~` : patch 업데이트는 해도 된다.
- 부등호(>=, <= 등등) : 해당 버전 이상, 이하면 상관없다.
- 아무런 기호가 없고 버전 네임만 있을 경우 : 딱 그 버전만 사용해야 한다.



## npm 명령어 알아보기

npm install 할때 패키지의 버전을 직접 지정해 줄 수 있다. (ex. `npm i express@4.14.0`)

`npm outdated` : 새로운 버전이 나온(update 할 수 있는) 패키지를 알려준다.

`npm update [패키지이름]` : 패키지 이름이 없을 경우 package.json의 버전 네이밍에 적힌 규칙(^, ~, 부등호)에 따라 업데이트할 수 있는 모든 패키지를 업데이트 한다. / 패키지 이름이 있을 경우 해당 패키지만 업데이트 한다.

`npm remove 패키지이름` 또는 `npm rm 패키지이름` : 패키지를 삭제한다.

`npm search 패키지이름` : 해당 이름이 들어간 패키지를 검색한다.

`npm info 패키지이름` : 해당 패키지의 package.json 정보(상세정보)가 나온다.

`npm ls 패키지이름` : 내 패키지에서 해당 패키지가 어떤 dependency에 설치되어 있는지를 알 수 있다. (해당 패키지가 어떤 패키지에 딸려서 설치되었는지를 찾아볼 수 있다.)



## 패키지 배포하기

우선 `npm adduser` 를 입력한뒤 npmjs.com의 아이디와 비밀번호, 이메일을 이용해서 로그인 한다.

`npm whoami` 를 이용해서 누구로 로그인 되었는지 확인할 수 있다.

`npm logout` 을 입력해서 로그아웃 할 수 있다.

내 패키지의 버전을 올리려면 `npm version 1.0.8` 이런식으로 올리거나, `npm version patch` / `npm version minor` / `npm version major` 명령어를 이용해서 각각의 버전을 올릴 수 있다.

`npm publish` 를 이용해서 배포하면 된다.

`npm unpublish 패키지이름 --force` 를 입력해서 패키지를 삭제할 수 있다.  **(publish한 이후로 24시간 지나면 삭제 불가능...)**