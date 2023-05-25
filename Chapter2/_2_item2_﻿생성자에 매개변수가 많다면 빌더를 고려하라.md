> # Effective-Java
🔖 CS Books - effective java(joshua bloch) learning and summarizing   
📗 effective java  ( joshua bloch )   
**⭐ https://blog.naver.com/willyouspeedup/223107977824 네이버 블로그**
　   
 ***    
 　     
>  ﻿문제 상황 : 생성자에 매개변수가 많다  💬   

~~~
public class Pizza {
    private final int color;
    private final int pineApple;
    private final int apple;
    private final int fat;
    private final int sodium;
    private final int calories;
    private final int rice;
    private final int flour;
    .
    .
    .

}
~~~
﻿
위와 같은 코드를 보자. 　   
Pizza class에는, member field가 매우 많다. 　   
그리고, 이들 중 대부분은 "선택적 매개변수"이다. 　   
　   
　   
- 선택적 매개변수 　   
pizza에서 항상 rice가 사용되는 것이 아니다. 　   
선택적으로 사용될 경우, 기본값으로 그냥 의미없이 0이 주어진다.　   
　   
***
　   
> ﻿해결 방법 1. 점층적 생성자 패턴(telescoping constructor pattern)  💬   

﻿
과거에 사용한 방법이다.　   
　   
당연한 말이지만, 이 포스팅에서는　   
3번. 빌더 패턴(Builder pattern)을 사용하길 권장한다.　   
　   
과거에 사용한 방법을 학습하고, 이 부분에서 단점을 이해하고,　   
빌더 패턴에서 어떻게 단점이 극복되고 있는지 확인하자.　   
　   
- 점층적 생성자 패턴(telescoping constructor pattern)　   
: 매개변수의 개수를 서로 다르게하여, 필요한 모든 여러 개의 생성자를 다 작성하는 것이다.　   
: 이 수 많은 생성자 중, 내가 필요한 매개변수를 포함하면서도, 필요없는 매개변수를 가장 적게 사용하는 생성자를 선택해서 사용하게 된다.　   
　   
~~~
public class Pizza {
    (수많은 field들)

    //생성자 1
    public Pizza(int color) { }

    //생성자 2
    public Pizza(int color, int pineApple) { }

    .
    .
    .
}
~~~
　   
- 점층적 생성자 패턴 단점

한 눈에 봐도 보인다.　   
생성자가 너무 많아진다. 그런데, 생성자는 이름을 줄 수 없다. => 관리의 어려움, 실수 유발 가능성, 같은 이름의 생성자가 매우 많으면, 가독성이 떨어진다.
필요 없는 매개변수도 포함해서 생성자를 써야 한다 => 개발의 복잡성, 결합도 향상
　   
　   
***
　   
　   
> ﻿해결 방법 2. 자바빈즈 패턴(JavaBeans pattern)   💬   


- 자바빈즈 패턴(JavaBeans pattern)　   
: 매개변수가 없는 생성자로 객체를 만든다.　   
: 그 다음, 각 field별로 각각 setter를 이용하여 내가 원하는 field의 값만 지정해준다.　   

~~~
public class Pizza {
    (수많은 field들)

    //생성자
    public Pizza() { }

    //수 많은 Setter들
    public Pizza(int color, int pineApple) { }

    .
    .
    .
}
~~~

　   
- 점층적 생성자 패턴(1)의 단점 해결　   
점층적 생성자 패턴의 2가지 단점을 모두 해결했다.　   
　   
- 그럼에도 자바빈즈 패턴의 단점
객체 하나를 만들기 위해, "setter method"를 여러 번 호출해야 한다.　   
객체 하나가 완전히 완성되기 전까지는, "일관성(consistency)"이 무너진 상태로 유지된다.　   
=> 점층적 생성자 패턴(1)에서는 최소한, 이 문제들은 없었다.　   
　   
　   
- 일관성이 무너진 객체의 문제점　   
"불변(immutability)"으로 만들 수 없다. (불변은, 아래에 나옵니다)　   
코드가 하나로 모여있는 것이 아니라, 흩어져 있기 때문에, 에러가 발생했을 때 찾기 쉽지 않다.　   
　   
　   
***
　   
> ﻿해결 방법 3. 빌더 패턴(Builder pattern)  💬   

　   
- 빌더 패턴(Builder pattern)　   
　   
본래 class(Pizza) 의 instance를 만들기 위해, 빌더 클래스/객체를 이용한다.　   
빌더 클래스(Builder) 는, 보통 본 class 내부에 생성한다.　   
　   
ⓐ) 생성자/정적 팩터리를 이용하여, "빌더 객체"를 생성한다. 이 때, "필수 매개변수"만 사용한다.　   
ⓑ) "빌더의 setter method"로, 원하는 "선택 매개변수"값을 지정해준다.　   
ⓒ) 지정이 다 끝났으면, 매개변수가 없는 Builder.build() method를 호출하여 우리가 궁극적으로 필요하던 "본래 class의 instance"를 얻어야 한다.　   
ⓓ) Builder.build()에서는, 당연히 객체 생성을 위해 "본래 class의 생성자"가 호출된다. 생성자의 매개변수로 이 Builder 객체 자체가 넘어간다.　   
본래 class에서는, Builder 객체에 담긴 정보들을 이용하여 본래의 객체를 한 번에 완성시킨다.　   
그래서 보통, 빌더 패턴에서 본래 클래스는 불변(Immutable)이다.　   
　   
　   
~~~
public class Pizza {
    private final int color;
    private final int pineApple;
    private final int apple;
    private final int fat;
    private final int sodium;
    private final int calories;
    private final int rice;
    private final int flour;

