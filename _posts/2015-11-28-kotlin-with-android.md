---
layout: post
title:  "Kotlin + Android 소개"
date:   2015-11-29 05:37:00 GMT+9
categories: daily
---

[Kotlin 홈페이지](https://kotlinlang.org/)

### Android + Kotlin 조합은 아주 훌륭하다.
가끔 Kotlin을 **_Android의 Swift_**라고 부르는 글이 올라오기는 하지만, Kotlin은 아직 굉장히 Minor한 언어다. 1.0-beta2 version 까지 출시가 되었음에도 우리 팀을 제외하고는 주변에서 써봤다는 사람을 찾아보기 어렵다.

그럼에도 Kotlin은 Android Studio에서 정말 완벽하게 동작한다. 개발사가 Android Studio의 기반인 IntelliJ의 제작사 JetBrains라는 점이 아마 크게 작용하지 않았나 싶다.

Java code와 함께 사용해도 아주 매끄럽게 동작하며, 거의 모든 Android code를 (심지어 Unit test도) Kotlin으로 작성해도 디바이스에서 아무 문제없이 작동한다.

### 주요 장점
- **Java에서 Kotlin으로 자동 변환 가능.** 아주 잘 된다. Java와 syntax도 크게 다르지 않다.
- **Functional programming** 가능
- Type inference가 가능한 경우 타입을 굳이 명시하지 않아도 된다.
- Semicolon 생략 가능
- Lazy evaluation 지원
- 불필요한 getter, setter를 만들지 않아도 됨
- 엄격한 null check을 지원하는 타입 시스템
- 어떤 class를 상속하지 않더라도, **extension function**을 이용해 해당 class의 기능을 확장할 수 있다. (RealmObject의 상속이나 method 추가가 불가능한 Realm.io 사용 시 아주 유용하다.)

이 외에도 정말 많은 장점들이 있다.

### 예시
간단한 Fragment Java Code를 생각해보자.

**MyFragment.java**

{% highlight java linenos %}
TextView mTextView;

@Override
public void onActivityCreated(Bundle savedInstanceState) {
    super.onActivityCreated(savedInstanceState);
    mTextView = (TextView) getActivity().findViewById(R.id.my_text_view);

    Bundle arguments = getArguments();
    String mode;
    if (arguments != null) {
        mode = arguments.getString("mode");
        if (mode == null) {
            mode = "visible";
        }
    } else {
        mode = "visible";
    }

    if (mode.equals("visible")) {
        mTextView.setVisibility(View.VISIBLE);
    } else {
        mTextView.setVisibility(View.GONE);
    }
}
{% endhighlight %}

위 코드를 아래처럼 줄일 수 있다.

- ?. (Safe function call)과 ?: (Elvis operator)를 이용하여 null check을 단순화할 수 있다.
- If-else 문을 삼항연산자처럼 사용 가능하다. (대신 삼항연산자가 없다.)
- 항상 코드를 지저분하게 만드는 findViewById 부분도 extension function과 lazy property를 이용해 간결하게 정리할 수 있다.

**ViewExtensions.kt**

{% highlight kotlin linenos %}
// An example of extension function for Fragment
// You can also see getActivity() is replaced with activity property access syntax
fun <T : View> Fragment.findViewLazy(id: Int) = lazy { activity.findViewById(id) as T }
{% endhighlight %}

**MyFragment.kt**

{% highlight kotlin linenos %}
// Initializing a View value outside onActivityCreated is possible
// because it is initialized lazily.
// Thanks to ViewExtensions.kt, no type casting is needed every time.
val mTextView by findViewLazy<TextView>(R.id.my_text_view)

// Bundle? means savedInstanceState is a nullable Bundle.
override fun onActivityCreated(savedInstanceState: Bundle?) {
    super.onActivityCreated(savedInstanceState)

    // Safe call - if 'arguments' is null,
    // then getString is not called and the value is set to "visible" instead.
    // ?: is called Elvis Operator.
    // Without it, arguments?.getString("mode") will return null if 'arguments' is null
    val mode = (arguments?.getString("mode") ?: "visible") ?: "visible"
    
    // equals method can be replaced with == (use === for physical comparison)
    mTextView.visibility = if (mode == "visible") View.VISIBLE else View.GONE;
}
{% endhighlight %}

위의 예시와는 별개로, 어떤 parameter가 null일 때 return하는 코드도 아래처럼 한 줄로 줄일 수 있다.

{% highlight kotlin %}
fun sample(param: Any?) {
    // if param is null, then return
    param ?: return
    
    // Do other jobs below.
}
{% endhighlight %}

### 단점

#### *Android Lint가 작동하지 않는다.

Kotlin code에 실수로 API level 21 이상에서만 작동하는 method를 사용했다가 KitKat 기기에서 앱이 강제 종료되는 경험을 해보았다. Compile time에 아무런 경고도 받지 못했는데, 이러한 문제가 수정될 예정이 있는지는 모르겠다.

#### *Java reflection을 이용하는 library와 문제가 있을 수 있다.

Java class의 경우 reflection을 이용해 private field에도 접근할 수 있는데, Kotlin class의 private property는 무슨 수를 써봐도 접근이 안 된다. 내가 방법을 몰라서 못하고 있는 건지도 모르겠지만...

위에서 언급했던 Realm.io의 RealmObject의 경우 private field를 만들어놓으면 그걸 가지고 compile time에 ProxyClass를 generate하는데, RealmObject를 Kotlin 파일로 만들어놓으면 이 과정에서 에러가 난다.

### Java와 함께 사용할 때 주의점 

#### *기본적으로 모든 object들은 final이다. (function 포함)
Overriding을 하려면 **open**이라는 modifier를 붙여주어야 한다.

#### *Visibility modifier가 Java와 다르다.
Protected의 경우 Java에서는 같은 package에서도 접근이 가능했는데 Kotlin에서는 안 된다.

그리고 Kotlin에서는 default(no modifier)가 public이고, 대신 Java의 default modifier와 유사한 internal이라는 게 있다. 그런데 internal인 경우 같은 모듈?에 있는 다른 class에서 접근이 가능하다고 되어있어서, 혹시 같은 패키지에서도 접근이 되는지 알아봤더니 빌드는 잘 되지만 runtime에 error가 나면서 접근이 안 된다; 

Kotlin 홈페이지의 visibility modifier 설명이나 다른 글들을 계속 읽어보고 있는데, 아직 차이점을 완전히 파악하지 못한 것 같다.

#### *Compile time의 Nullable Check은 Kotlin code에 대해서만 이루어진다.
자바에서 모든 변수는 nullable하다.

그와 달리 Kotlin에서는 타입 뒤에 ?를 붙이지 않으면 null value를 허용하지 않는다.

{% highlight java %}
public void register(String id, String password, String email) {
    // email is optional.
}
{% endhighlight %}

위와 같은 코드를 별 생각 없이 아래와 같이 바꾸는 경우 문제가 될 수 있다.

{% highlight kotlin %}
fun register(id: String, password: String, email: String) {
    // email *MUST NOT* be null.
}
{% endhighlight %}

Java에서 아래처럼 method call을 한다면, build는 잘 되지만 runtime에 exception이 발생하게 된다. Compile time의 type check은 Kotlin compiler가 하는 일이므로 당연히 Kotlin code에 대해서만 이루어지기 때문이다.

{% highlight java %}
// What if an user enters nothing in emailVIew?
register(idView.getText(), passwordView.getText(), emailView.getText());
{% endhighlight %}

이 정도면 Kotlin에 대한 소개가 충분히 되었는지 모르겠다.

아무튼, Kotlin 정말 좋다!
