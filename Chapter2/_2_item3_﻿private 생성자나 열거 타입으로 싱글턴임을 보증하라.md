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
 　     
        　     
- interface member method

인터페이스 멤버 함수는, 반드시 public abstract final이다. 　     
마찬가지로, 아래와 같이 생략 가능하다. 　     
~~~
public interface TestInterface {

    int a = 0;

    void printMe(String s);
}
~~~
 　     
 　 　          
 　     
- interface를 이용하여 singleton의 문제를 해결해보자

singleton은 mock instance를 만들 수 없기에, 클라이언트에서 테스트가 어렵다는 문제가 있었다. 　     
아래 코드처럼, singleton interface를 만들고, 　     
이를 구현하는 2개의 class를 이용하면 가짜(mock) class를 이용해서 Test를 할 수 있다. 　     
interface에서 정의되는 field는 private static final이므로, 무상태(stateless)이다. 싱글톤이 가능하다. 　     
~~~
interface Singleton {
    private static final myInstance;
    ~~
}

class RealSingleton implements Singleton {
    ~~
}

class FakeSingleton implements Singleton {
    ~~
}

class ClientTest {
    private Singleton testSingleton = new FakeSingleton();

    @Test
    public void test() {
        ~~
    }
}
~~~ 
 　     
***
 　     
> ﻿singleton 만들기 ① 

 　     
- public static final field를 이용하는 방법이다. 　     

생성자는 private, 유일한 인스턴스는 public static final field
 　     
 　     
1) 생성자를 private로 　     
: 외부에서 접근할 수 없도록 막는다. 　     
 　     
 　     
2) public static final field를 만든다. 　     

: Type은, "이 클래스의 객체"이다.

: new Class();로 instance를 만들어낸다.

: final이므로, 처음 instance가 할당된 이후, 다른 객체로 바뀌지 못한다. "무상태"이다.

: static이므로, 외부에서는 "Class명.INSTANCE"로 접근하여 "싱글턴 객체"를 받아올 수 있다.

: 이 instance는, "Class명.INSTANCE"가 최초로 호출될 때, private 생성자()를 호출한 뒤 생성된다.

(즉, private 생성자()는, 이 static field가 최초로 호출될 때 단 1회 실행된다.)
 　     
~~~
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();

	private Elvis() {
	}

	public void leaveTheBuilding() {
		System.out.println("Whoa baby, I'm outta here!");
	}

	// psvm 코드는 보통 외부에 따로 구현합니다. 
	public static void main(String[] args) {
		Elvis elvis = Elvis.INSTANCE;
		elvis.leaveTheBuilding();
	}
}
~~~
 　     
        　     
- 예외 　     

"리플렉션(Reflection, item 65)"을 이용하면, 　     
private 생성자를 호출할 수 있다. 　     
아래 코드를 참고하자. 　     
~~~
Elvis.setAccessible(true);
Elvis.newInstance();
~~~

- 해결 방법 　     

private 생성자 내부에 방어적인 코드를 추가하면 된다. 　      　     
구현 방법은 여러 가지가 있겠다. 　     
아래 코드를 참고하자. 객체가 2번 이상 호출되려 하면(=생성자가 호출되면), 예외를 던져준다. 　    
~~~
private Elvis() {
   throw new AssertionError("Suppress default constructor for noninstantiability");
}
~~~
 　     
 　     
- 장점 　     

1) 코드 한 줄(API)에 "싱글턴"임이 명확히 들어난다. 　     
2) 코드 한 줄이면 되므로, "간결하다" 　     
 　     
﻿ 　     
 　     
***
 　     
        　     
               　     
> ﻿singleton 만들기 ②

 　     
        　     
- ﻿public static final method를 이용하는 방법이다.
﻿생성자는 private, 유일한 인스턴스는 private static final field, 받아오기는 public static method(getInstance)
①번과 유사하지만, 외부에서 field에 직접 접근하는 것이 아니라, method를 통해 얻어온다.
 　     
 　     
       ﻿
1) 생성자를 private로

: 외부에서 접근할 수 없도록 막는다.
 　     
 　     
2) private static final field를 만든다.

: 외부에서 직접 접근할 수 없도록 막는다.

: 나머지는 ①번과 유사하므로 설명 PASS
 　     
 　     
3) public static method로 해당 field를 받아갈 수 있다.
~~~
public class Elvis {
	private static final Elvis INSTANCE = new Elvis(); //private field

	private Elvis() {
	}

	public static Elvis getInstance() { //method로 반환 
		return INSTANCE;
	}

	public void leaveTheBuilding() {
		System.out.println("Whoa baby, I'm outta here!");
	}