    //Builder class 내부에 생성
    public static class Builder {

        //필수 매개변수들 
         private final int color;
         private final int fat;
         
         //선택 매개변수들
         private final int pineApple;
         private final int apple;
         private final int sodium;
         private final int calories;
         private final int rice;
         private final int flour;

         //1. Builder의 생성자
         public Builder(int color, int fat) { this.color=color; this.fat=fat; } //필수 매개변수만 설정해준다

         //2. Builder의 선택 매개변수 setter들, 자기 자신 객체를 반환하기 떄문에, "메서드 연쇄적 호출"이 가능함
         public ★Builder★ setPineApple(int pineApple) { this.pineApple=pineApple; ★return this;★ } 
         public Builder setApple(int Apple) { this.Apple=Apple; ★return this;★ }
            .
            .
            .
         //3. Builder의 build()메소드는, Pizza의 생성자를 호출한다. ★매개변수로는 이 Builder 객체를 보낸다.★
         public ★Pizza★ build() { return ★new Pizza(this)★; }

    }


     //4. Pizza는 불변이다. 생성자를 private로 하여 의도치않은 외부 접근을 막는다. 
     //Builder 객체의 정보를 이용하여 Pizza 객체를 구성한다.
     private Pizza(Builder builder) {
          color=builder.color;
          fat=builder.fat;
          .
          .
          .
    }
}
~~~
위 코드를 보자.

특정한 Pizza 객체는 Builder.build()를 통해서만 "오직 1회" 생성될 수 있다.

Pizza()생성자로만 Pizza의 field(color, pineApple..)들 값이 정해진다.

Pizza()생성자로 Pizza 객체가 생성되고 나면, private final 인, setter도 없는 Pizza의 field들은 "값이 바뀔 수가 없다"

Builder.build()를 하더라도, 새로운 Pizza() 객체가 생성되어 반환될 뿐이다.

