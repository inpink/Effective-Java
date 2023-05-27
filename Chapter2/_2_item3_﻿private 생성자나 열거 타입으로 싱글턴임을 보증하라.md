> # Effective-Java
🔖 CS Books - effective java(joshua bloch) learning and summarizing   
📗 effective java  ( joshua bloch )   
**⭐ https://blog.naver.com/willyouspeedup/223111617222 네이버 블로그**
　   
 ***    
 　     
>  싱글턴(singleton)   💬   

 　      　    
- 싱글턴: 인스턴스를 단 1개만 만들 수 있는 클래스 　     
사용 예: 무상태(stateless) 객체, 시스템 전체에서 1개만 생겨야 하는 Component 　     
 　     
 　     
- 무상태(stateless) 객체 　     
 　     
: stateful한 field가 없는 객체를 의미한다. 　     
: 변경될 수 있는 field가 없는 객체를 의미한다. 　     
: 싱글턴은 반드시 무상태(stateless)여야 한다. 　     
 　     
아래의 2개 코드는 stateless하다. 　     
~~~
class Stateless {

    void test() {
        ~~
    }
}
~~~
~~~
class Stateless {

    final String TEST = "Test!";

    void test() {
        ~~
    }
}
~~~
TEST field는 상수이다. 　     
Stateless의 instance가 생성될 때 생성된 이후, 절대 바뀌지 못한다. 　     
 　     
위의 두 코드에는, 객체가 생성된 이후 값이 바뀔 가능성이 있는 field가 없다. 　     
이를 무상태(stateless)라고 한다. 　     
 　     
 　     
- 싱글턴의 문제점 　     
전체 프로그램을 통틀어 1개밖에 없기 때문에, 　     
싱글턴 클래스의 가짜(mock) 객체를 만들 수 없다. 　     
따라서, 싱글턴 클래스는 테스트가 어렵다. 　     
 　     
 　     
- 해결 방법 　     
"인터페이스"를 이용한다. 　     
이를 구현하는 진짜 싱글톤 클래스/가짜(mock) 클래스 2개를 만든다. 　     
(아래에 자세히 나옴) 　     
 　     
 　     
 ***
  　     
         　     
> ﻿interface를 이용하여 singleton class, mock class

- interface member field

인터페이스 멤버 변수는, 반드시 public static final이다. 　     
아래와 같이 public static final은 생략이 가능하다. 　     
~~~
public interface TestInterface {

    int a = 0;

}
~~~
 　     
        　     
interface member method

인터페이스 멤버 함수는, 반드시 public abstract final이다.

마찬가지로, 아래와 같이 생략 가능하다.

﻿
