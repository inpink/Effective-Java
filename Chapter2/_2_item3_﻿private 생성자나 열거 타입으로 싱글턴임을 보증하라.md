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
전체 프
로그램을 통틀어 1개밖에 없기 때문에, 　     
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
 ~~~
  　     
   　     　     

😀 Supplier을 이용한 대표적인 예시는 "Lazy Evaluation(느린 연산)"이다.
segment tree lazy propagation와 비슷하다.
일반적으로 아래와 같이, 메서드를 적어주면 바로 호출되어 버린다.
아래는 Elivs.getInstance();가 3번 즉시 호출된다. return값이 e에 담긴다.
~~~
 test(1,Elivs.getInstance()) 
test(2,Elivs.getInstance())
test(3,Elivs.getInstance())

void test(int n, Elvis e){
  ~~
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
 　     
 　     
- ①public static final field vs ②public static method 비교

1) 각각의 장점을 고려해봤을 때, ②method의 장점이 굳이 필요한 게 아니라면 일반적으로 ①field 방식이 더 좋다

2) "직렬화"를 해야 한다면, 아래의 방법을 써야 한다.
 　     
 　     
 　     
- 직렬화

Java Data -> Byte Stream 으로 "변환"하는 것.

자바의 data를 자바 외부에서도 쓸 수 있게 하기 위함이다.

Stream of Bytes는, 연속적(serial)이라는 특징이 있다.
 　     
 　     
다음과 같이 Serializable interface를 구현해서 직렬화시킬 수 있다. (도서 12장도 참고하자)

~~~
	public class User implements Serializable {

    public static void main(String[] args)  throws  Exception  {
        //직렬화할 객체 user
        User user=new User();

        //직렬화 데이터 저장할 파일 path
        String filePath = "/User/~~/~~.md";

        //Java Data -> Stream of Bytes
        try (
                //두 가지 class 모두 필요하다
                FileOutputStream fos = new FileOutputStream(filePath); //해당 File에 Stream of Bytes 작성하기 위해 사용
                ObjectOutputStream out = new ObjectOutputStream(fos) //객체를 직렬화하는데 사용 
        ){
            out.writeObject(user); //해당 file에 user 객체를 직렬화하여 적기

        } catch (IOException e) { //예외 발생 시 
            e.printStackTrace();
        }
    }
}
~~~
 　     
 　     	

- 역직렬화

Byte Stream -> Java Data

이 때, byte stream으로부터 "새로운 객체"가 생성되게 된다.

"싱글턴"을 유지하기 위해서는, "원래 쓰던 싱글턴 객체"로 변환되도록 해야 한다.

싱글턴인 경우에, 아무 것도 해주지 않고 그냥 직렬화만 한다면, 역직렬화 과정에서 "싱글턴이 깨지게 된다"

=> 아래 방법을 사용하자.
 　     
 　     
- 싱글턴 클래스 직렬화(serialize)

1) Serializable을 구현한다.

2) Instance를 반환하는 field는, transient(일시적)이여야 한다.

아래와 같이 transient 키워드를 붙여서, "직렬화 하지 않을 field임"을 명시한다.
~~~
public transient int numb = 100;
~~~
﻿
하지만, final과 static 키워드가 붙은 field는, transient를 붙여도 직렬화를 막지 못한다.


3)그래서, private Object readResolve() method를 추가한다.

이는, "역직렬화"시 자동으로 호출된다.

원래의 싱글턴 INSTACNE를 반환하고, "역직렬화된 새로운 객체는 가비지 컬렉터(Garbage Collector)가 삭제하게 된다"
~~~
	private Object readResolve() {
		return INSTANCE;
	}
~~~
	 　     
﻿ 　     
- 가비지 컬렉터(Garbage Collector, Garbage Collection)

JVM의 Heap 영역에서, 참조하는 변수가 하나도 없는 객체를 주기적으로 탐색하여 삭제한다.
 　     
 　     
***
 　     	
 　     	
> ﻿singleton 만들기 ③

 　     
- ﻿Enum Type(열거형) 이용
	
: abstract Enum class로부터 구현된다.