===> "불변"이 맞다! (불변은 아래에 자세히 나옵니다)
　   
　   
- 점층적 생성자 패턴(1)의 단점 해결　   
점층적 생성자 패턴의 2가지 단점을 모두 해결했다.　   
　   
- 자바빈즈 패턴(2)의 단점 해결　   
자바빈즈 패턴의 2가지 단점을 모두 해결했다.　   
　   
　   
- 빌터 패턴(3)의 장점　   
**장점1. Builder의 setter들은 객체 자기 자신을 반환한다. 따라서, "메서드 연쇄적 호출(method chaning/fluent API)"이 가능하다.**   
이러한 방식은, 개발자에게도 쉬운 방법이며, 규칙성이 있이게 읽는 이에게도 이해가 쉬운 방식이다.　   
~~~
Pizza pizza= new Pizza.Builder(1,20).apple(10).pineApple(500).rice(10).build();
~~~
　   
　   
**장점2. 불변과, 불변식을 관리하기 용이하다.**　   
빌더 패턴은, 점층적 생성자 패턴(1)과 자바빈즈 패턴(2)의 단점을 해결하였다.　   
일관성 덕분에, "불변" 객체로 만들 수 있다.　   
　   
"불변식(invariant)" 검증은 다음과 같이 한다.　   
Builder의 Constructor과 Setter에서 매개변수 "각각"을 검증한다.　   
본 class의 Constructor가 호출될 때, "매개 변수 여러개"에 걸친 불변식을 검증한다.　   
　   
예를 들어, Builder.apple(-1)에서, 0보다 작은 값이 왔으므로 에러를 던져주면 된다. (내가 임의로 만든 불변식)　   
예를 들어, new Pizza(builder)에서, ( color+fat+rice+apple+pineApple..) < 50이라면, 에러를 던져주면 된다. (내가 임의로 만든 불변식)　   
　   
　   
**장점3. 계층(부모-자식 상속) 구조와 잘 어울리는 패턴이다.**　   
클래스를 만들고, 이를 상속받는 여러 다양한 자식 클래스를 두어 커스터마이징 하자.　   
자식 클래스들은, 부모 클래스에서 지정한 field를 확장하여 필수, 선택 field를 각기 다르게 만들어줄 수 있다.　   
빌더 패턴은, 이러한 계층 구조와 잘 어울린다.　   
　   
　   
~~~
﻿abstract Pizza 코드는 저작권을 지키기 위해 도서를 직접 참고해주세요 :) 
~~~
~~~
NyPizza 코드는 저작권을 지키기 위해 도서를 직접 참고해주세요 :) 
~~~
~~~
CalPizza 코드는 저작권을 지키기 위해 도서를 직접 참고해주세요 :) 
~~~
위 코드를 분석해 보자. (도서 19~21p)　   
　   
우선, 여기서 굳이 추상 클래스를 쓴 이유는, 자식 클래스에서 반드시 재구현Override해줘야 하는 추상 method self()가 있기 때문이다.　   
그것 말고는 크게 없다는 점을 알고 아래를 읽도록 하자!　   
애초에 추상 메소드가 없으면, 추상 클래스와 일반 클래스는 거의 같다.　   
(추상 클래스는 아래에 자세히 나옴)　   
　   
　   
🙂 **Field**
　   
부모 Pizza class의 field : enum Topping, Set<Topping> toppings는 자식 class에 공유된다.　   
(복제해서 새로 만드는 것이 아니라, 자식이 부모에 있는 field에 접근 가능)　   
　   
- 추상 클래스 Pizza에서는 필수 field로 Set<Topping> toppings를 지정해줬다.　   
- 자식 클래스 NyPizza에는, 부모의 Set<Topping> toppings을 사용하면서, 추가 "필수" field로 Size size를 만들어 사용하고 있다.　   
=> 필수이기에, Builder 생성자 매개변수(ⓐ)를 이용한다.　   
- 자식 클래스 CalPizza는, 부모의 Set<Topping> toppings을 사용하면서, 추가 "선택" field로 sauceInside를 만들어 사용한다.　   
=> 선택이기에, CalPizza에는 setter(ⓑ)만 이용한다.　   
또, 기본 생성자 public Builder() { }가 생략되어, 컴파일러에 의해 자동 생성된다. "Builder 객체는 필수로 필요하기 때문이다"　   
　   
-추상 클래스 Pizza.Builder에서는 필수 field로 EnumSet<Topping> toppings를 지정해줬다.　   
-자식 클래스 NyPizza.Builder에서는 필수 field로 Size size를 만들어 사용하고 있다.　   
-자식 클래스 CalPizza.Builder에서는 선택 field로 sauceInside를 만들어 사용하고 있다.　   
　   
=> 빌더 패턴(3)에 따라, Builder 안에서 사용된 field값들이, 각 본 클래스인 Pizza/NyPizza/CalPizza로 적용되어, 이들의 불변 객체를 생성해낸다는 점을 주목하자.　   
본 class와 내부 Builder class의 변수의 이름이 같지만, 이러한 빌더 패턴을 이해하고 있다면 이해에 도움이 될 것이다.　   
　   
　   

🙂  **Method**　   
　   
Method도 마찬가지로 공유된다.　   
그 것이, 추상 클래스이든 아니든 상관없이 공유된다. 추상 클래스의 일반 method도 공유된다는 것이다. (추상 method면 X)　   

~~~
 public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            System.out.println("부모의 addTopping이 호출되고 있군요");
            return self();
        }
 ~~~
