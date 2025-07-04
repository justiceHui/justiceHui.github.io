# JusticeHui가 PS하는 블로그

### 알고리즘 튜토리얼 프로젝트
[github projects](https://github.com/justiceHui/justiceHui.github.io/projects)에서 작성할 예정 혹은 너무 예전에 작성하여 수정이 필요한 게시물들을 관리하고 있습니다.<br>
원하시는 내용이나 수정해야 할 것이 있으면 issue로 넣어주시면 감사하겠습니다.

### 오류 제보
[github issues](https://github.com/justiceHui/justiceHui.github.io/issues)로 주시거나, 게시물에 댓글로 달아주시면 확인 후 수정하겠습니다.

### Pull requests
사이트 이용 중 불편하신 점을 직접 수정하고 싶으면 pull requests를 넣어주세요. 확인 후 반영하겠습니다.

### 브라우저 지원 여부
![Browser support](http://iissnan.com/nexus/next/browser-support.png)

### Repository Fork
이 블로그를 포크 후 수정해서 사용하실 생각이라면 아래 내용을 확인해주세요.

1. 이 폴더들을 **제외한** 나머지 폴더는 **필요없는 폴더입니다**.
  * `_data`, `_includes`, `_layouts`, `_posts`, `_posts`, `_sass`
  * `about`, `archives`, `assets`, `categories`, `category`, `navigator`, `tag`, `tags`
2. 아래 폴더/파일을 **삭제해 주세요**.
  * `_data/teacher.yml`
  * `_data/cv/` 폴더 전체
  * `about/award/` 폴더 전체
  * `about/secpro/` 폴더 전체
  * `teach/` 폴더 전체
3. 이 내용들을 **수정해야 합니다**.
  * `_includes/_layout.html`의 google analytics 관련 부분
  * `_includes/index.html`의 github chart 관련 부분
  * `about/index.md`, `navigator/index.html` 전체
  * `_config.yml`의 Disqus 관련 부분
  * `_includes/advertise.html` 전체
  * (사이드바에 광고를 넣지 않는다면) `_includes/_macro/sidebar.html` 하단 `{% include advertise.html %}` 부분 삭제
4.  포스팅 작성 방법은 [여기](https://github.com/justiceHui/justiceHui.github.io/blob/master/posting.md)를 참고해 주세요.