	// psvm 코드는 보통 외부에 따로 구현합니다. 
	public static void main(String[] args) {
		Elvis elvis = Elvis.getInstance();
		elvis.leaveTheBuilding();
	}
 ~~~
  　     
  　     
 ﻿ 　     
- 장점

1) API를 바꾸지 않고도 싱글턴이 아니게 바꿀 수 있다. getInstacne에서, return new Elvis()를 하면 된다.

2) 반환 method를 제네릭 싱글턴 팩터리(generic singleton factory, item 30)로 쓸 수 있다. 아래 예시를 참고하자.

3) 정적 팩터리의 "메서드 참조"를 "공급자"로 사용할 수 있다.(item 43,44) 아래 예시를 참고하자.
 　     
 　     
 　     
- 제네릭 싱글턴 팩터리(generic singleton factory) 예시 　     

제네릭 싱글턴 팩터리를 이용하여, 같은 자료구조 SET에 서로 다른 TYPE의 데이터들을 넣어줬다. 　     
java.collections는 기본적으로 서로 다른 데이터 타입을 담을 수 있다. 　     
 　     
EMPTY_SET는, 제네릭 싱글턴 팩터리 메소드에서 적용되는 type에 따라 다른 type을 가진다. 　     
하지만 singleton이기에, 여러 type의 참조를 만들어줘도, 단 하나의 singleton 객체 EMPTY_SET를 함께 쓰는 것이다. 　     
~~~
public class Singleton{

    private static final Set<Object> EMPTY_SET = new HashSet(); //field
     
    private Singleton() { } 

    public static final <T> Set<T> emptySet() { //factory method
        return (Set<T>) EMPTY_SET;
    }

    public static void main(String[] args) {
        Set<Integer> set= blue0.emptySet(); //제네릭으로 Integer 보내줌. Set<Integer> EMPTY_SET 받음
        set.add(0); //set로는 integer형을 넣어줄 수 있다
        System.out.println(set); //[0]

        Set<String> set2= blue0.emptySet(); //제네릭으로 String 보내줌.  Set<String> EMPTY_SET 받음
        set2.add("kk"); //set2로는 integer형을 넣어줄 수 있다  [0,"kk"]
        set.add(5); // [0,"kk",5]
        
        //★set, set2는 제네릭 type만 변환되었지, "같은 EMPTY_SET 싱글톤 객체"를 받아쓰고 있다. 
        //따라서, 같은 객체
        System.out.println(set); // [0,"kk",5]
        System.out.println(set2); // [0,"kk",5]


    }
}

~~~
 　     
 　     
 　     
 　     ﻿
- 공급자 Supplier

😀 아래는 Supplier<T> interface의 원문 코드이다. 　     
구현 메소드로 단 하나, T get()이 존재한다. 　     
=> 매개변수 없이, 단순히 "무언가"를 반환하는 역할을 한다. 　     
~~~
 @FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
 ~~~'
  　     
   　     　     
  　     ﻿
😀 Supplier을 이용한 대표적인 예시는 "Lazy Evaluation(느린 연산)"이다.
segment tree lazy propagation와 비슷하다.
일반적으로 아래와 같이, 메서드를 적어주면 바로 호출되어 버린다.
아래는 Elivs.getInstance();가 3번 즉시 호출된다. return값이 e에 담긴다.
~~~
 test(1,Elivs.getInstance()) 
test(2,Elivs.getInstance())
test(3,Elivs.getInstance())

void test(int n, Elvis e){
  ~~~
}
 ~~~
  　     
  　     
 ﻿ 　     
하지만 나는 특정 시점에서만 메서드가 호출되길 원한다. 　     
가령, test 매개변수 값이 1일 때만 Elivs.getInstance()를 호출시키고 싶다. 　     
우리는 Supplier s에 Elivs.getInstance()라는 "메소드"를 담는다. 즉시 실행되지 않는다. 　     
s.get()이 실행 되었을때가 돼서야, Supplier s에 담겨있는 Elivs.getInstance()가 실행된다. 　     
 ~~~
 test(1,()-> Elvis.getInstance()) 
test(2,()-> Elvis.getInstance())
test(3,()-> Elvis.getInstance())

void test(int n, Supplier<Elvis> s){
 if (n==1) s.get();
}
 ~~~
 　     
﻿ 　   　        
 　     
😀 Supplier에 "무언가"를 담아주는 방법에는 크게 1)람다 2) 메서드 참조가 있다. 　     
그 중에, 위의 방식은 "람다"를 이용한 방식이다. 　     
"메서드 참조"를 보자. 　     
우리 코드에서 사용한다면 아래와 같이 사용할 수 있다. 　     
supplier.get()으로 singleton INSTANCE를 받아온다. 　     
 ~~~
 public static void main(String[] args) {
    Supplier<Elvis> supplier = Elvis::getInstance;
    supplier.get()
}
 ~~~
 　     
﻿