위 코드를 보자. abstract Pizza의 abstract Build의 "addTopping"은, 자식에게 공유된다.　   
자식은, 부모의 Pizza.Build.addTopping을 쓸 수 있다!　   
(자식 자신의 것이 아니다. 부모 것을 호출하는 것이다!!)　   
　   
　   
　   
🙂  **부모 Pizza 클래스의 addTopping**　   
　   
addTopping은, setter 역할을 한다.　   
이 Builder method를 여러 번 호출하게 하고, 여러 번 입력된 각 데이터를 "하나의 자료구조에 모아서 사용"하고 있다.　   
이 때, 1번 장점인 "연쇄적 호출"을 이용하기에, 개발자도, 읽는 이도 편하게 이해하고 사용할 수 있다!　   
　   
또, addTopping은 self()를 반환하고 있다.　   
self()는 추상 메소드이다.　   
따라서, 부모의 Pizza.Build.self()가 호출되는 것이 아니라,　   
이를 재구성한 자식의 NyPizza.Build.self()가 호출되는 것이다.　   
　   
　   
　   
🙂  **"계층 구조"에서 "메서드 연쇄"를 위한 self() method 추가**　   
　   
위에 Pizza서는, 같은 Builder 객체끼리 메서드 연쇄가 일어나므로, 그냥 return this;를 하면 됐다.　   
하지만 현재 abstract Pizza의 addTopping setter에서는 return this를 할 수 없다.　   
우리가 연쇄에 사용하고 싶은 것은 NyPizza나 CalPizza의 객체이지, Pizza의 객체가 아니기 때문이다.　   
(참고로, abstract 추상 클래스도 익명클래스를 이용하여 메소드를 재정의해주면 객체를 만들수는 있다.)　   
　   
따라서, 각 구체클래스의 객체를 반환해주는 protected abstract T self() 를 추가해야 한다.　   
자식(구체)클래스에서는, 이를 Override하여, 자식 자신 객체(this)를 반환해주게 된다.　   
~~~
@Override protected Builder self() { return this; }
 ~~~
 ﻿이제, 다음과 같이 연쇄적 호출을 할 수 있어졌다.
~~~
 NyPizza nyPizza= new NyPizza.Builder(SMALL).addTopping(SAUSAGE).addTopping(ONION).printToppings().build();

Calzone calzone = new Calzone.Builder().addTopping(HAM).sauceInside().build();
 ~~~
 　   
　   
　   
🙂  **추상 클래스, 추상 메소드**　   
　   
추상 클래스도, 생성자를 정의할 수 있다. 아니, 꼭 필요하다.　   
"반드시 자식에서 override 해줘야 하는 method가 있어서 이가 재구성 된 다음에야 이 클래스의 객체를 만들 수 있을 뿐이지, 나머지는 일반 class와 같다고 보면 된다."　   
책의 코드에서는, CalPizza.Builder와 마찬가지로 기본 생성자 public Pizza.Builder() { }가 생략되어, 컴파일러에 의해 자동 생성된다. "부모 Builder 객체는 필수로 필요하다.　   
　   
이 때, Builder class는 static이므로, Pizza의 생성자가 호출되지 않고도 Pizza.Builder() 생성자만 호출된다. ★　   
　   
위의 연쇄적 코드들의 핵심 호출 순서를 정리해보자.　   
![image](https://github.com/inpink/Effective-Java/assets/108166692/c85c9aea-6a50-4f02-9519-f7611135858b)　   
　   
new이므로, 부모 Builder() 생성자 호출 -> 자식 Builder() 생성자 호출, 객체 반환　   
-> 부모 Build의 addTopping() 호출 -> 부모 Build의 addTopping() 호출　   
-> Pizza() 생성자 호출 -> NyPizza() 생성자 호출, 객체 반환　   　   
　   
　   
nPizza를 예시로 보자.　   
　   
- Builder 객체를 먼저 만들고 (부모->자식 순서),　   
- 부모 Builder 객체를 이용하여 addTopping을 연쇄적으로 호출하여 들어간 데이터 SAUSAGE, ONION은,　   
- 부모 Builder 객체의 EnumSet<Topping> toppings에 들어간다.　   
- 마지막에 자식.build()를 하여,　   
- NyPizza의 불변인 nPizza 객체가 된다. (부모Pizza->자식 NyPizza 순서)　   
이 때, Builder객체의 Enum toppings는, 불변 field인 final set<Topping> toppings가 된다. (바로 아래 clone() 참고)　   
 　   
　   
　   
🙂  **clone()**　   
　   
 ~~~
 Pizza(Builder builder) {
        toppings = builder.toppings.clone();
    }
 ~~~
 ﻿
위 코드에서,　   
- 좌측 toppings는, Pizza 객체의 final Set<Topping> toppings;이다.　   
- 우측 builder.toppings는, Pizza.Builder 객체의 EnumSet<Topping> toppings 이다.　   
　   
우측 EnumSet<Topping> toppings는, 자식으로부터 호출받은 addTopping에 의해 값이 차곡차곡 채워진 상태이다.　   
　   
Pizza 객체의 final Set<Topping> toppings에　   
Pizza.Builder의 EnumSet<Topping> toppings의 "얕은 복사본(clone)"을 넣는다.　   
　   
Pizza 객체의 final Set<Topping> toppings에 대한 접근은, "Pizza 생성자"에서만 행해지고 있다.　   
그리고 이는 "final"이다.　   
=> 1) 하나의 객체에서 생성자는 1회만 호출된다. 2) final이기 때문에, 다른 객체로 대체가 불가능하다.　   
=> "불변"이 맞다!　   
　   
☆ clone을 써도 되는 이유　   
: 우측 builder.toppings는, Pizza.Builder에서 만들어준 EnumSet임이 확실하다.　   
신뢰할 수 없는 하위 클래스에서 받은 데이터가 아니다. 즉, clone()을 사용해도 안전하다.　   
추가적인 내용은 (item 50)을 참고하자.　   
　   
　   
🙂  **'얕은 복사'와 '깊은 복사'**　  
 ~~~
 toppings = builder.toppings;  //얕은 복사
