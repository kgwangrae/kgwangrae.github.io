---
layout: post
title:  "Android Instrumentation"
date:   2015-11-09 23:59:59 GMT+9
categories: daily
---

### Android Instrumentation
Test를 하면서 **Instrumentation**이라는 단어를 자주 접하게 되었는데, 내가 알고있던 instrument라는 단어의 뜻과 (악기, 장비) 잘 연결이 되지 않아서 혼란스러웠던 적이 있다.

[Wikipedia](https://en.wikipedia.org/wiki/Instrumentation)에서는 Instrumentation을 아래와 같이 표현하고 있다.

> **Instrumentation** is the use of measuring instruments to monitor and control a process.

개발할 때는 logger나 profiler를 사용하는 것이 Instrumentation이 된다. 자세히는 [이 문서](https://en.wikipedia.org/wiki/Instrumentation_(computer_programming))에 나와있다.

여튼, Android에도 [**Instrumentation**](http://developer.android.com/reference/android/app/Instrumentation.html)이라는 class가 있다. Application이나 Activity의 lifecycle을 조작한다거나, memory 할당 상태를 모니터링 하거나 기타 여러 유용한 일을 할 수 있다. 당연히 있을 거라고는 생각했는데, 왜 이제야 알았지;

Instrumentation test를 작성할 때 UI Thread에서 method를 실행하고 그 결과를 기다릴 때 복잡하게 lock을 이용했었는데 그럴 필요가 전혀 없었다; [runOnMainSync(Runnable)](http://developer.android.com/reference/android/app/Instrumentation.html#runOnMainSync(java.lang.Runnable))을 쓰면 되는 문제였다.

좋은 게 있어도 그게 있는 지 알아야 쓸 수 있다는 걸 다시금 느낀 하루였다. 고민되는 게 있으면 일단 검색부터 더 자세히 해보릇 해야겠다.

**_그건 그렇고 daily blog라고 써놨는데 벌써 망했다..._**