: 서로 관련있는 상수들이 모여서 하나의 Enum형을 이룬다.

: 상수, 메서드를 선언할 수 있다.

: 다른 언어에서와 다르게, 자바의 Enum에서는 더 많은 기능을 제공한다.

: 그럼에도 매우 간단하고, 강력하다.

: 아래와 같은 기능들도 제공한다.

﻿![image](https://github.com/inpink/Effective-Java/assets/108166692/361e3876-2311-42c9-84a7-d8321b55af77)

﻿

아래는 Enum Fruits 예시이다.

APPLE, BANANA, ORANGE 3개의 상수가 있으며, 1개의 method가 있다. 물론, 호출 가능하다.

~~~
public enum Fruits {
    APPLE, BANANA, ORANGE;

    public void printAnnounce(Fruits fruits) {
        System.out.println("나는 과일"+fruits);
    }
}
~~~
~~~
public static void main(String[] args) {
        Fruits fruits=Fruits.APPLE;

        fruits.printAnnounce(Fruits.APPLE);
        fruits.printAnnounce(Fruits.BANANA);
        fruits.printAnnounce(Fruits.ORANGE);
    }
~~~
﻿자세한 Enum 메서드 호출 방법은, 아래를 참고하자.
	
	
- Enum Type을 이용한 singleton

: Enum Type에서 상수(원소)가 하나라면, 싱글턴이다.

: 자바에서 Enum type을 어떻게 사용하고 있는지 이해하면 "Enum Type을 이용한 singleton"의 이해가 더욱 쉽다.
 　     
 　     
1) Enum은 사실 abstract Enum Class를 구현한 "특수한 Type"이다.

2) Enum class는 private 생성자 호출이 불가능하다. "리플렉션으로도 불가능하다" (=리플렉션 공격으로부터도 안전하다)

3) Enum의 상수는, 직렬화/역직렬화 상황에서 추가적인 작업 없이도 싱글톤을 유지해준다.

(Enum 내부의 일반 변수는 아예 "직렬화가 되지 않는다")

4) 대부분의 상황에서는, Enum을 이용하는 것이 싱글턴을 만드는 매우 좋은 방법이다.