toppings = builder.toppings.clone(); //깊은 복사
 ~~~
위 코드는, 얕은 복사와 깊은 복사의 예시이다.　   
　   
얕은 복사 :　   
일반 대입연산자(=)를 이용하면, "참조"까지 같이 복사되어 버린다.　   
toppings와 builder.toppings는 서로 "같은 객체를 바라보고 있는 것"이다.　   
builder.toppings를 바꾸면, toppings에도 같은 변경값이 적용되는 것이다. (애초에 같은 객체를 보고 있으니..)　   
　   
깊은 복사 :　   
clone()은, 단순 "값"만 복사해서 new instance를 생성해주는 메소드이다.　   
"참조"는 복사가 안된다. 이를 "깊은 복사"라고 한다.　   
위 코드에서는, toppings와 builder.toppings은 서로 다른 객체를 가지고 있다. 완전한 별개가 되는 것이다.　   
　   
　   
🙂  **Builder<T extends Builder <T>>와, Builder<?> builder**　   
　   
<T extends Builder <T>>와 <?>는 제네릭이다.　   
(제네릭에 대한 자세한 내용은 아래에)　   
　   
<T extends Builder <T>>는, "T는 Builder<T> 타입의 자식이어야만 한다"라는 뜻이다.　   
<?>는, "? extends Object"의 줄임말이다. unbounded wild card라고도 부른다. "어떤 자료형의 객체도 제네릭 ? 자리에 올 수 있다"라는 뜻이다.　   
=> T에 Builder를 상속받은 "자식 Builder class"을 넣으라고 제한을 걸어준 것이다.　   
　   
도서에 나온 코드에서는,　   
<T extends Builder <T>>대신 <T>를 써도 문제없다.　   
<?>를 생략해도 문제없다.　   
이들을 구현한 NyPizza, Calzone에서, 문제없이 T자리에 자식 Builder class를 넣어주고 있기 때문이다.　   
　   
 　   
 ***
 　   
 ﻿
**장점4. 생성자에서는 사용하지 못하는, "가변인수(varargs)"를 Builder에서는 일반 setter method를 이용하기 때문에 사용할 수 있다.**　   
물론, setter에서 그냥 자료구조(int[], ArrayList, Set 등)를 매개변수로 받아도 된다.　   
(가변인수는 아래에 나옴)　   
　   
**장점5. "API는 시간이 갈수록 기능이 추가되며 확장되는 성질을 가지고 있다"**　   
빌더 패턴은, 본 class의 객체뿐만 아니라, 추가적으로 Builder 객체를 생성하는 비용이 들어간다.　   
따라서, 매개변수의 개수가 작을 때는 유용하지 않을 수 있다.　   
　   
그리고, 일반적으로 Builder 객체를 생성하는 것때문에 문제가 되는 경우는 매우 적지만,　   
성능에 매우 민감한 경우에는 문제가 될 수 있다. (단점이 될 수 있는 부분)　   
　   
하지만, API는 갈수록 확장될 가능성이 크다.　   
따라서, 큰 그림으로 봤을 땐 처음부터 "빌더 패턴"을 이용하는 것이 유리한 경우가 많다.　   
　   
　   
 > 빌더 패턴(3) 정리　   
전체적으로, 빌더 패턴은 매우 큰 "유연성"을 가지고 있다.　   
생성자/정적 팩토리 메서드 에서 매개변수 개수가 많아진다면, "빌더 패턴"이 매우 유용하다.　   


특히, 1. "필수 field"가 많거나, 2. 서로 같은 타입이 많다면 더욱 유용하다.　   
1=>Builder의 생성자로 필수 매개변수를 한 번에 처리해줄 수 있으며,　   
2=>연쇄적 호출/setter 여러번 호출 등을 통해, 하나의 자료구조에 같은 타입의 데이터를 모아줄 수 있기 때문이다. (abstract Pizza, Builder.addTopping 예시 참고)　   
　   
　   
 ***
 　   
　   
 > ﻿객체지향

 　   
 - 생성자　   

ⓐ 자식 class의 객체가 생성될 때, 반드시 부모 class의 객체도 생성되어야 한다. (생성자가 둘 다 호출되어야 한다)

super()이 부모 class의 생성자를 호출하는 역할을 한다.

