---
layout: post
title:  "GitHub Pages + Jekyll 적용 후기"
date:   2015-11-29 20:04:00 GMT+9
categories: daily
---

*적용 방법은 [GitHub 공식 홈페이지](https://help.github.com/articles/using-jekyll-with-pages/)에 잘 설명되어있으므로 여기서는 다루지 않는다.*

### 귀찮음을 이겨내다
Jekyll 적용을 해야지 해야지 하다가 귀찮아서 못하고 있었는데. 드디어 적용했다. GitHub Issue를 열어준 @dobestan에게 감사하다. 첫 Issue + 첫 Star도 감사...

해놓고 보니 Preview 하기도 좋고 여러모로 참 좋다. 이제 기다란 주소 대신 kgwangrae.github.io, 아니면 blog.gwangrae.kim으로 접속할 수 있게 되었다.

### TODO
디자인이 아직 황무지 상태다. 근데 글 옮기는 것도 귀찮았는데 디자인까지 고치자니 더욱 귀찮아져서 여기까지... 다음에 손이 닿으면 바꿔야겠다.

About page 정도는 고쳐두었다.

### GitHub-Flavored Markdown과의 미묘한 차이
아직 적용한지 몇 시간 밖에 안 되어서 잘 모르지만, GitHub에서 쓰던 Markdown과 미묘한 차이가 있다.

- Code Highlight 할 때 "```" 으로 하던 것을 이제 ruby code를 이용해야 한다는 점.
- GitHub에서는 개행을 하지 않아도 새 단락으로 넘어가던 부분이 한 줄로 붙어버리는 경우가 있었다. 이것 때문에 글이 깨지는 경우가 많아서 일일이 고쳤다..
- 글 위에 layout, title, date, categories 정보를 넣어야 한다. (아, 안 넣어도 상관은 없기는 한데, 관리할 때 불편해질 것 같아서 넣기로 했다.)

그건 그렇고 Code Font가 GitHub와 달리 정말 구리다. 어서 바꾸고싶다.

### 신기한 점

- 글을 터미널에서 고치면 (에디터나 댓글창은 없다. Static page이므로...) 로컬에서 바로 markdown이 html로 변환되어 결과를 확인할 수 있다. 물론 사이트에 올리려면 GitHub에 push를 해야 한다.