5) But, Enum은 상속을 지원하지 않는다(extends 불가). Interface를 구현하는 것은 가능하다.
![image](https://github.com/inpink/Effective-Java/assets/108166692/3d4e7348-e4a2-47b3-a4be-ffa6cb5fbcbb)
	
	
	
~~~
public enum Color {
RED, GREEN;
}
~~~
﻿
위 Enum 코드는, 아래 코드(①public field 방식 싱글턴)와 "거의 유사하다"
 　     
 　     
static이라는 점을 잊지 말자.

Color.RED, ColorGREEN 이 각각 최초 1회씩 호출될 때,

각각 new Color() 생성자가 호출되며, "데이터 영역"에 적재된다.

이후, Color.RED, ColorGREEN를 다시 호출해도, " new Color() 생성자가 호출되지 않고 이미 생성되었던 "싱글턴 객체"가 반환된다."
~~~
public class Color {
    public static final Color RED = new Color();
    public static final Color ﻿GREEN = new Color();
    private Color() { } 

}
~~~
﻿
=> 그렇기에, "상수를 하나만"두면, 그 Enum은 자연스럽게 Singleton이 되는 것이다.
 　     
 　     
-﻿사실 Enum 상수는
![image](https://github.com/inpink/Effective-Java/assets/108166692/99bf9b63-bf89-4455-97d6-bf8c259b0937)
﻿
Elvis Enum class의 instance를 반환하는 Method 형태이다.

그러니, 엄연히 따져보면 ①public field 방식보단 ②public method 방식에 더 가까울 것이다.

아래 코드를 보자.

RED와, GREEN은 싱글톤이다.
~~~
public class Color {
    private static final Color RED = new Color(); //static이라 한 번 호출된 이후에 다시 생성자 호출 X
    private static final Color ﻿GREEN = new Color();

    private Color() { } 

    public static Color RED() { return RED; } //Color의 instance인 싱글톤 객체 RED 반환 
    public static Color ﻿GREEN() { return ﻿GREEN; }
}
~~~
﻿
enum이 사실 class라는 것, 그리고 enum 상수는 사실 팩터리 메소드라는 것을 알고 아래 코드를 보면,

코드가 술술 읽힐 것이다.
 　     
 　     
- Enum 사용 방법

Enum Elvis는 상수가 2개이므로 싱글톤이 아니다.

INSTANCE, TEST 각각 싱글톤 객체가 생겨버려서, Elvis의 인스턴스가 1개가 아니기 때문이다.

Elvis가 싱글턴이어야 하는거지, 싱글턴 class에서 싱글톤 객체가 아닌 변수를 사용하지 못하는 것이 아니다. (int a)
~~~
public enum Elvis {
    INSTANCE, TEST;

    private int a; //Elvis가 싱글턴이면 되는거지, 변수 사용을 못하는게 아님

    public int getA() {
        return a;
    }

    public void setA(int a) {
        this.a = a;
    }

    public void leaveTheBuilding() {
        System.out.println("Whoa baby, I'm outta here!");
    }
}
~~~
	
~~~
public class Main
{
    public static void main(String[] args) {

        //enum은 생성자가 private => 외부에서 객체 생성 불가, 리플렉션으로부터도 안전하다.
        //Elvis e = new Elvis(); //불가능

        //아래와 같이 단 하나만 존재하는 상수를 가져와서 씀.
        //상수라곤 하지만, 사실 Elvis INSTANCE()임.
        //같은 상수가 반환하는 것은, "같은 싱글톤 객체"이다.
        Elvis elvis=Elvis.INSTANCE;
        System.out.println(elvis); //Elvis Instance반환, ★ 객체 이름은 "INSTANCE"

        //enum 내부에 있는 method는 아래와같이, "상수에 직접" 사용해줄 수 있음
        //Elvis 객체를 이용해서 method를 사용해줄 수도 있다.
        //INSTANCE=Instance()=Elvis싱글톤객체  라는 것을 생각하면 이해가 쉬움 
        Elvis.INSTANCE.leaveTheBuilding();
        elvis.leaveTheBuilding();

        //싱글턴이 맞을까?
        elvis.setA(5); 

        Elvis elvis1=Elvis.TEST;
        elvis1.setA(10);

        System.out.println(elvis.getA()); //5
        System.out.println(elvis1.getA()); //10

        Elvis elvis3=Elvis.INSTANCE; //같은 INSTANCE를 쓰는 elvis1와 elvis3은 "같은 객체를 참조하고 있다"
        System.out.println(elvis3.getA()); //5


    }
}
~~~
 　     
👀 상수를 통해 싱글톤 객체 반환

Elvis elvis=Elvis.INSTANCE; 　     
 　     
 　     
👀 싱글톤 객체 출력 시, 객체 이름은 Enum 상수이다.

System.out.println(elvis); => INSTANCE 출력 　     
 　     
 　     
👀 Enum method 사용

Elvis.INSTANCE.leaveTheBuilding(); / elvis.leaveTheBuilding(); 　     
 　     
 　     
👀 같은 INSTANCE를 쓰는 elvis1와 elvis3은 "같은 객체를 참조하고 있다"

Elvis elvis=Elvis.INSTANCE;

Elvis elvis3=Elvis.INSTANCE; 　     

 　     
	 　     
	 　     
***
	
> ﻿참고 문헌
	
﻿https://stackoverflow.com/questions/9735601/what-is-stateless-object-in-java (﻿stateless object) 　     
﻿https://stackoverflow.com/questions/2302179/mocking-a-singleton-class/2302211#2302211?newreg=d7ceee7a805644f6aa5eba9add068fda (﻿mocking a singleton class using interface) 　     
﻿https://stackoverflow.com/questions/398953/what-is-the-preferred-throwable-to-use-in-a-private-utility-class-constructor (﻿throwing Exception when private constructor is called by using reflection) 　     
﻿https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0 (﻿serializable) 　     
﻿https://limkydev.tistory.com/66 (﻿Enum)  　     	 　     
﻿https://github.com/marhan/effective-java-examples/tree/master (﻿Effective Java Source) 　     