따라서 자식 class의 생성자에서는, super()가 반드시 포함되어 있어야 한다.

이 때, 매개변수의 개수/타입은, 호출하고 싶은 부모 생성자와 맞춰주면 된다. 둘이 같지 않아도 된다!
　   
　   
ⓑ 생성자 호출 순서는, 부모 클래스가 우선, 계층적으로 내려온다.

사라질 때는 생성 순서의 반대이다. 계층이 가장 낮은 자식 객체가 먼저 사라진다.
　   
　   
 - field

ⓒ부모 객체의 field는, 기본적으로 자식 객체에서 완전히 공유된다.

복사되는 것이 아니라, 자식 객체에서 부모 객체의 field에 접근할 수 있는 것이다.

단, 접근 지정자가 private라면, 자식에서는 접근할 수 없다.
　   
　   
추상 클래스/구체 클래스라고 할지라도 위의 규칙은 지켜진다.

abstract가 붙은 method만 자식 클래스에서 override해서 자식이 스스로 쓰면 되는 것이다.

구체(자식) 객체에서 추상(부모) 객체의 변수를 공유한다.
　   
　   
-ⓓ 각 자식 객체별로, 부모 객체가 새롭게 하나씩 생성된다.﻿
 ![image](https://github.com/inpink/Effective-Java/assets/108166692/5834dda2-d8d7-4e11-b40b-f321cbb02109)
 　   
 ﻿　   
　   
ⓔ static

static이 붙으면, 여러 객체에서 접근가능한 "공유 API(field, method, class..)"가 된다.

"클래스명.API명"으로 접근 가능하다.
　   
　   
여러 클래스에 a라는 멤버가 있지만, Apple.a는 단 하나이다.

Pizza.Builder는 하나이다.

Apple.getApple()은 하나이다.
　   
　   
* 이를 담고 있는 클래스의 생성 유무와 전혀 상관없이 사용이 가능하다.

이를 담고 있는 클래스가 생기지 않아도, Apple.a를 쓸 수 있다는 것이다.
　   
　   
* static 멤버는, load되는 순간 "데이터 영역"에 적재된다.

그래서, 교재 19~21p의 코드에서

new NyPizza.Builder(~~.. )이 호출되는 순간,
　   
　   
Pizza.Builder()->NyPizza.Builder()가 최초 1회씩 호출되어 "데이터 영역"에 적재된다.

이후, new NyPizza.Builder()를 다시 호출해도, "생성자가 호출되지 않고 이미 생성되었던 "싱글턴 객체"가 반환된다."
　   
　   
아래 예시를 참고하자.
~~~
 package item2;

import static item2.NyPizza.Size.SMALL;
import static item2.Pizza.Topping.*;

public class Main {

    public static void main(String[] args) {
        NyPizza nyPizza= new NyPizza.Builder2(SMALL) //static Pizza.Builder() ->  static NyPizza.Builder2() 호출
                .addTopping(SAUSAGE)
                .addTopping(ONION).printToppings().printTest()
                .build();

        System.out.println();

        //NyPizza를 위한 Pizza.Builder()랑 Calzone을 위한 Pizza.Builder()는 별개임
        Calzone calzone = new Calzone.Builder() //static Pizza.Builder() ->  static Calzone.Builder() 호출
                .addTopping(HAM)
                .sauceInside()
                .build();

        new NyPizza.Builder2(SMALL); //static이라 생성자가 다시 호출되지 않고,
        //이미 생성되었던 "싱글턴 객체"가 반환된다.

        new TestC(); //static이 아니므로, TestC()호출,  새로운 TestC 객체가 반환됨 
        new TestC();//static이 아니므로,  TestC()호출, 새로운 TestC 객체가 반환됨 


        nyPizza.printSize();
    }
}
 ~~~
　   
　   
　   
위의 객체지향 특징들을 코드를 통해 보도록 하자.
각 번호(ⓐ~ⓔ)가 관련된 코드에 달려있다.
　   
　   
public abstract class A
~~~
 import java.util.HashSet;
import java.util.Set;

public abstract  class A {

    //ⓒ
    int apple; //자식에게 공유됨
    final Set<Integer> toppings; //자식에게 공유됨

    // protect/default/public setter를 쓰지 않는 이상, 자식이 접근이 불가능하므로 사실상 공유되지 않는다
    private int nope=10;


    protected A(int a) {
        this.toppings = new HashSet<>();
        apple=a;
        System.out.println("부모 생성자 호출"); //ⓑ
    }

    public void printApple(){
        System.out.println("apple A ="+apple);
    }

}
~~~
　   
　   
　   
 ﻿public class A_1 extends A
 ~~~
 public class A_1 extends A {

    static int a=10; //ⓔ static
    protected A_1(int a) {
        super(a); //ⓐ 필수! 부모 클래스의 생성자를 호출.
        // super(a,b); //부모에 이런 매개변수를 가진 생성자가 있다면, 대신 이걸 호출할 수도 있다.
        System.out.println("자식A 생성자 호출"); //ⓑ
    }


    public void getAppleA1(){
        System.out.println("A1 Apple:"+apple);
    }

    public static void main(String[] args) {
        A_1 a1= new A_1(10);
        A_2 a2= new A_2(5);

        //ⓓ a1에 사용되는 부모 객체와, a2에 사용되는 부모 객체가 다르다.
        a1.getAppleA1(); //10
        a1.printApple(); //10
        a2.getAppleA2(); //5
        a2.printApple(); //5

    }
}
 ~~~
 　   
　    
　   
 ﻿public class A_2 extends A
 ~~~
 public class A_2 extends A{

    protected A_2(int a) {
        super(a);

    }

    public void getAppleA2(){
        System.out.println("A2 Apple:"+apple); //ⓒ 부모의 apple 변수에 접근 가능
        //System.out.println(nope); //But, private인 변수는 접근이 불가능하다.
    }

    public static void main(String[] args) {
        A_1.a=10; //ⓔ 데이터 영역에 저장된 static 공유 변수 a에, class명을 이용하여 직접 접근

    }
}

 ~~~
 　   
 　   
 ***
 　   
 　   
> ﻿제네릭(Generic)

~~~
 public class A_1<T> {  ... }   //제네릭 클래스 선언 

A_1 a1= new A_1<Integer>(10);  //제네릭 클래스 사용
 ~~~
 　   
클래스 내부에서 쓸 타입 T에 대해, 내가 타입을 정해주지 않고, 외부에서 입력받아 사용한다.　   
외부에서 T의 실제 타입을 정해서 쓰도록 해줌.　   
　   
제네릭 타입으로는, 참조 타입(Reference Type)만 올 수 있다.　   
즉, int, float, boolean같은 "일반 원시 타입(primitive type)"은 올 수 없다는 것이다.　   
　   
- 제네릭 장점　   
T에 여러 타입이 올 수 있으므로 재사용성, 활용도가 높아진다.　   
제네릭 T에 옳지 않은 타입이 들어오면, "컴파일 시점에" 에러 체크가 가능하다.　   
T자리에 객체가 아니라 int같은 일반 원시 타입이 온다면! COMPILE ERROR　   
NyPizza.Builder2 class에서 T를 Builder2로 정해줬는데, nyPizza를 사용하는 시점에서 Integer i = nyPizza.addTopping(); 으로 Integer로 받는다면! COMPILE ERROR　   
　   
　   
　   
***
　   
　   
 > ﻿가변인자(varargs) 매개변수

﻿var/args로 읽는다. 가변인수라고도 한다.　   
 ~~~
 public static void sum(String...str) //...을 이용하여 가변인수 표현

Class.sum(); //아예 안넣어줄수도
Class.sum("ABC"); //하나만 넣을수도
Class.sum("A","BB","C","kkk","12"); //여러 개 넣을 수도 있다.
~~~

매개변수의 개수가 가변적이다.　   
　   
　   
내부적으로 배열[]을 생성한다.　   
아래와 비슷한 효과를 가진다.　   
~~~
 public static void sum(String[] str) 
 ~~~
 　   
　   
 ﻿가변인자는 매개변수의 끝에 넣어줘야, 컴퓨터가 가변인자의 시작과 끝을 인식 가능하다.﻿
 ~~~
 public static void sum(String...str, int a) (X) 
public static void sum( int a,String...str) (O)
 ~~~
 　   
 　   
 ***
 　   
 　   
 > ﻿자바에서 생성자 없이는 인스턴스를 만들 수 없다.

만약 생성자를 적어주지 않으면,　   
컴파일러가 매개변수와 구현 내용이 없는 기본 public 생성자() { }를 생성해준다.　   
　   
그 클래스의 인스턴스를 만들지 못하게 하려면,　   
private 생성자() { }를 적어두면 된다.　   
(아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라)　   
　  　    
　   
 ***
 　   
 > ﻿불변(immutability)

　   
불변 클래스, 불변 객체 등에서 사용되는 '불변(immutability)'이라는 개념은, 다음을 나타낸다.

"한 번 할당된 값은, 절대 바꾸지 못한다"
　   
　   
예) String, Boolean, Integer 등이 불변 클래스이다.

