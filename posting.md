# 포스팅 작성 방법

### 포스팅 파일
파일 이름을 `yyyy-MM-dd-이름.md` 형식으로 해서 `_posts`폴더에 넣으면 됩니다.

### yaml front matter
이 내용을 포스팅 최상단에 적어야 합니다.
```yml
---
title: "제목"
date: yyyy-MM-dd HH:mm:ss
categories:
- 카테고리
tags:
- 태그1
- 태그2
- 태그3
---
```

title은 포스팅 제목이 됩니다.<br>
date는 포스팅 업로드 날짜가 됩니다.<br>
categories는 포스팅 카테고리가 되고, 포스팅 링크가 되는 `블로그주소/카테고리/yyyy/MM/dd/이름/`에서 `카테고리` 부분에 나타나게 됩니다.<br>
tags는 포스팅에 달리는 태그가 됩니다. 이 블로그 테마의 경우 포스팅 하단에 태그가 표시되고, `블로그주소/tags/`에서 태그들을 모아 볼 수 있습니다.

`/template` 폴더에서 몇 가지 예시를 확인할 수 있습니다.