예) String a="KK"; 를 주고 나면, "KK"를 담은 객체@I324fsOa 는 KK라는 값을 절대 바꾸지 못한다.

하지만, 분명히 우리는 a="Hello";로 값을 바꿔줄 수 있다.

이 때는, 객체@I324fsOa 의 값이 바뀌는 게 아니라, 새로운 불변 객체@912UIS23 에 값이 담겨서 a에 재할당되는 것이다.
　   
　   
그 객체가 불변인 것이지, 재할당이 불가능한 것은 아니다.

객체 재할당이 불가능하려면, final이 있다.
　   
![image](https://github.com/inpink/Effective-Java/assets/108166692/1a6519b8-0031-4fa9-ab6b-15f66abc9ece)
  　   
***
  　   
 > ﻿불변식(invariant)

 
불변(immutability)과 불변식(invariant)은 다른 것이다.
　   
　   
불변식은 프로그래머가 정해준 기간(프로그램 전체일수도 있고, 특정 기간일수도 있다)동안 반드시 만족해야 하는 조건이다.
　   
　   
예) size값은 0 이상의 정수이다. 음수 값이 들어오면 에러를 던져준다.
　   
 　   
***
 　   
 > ﻿불변 vs final

 　   
불변 : "값을 한 번 가진 객체는, 값이 못바뀐다" / "재할당은 가능" / 예) String
　   
　   
final : 원시 타입 변수 / 객체 / 메소드 / 클래스 에 따라 다른 효과를 가진다.　   
　   
- final 원시 타입 변수 : 값이 정해진 뒤, 바꿀 수 없는 "상수"가 됨
~~~
 final int aaa;
aaa=10;
//aaa=5; //원시 타입은 값 자체를 바꾸는거라 ERROR
 ~~~
 　   
 　   
 - final 객체 : 객체 할당된 뒤, "재할당"불가함. "객체 내부 값 변경은 가능하다"
 ~~~
 final int[] alist;
alist=new int[10];
alist[0]=1;
alist[0]=2;
 ~~~
 　   
 　   
 - ﻿final 메소드 : 상속받은 클래스에서 Override를 못하게 금지함
 ~~~
  public class Fruit{
   public final void getFruit() {. .. }
} 

public class Banana extends Fruit{
    //ERROR! @Override public void getFruit() { .. }
     
}
 ~~~
 　   
 　   
 - ﻿final 클래스 : 이 클래스는 상속할 수 없음
 ~~~
 public class Fruit{

} 

public class Banana extends Fruit{ //ERROR!
     
}
 ~~~
 　   
 　   
 　   
 ***
 　   
 ~~~
 public abstract class Pizza{
    final Set<Topping> toppings;

    abstract static class Builder<T> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
    }

    Pizza(Builder builder) {
        toppings = builder.toppings.clone();
    }
}

~~~
 ﻿
위 코드에서,　   
　   
　   
- Pizza의 Set<Topping> toppings는 "Pizza 생성자"에서만 생성되고, 접근되고 있다.　   
=> 생성자는 최초 1회만 호출되기 때문에, toppings는 "불변"이다.　   
　   
　   

- 그리고 "final"이다.　   
=> 할당된 이후, 객체 재할당 불가　   
　   
　   
　   
- 만약 아래 코드가 Pizza의 method로 추가된다면?
~~~
 public void delTopping(Topping topping){
        toppings.remove(topping);
    }
 ~~~

final 객체은, 객체 재할당이 불가능하다는 것이지. set.add(), set.remove()처럼 내부 값을 바꾸는 건 가능하다고 했다.　   
=> 위 코드는 문제없다. 외부에서 delTopping(topping) 호출, 실행도 가능하다.　   
=> But, 위 코드때문에, "불변"이 깨진다.　   
　   
 　   
 ***
 　   
 　   
 > ﻿item2를 마치며, 소감
 
![image](https://github.com/inpink/Effective-Java/assets/108166692/e2f48c01-e0f5-4d9d-afc0-d4b297664e5d)
　   
Effective Java는 한 문장 한 문장 중요한 것들이 압축되어 있다.　   
무심한 듯, 당연한 듯 던져진 문장들이지만　   
내가 자바에서 모르던 부분들이 얼마나 많았는지 깨닫게 해준다.　   
　   
한 문장, 한 줄의 코드라도 대충 넘기지 않고 공부하려하고 있다.　   
그러다보니 4장의 도서 포스팅이 이렇게 길어져버렸다.　   
　   
만약, 지금 대충 넘어간다면, 어차피 언젠간 다시 만나게 될 문제라고 생각한다.　   
지금은 시간이 많이 걸리지만, 꼼꼼히 공부하다보면 뒤로 갈수록 모르던 부분이 적어지기에 속도도 붙을 것이라 믿고 있다.　   
이렇게 한 문장씩 깊게 파는 것도 하다보니 맛들리는 것 같다.　   
