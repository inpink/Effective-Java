> # Effective-Java
🔖 CS Books - effective java(joshua bloch) learning and summarizing   
📗 effective java  ( joshua bloch )   
**⭐ https://blog.naver.com/willyouspeedup/223096929862 네이버 블로그**
　   
 ***    
 　     
>  2장 주제 요약   💬   
   
1. 객체를 만들어야 할 때와 만들지 않아야할 때   
2. 올바른 객체 생성 방법   
3. 불필요한 객체 생성을 피하는 방법   
4. 제때 객체가 파괴됨을 보장할 수 있는 방법   
5. 객체가 파괴되기 전에 해야 할 정리 작업들   
　   
　   
    　  
>  생성자 vs 정적 팩터리 메서드   💬   

- public 생성자 

 
우리가 대부분의 라이브러리를 사용할 때, 라이브러리 내의 클래스의 인스턴스를 만들 때를 생각해보자.

~~~
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));   
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));   
~~~
다음과 같이 new, "생성자"를 이용하여    

BufferedReader과 BufferedWriter의 instance를 반환받는다.    

이 것이 전통적인 방법이다.
　   
　   
    　  
>  정적 팩터리 메서드(static factory method)  💬 

하지만, 객체지향 관점에서 알아두고 잘 써야할 한 가지 방법이 더 있다. 정적 팩터리 메서드이다.   

정적 팩터리 메서드는, 생성자처럼 "클래스의 인스턴스를 생성하는 역할"을 한다는 점에선 동일하지만, 사용 방법과 특징이 완전히 다르다.   

　    
아래 코드는 실제로 boolean 기본 타입의 "박싱 클래스(boxed class)" Boolean class 의 일부이다.   

아래 코드가 바로 정적 팩터리 메서드이다.    

Boolean에게서 Boolean.TRUE와 Boolean.FALSE를 반환받기 위해,    

new Boolean()이 아니라 Boolean.valueOf(true)를 사용하는 것이다.    

~~~
public static Boolean valueOf(boolean b) {
 return b? Boolean.TRUE : Boolean.FALSE;
}
~~~
　   
　   
    　  
> boolean이 있는데 Boolean class를 왜 만들고 사용할까?  💬   

기본 타입인 boolean, int 등은 객체로 사용될 수 없다.    

따라서, 박싱 클래스를 통해 Booelean, Integer 등의 래퍼(Wrapper) 클래스를 만들어둔 것이다.    

~~~
PriorityQueue<int[]> heapq = new PriorityQueue<>(
				(o1, o2) -> o1[0] == o2[0] ? o1[1] - o2[1] : o1[0] - o2[0]);
~~~        
PriorityQueue의 < > (제네릭,Generics) 안에는 기본 타입인 int는 들어갈 수 없다.  

참조 타입인 Integer나, 객체인 int[]이 들어갈 수 있다.    
　   
　   
    　  
***
　   
　   
    
> 정적 팩터리 메소드(static factory method)의 4가지 장점  💬  

1. 메소드 이름을 가질 수 있다    

2. 생성자와 다르게, 호출한다고 해서 매번 꼭 해당 클래스의 인스턴스가 생성되는 것이 아니다. ( 싱글톤 등)    

3. 반환 타입의 활용성이 다양하다. 반환 타입에 인터페이스를 적을 수 있으며, 반환값은 반환 타입의 자식일 수도 있다. 입력값에 따라 다른 자식 타입을 반환해줄 수도 있다.    

4. ☆정적 팩터리 메서드를 작성하는 시점에서는, "반환할 객체의 클래스가 존재하지 않아도 된다"    
　   
　   
    
> 1. 메소드 이름을 가질 수 있다  💬 

~~~
BigInteger(int a, int b, Random r)
~~~
~~~
BigInteger.probablePrime(int certainty)
~~~

위는 생성자, 아래는 정적 팩터리 메소드를 이용한 코드이다.    

딱 봤을 때, 아래 코드는 "BigInteger가 소수인지 아닌지 certainty확실한 정도에 따라 판단한다"라는 것을 유추할 수 있다.    

위의 코드는 이름만 봤을 땐 무엇을 하는지 알기가 거의 불가능하다.    

또, 만약 다양한 기능을 위해 BigInteger 생성자를 다음과 같이 매개변수만 다르게 여러 개 둔다고 해보자.    

개발자가 실수하기도 쉽고, 사용자도 이를 외우고 사용하기 어렵다.    
~~~
BigInteger(int a, int b, Random r)
~~~
~~~
BigInteger(int a, int b)
~~~
~~~
BigInteger(float f)
~~~
　   
　   
    
> 2. 생성자와 다르게, 호출한다고 해서 매번 꼭 해당 클래스의  인스턴스가 생성되는 것이 아니다.   💬

생성자를 호출하기 위해선 new 클래스명()이 필요하다. 새로운 인스턴스를 생성시키는 것이다.    

하지만 정적 팩터리 매서드는, 다음과 같이 해당 클래스의 인스턴스를 미리 만들어놓고 반환해줄 수 있다.    

매번 새롭게 객체를 만들지 않아도 된다. 불필요한 객체 생성을 막을 수 있다.     
~~~
public class Fruit {
     private static  Fruit fruit= new Fruit();

     public static Fruit getInstance() {
        return fruit;
    }
}
~~~
   
생성 비용이 큰 객체가 자주 호출되는 상황에서 사용한다면, 성능 향상을 이끌어낼 수 있다.     

싱글톤 패턴, 플라이웨이트 패턴, 인스턴스 통제(instance-controlled) 클래스, 불변 클래스(immutable class; 아이템 17) 등에서 사용되는 방식이다.     

열거 타입인 enum은, 인스턴스 통제(instance-controlled) 클래스의 일종으로, 해당 type의 인스턴스가 단 하나만 사용됨을 보장한다.      

~~~
public enum Grade { //회원 등급 2가지 BASIC, VIP를 enum형으로 만듦
    BASIC,
    VIP
}
~~~

　   
　   
    
> 3. 반환 타입의 활용성이 다양하다.    💬

반환 타입에 인터페이스를 적을 수 있으며, 반환값은 반환 타입의 자식일 수도 있다. 입력값에 따라 다른 자식 타입을 반환해줄 수도 있다.     

=> 이는, 반환 타입의 "클래스"를 자유롭게 선택할 수 있는 큰 "유연성"을 가진다.     


인터페이스 기반 프레임워크(아이템 20)은  반환 타입에 인터페이스를 써서, 인터페이스를 구현한 "클래스"를 외부에 공개하지 않는다.      

따라서 API에는 인터페이스만 공개되기에 API 규모가 매우 작아질 수 있다.      

예시로는 EnumSet 클래스(아이템 36)이 있다.     

아래 코드는 3번의 예시이다.     

~~~
public class Fruit implements Food{
     private static  Fruit fruit= new Fruit();

     public static Food getInstance(String name) {
        if (name.equals("Fruit"))
            return fruit;
    }
}
~~~
Food(인터페이스) -> Fruit(클래스) -> fruit(객체)      

getInstance 정적 팩토리 메서드는, 반환 타입으로 "인터페이스"를 사용하고 있다.      
　   
　   
    
    
> 4. ☆정적 팩터리 메서드를 작성하는 시점에서는, "반환할 객체의 클래스가 존재하지 않아도 된다"    💬

이번 포스팅에서 가장 중요한 부분이다.     

아래 코드를 보자.     

Apples 인터페이스가 있고, 이 인터페이스를 구현한 RedApple 클래스가 있다고 해보자.     

~~~
public interface Apples{
    //구현해야 할 메소드
}
~~~
~~~
public class RedApple implements Apples{
    //구현되어야 할 메소드 
}
~~~
   
Fruit의 정적 팩터리 메서드인 fruits()는, ArrayList<Apples>()를 반환하고 있다.   

Apples는 인터페이스다. 이 자리에 RedApple Class가 들어갈 수도 있고, BlueApple class가 들어갈 수 있다.   

그리고, 우리는 지금 여기에 들어갈 모든 RedApple, BlueApple, GreenApple 클래스를 만들어두지 않았음에도,   

List<Apples>라는 타입을 작성할 수 있는 것이다.    
  
~~~
  public class Fruit{

     public static List<Apples> fruits(){
          return new ArrayList<>();
   }
}
  ~~~
   
   
=>★이는, "서비스 제공자 프레임워크(service provider framework)"의 가장 중요한 특징이 된다.   

service provider framework는 따로 공부를 해야 할 분량이기에 포스팅을 나누었다.    


아래 URL을 꼭 참고해주길 바란다!   

**https://github.com/inpink/Effective-Java/blob/235fce995221fb40025301bfb22bf0d919308f8e/Chapter2/ServiceProviderFramework.md**
	   
   
	
***
　   
　   
    
> 정적 팩터리 메소드(static factory method)의 2가지 단점  💬  

1) 클래스를 상속하기 위해선 public인 protected가 붙은 "생성자"가 필요하다. 정적 팩터리 메소드만 제공하면, "하위 클래스"를 만들 수 없다.

상속보다 컴포지션을 사용(item 18), 불변 타입을 사용(item 17)이라는 관점에서는 장점일 수도 있다

~~~
public class Fruit{
    
   //생성자가 없으면 아래 Apple class는 Fruit를 상속받을 수 없다
   public Fruit() {
          //코드
   }
   
}

public class Apple extends Fruit{
    
}

~~~
﻿
Fruit class에 생성자가 없으면 아래 Apple class는 Fruit를 상속받을 수 없다.

하위 클래스의 인스턴스가 생성될 때, 상위 클래스의 생성자가 먼저 호출되고, 하위 클래스의 생성자가 호출되기 때문이다.
　   
~~~
public class Fruit{
   public Fruit() { //1번
          
   }
   
}

public class Apple extends Fruit{
    public Apple() { //2번
          
   }
   public static int countApple(){ //3번
      //코드
   }
}

public class Main{
     public static void main(String[] args) {
	       Apple a=new Apple();
	       a.countApple(); 
	}
}
~~~
　   
﻿
위의 코드를 보면, Apple class의 인스턴스인 a를 생성했다. 그리고 a를 이용하여 Apple class의 method를 사용했다.

실행 순서는, ⓐ Fruit 부모 클래스 생성자 호출 -> ⓑ Apple 자식 클래스 생성자 호출 -> ⓒ Apple 일반 메소드 호출이다.
　   
　   
    
﻿

2) 정적 팩터리 메서드는 프로그래머가 "찾기 어렵다"

- 생성자처럼 명확한 생성 이름이 있는 것이 아니기 때문이다.

- 정적 팩터리 메서드를 만든 개발자는 API 문서를 반드시 만들어 놔야 하고, 이를 사용할 프로그래머도 API 문서를 반드시 참고해야 한다.
![image](https://github.com/inpink/Effective-Java/assets/108166692/42e4bd25-ba5c-46ec-9a04-a952b039c946)

	﻿
- 아래는 정적 팩터리 메서드의 이름을 정할 때 "주로" 사용하는 "명명 방식"이다.

"주로" 사용되는 것이므로, 아래 적힌 내용을 너무 맹신하진 않았으면 한다.

관례는 관례일 뿐이다. service provider framework (4번 장점 예시)에서도 봤듯이, 기존 틀에서 변형은 얼마든지 가능하다.

틀에 박힌 사고는 이해를 더욱 어렵게 한다.

﻿
	
|Name|When|Example|Explanation|
|------|---|---|---|
|﻿from|﻿매개변수 하나, 해당 타입의 인스턴스 반환|﻿Date d = Date.from(instant);﻿|﻿현재의 날짜를 Date의 instance으로 반환한다|
|﻿of|﻿매개변수 여러 개, 적합한 타입의 인스턴스 반환|﻿ArrayList<Fruit> fruits = EnumSet.of(APPLE, BANANA, ORANGE);﻿|﻿APPLE, BANANA, ORANGE를 담은 Fruit Type의 리스트를 반환한다.|
|﻿valueOf|﻿from + of|﻿BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);﻿|﻿int 정수의 최대 범위를 BigInteger의 instance로 반환한다.|
|﻿instance / getInstance|﻿해당 클래스의 인스턴스를 반환한다. *반환되는 인스턴스는, 서로 같을 수도, 다를 수도 있다. (매개변수가 있다면 매개변수 조건에 맞춰 반환)|﻿Fruit fruit= Apple.getInstance("RED");﻿|﻿RED라는 특징을 지닌 Apple의 instance를 반환받는다.|
|﻿create / newInstance|﻿해당 클래스의 인스턴스를 반환한다. *반환되는 인스턴스는, 매번 새로운 객체를 할당해주므로 항상 서로 다르다. (매개변수가 있다면 매개변수 조건에 맞춰 반환)|﻿Object newArray= Array.newInstance(classObject,arrayLen);﻿|﻿classObject,arrayLen라는 특지을 지닌 Array class의 instance를 새로 할당하여 반환받는다|
|﻿get(Type)|getInstance와 유사하나,  반환되는 instance가 자기 자신 class의 instance가 아니라 다른 class의 instance다﻿|﻿FileStore fs = Files.getFileStore(path)﻿|﻿ Files라는 class에는"FileStore class"의 instance를 반환하는 getFileStore라는 메소드가 있다. path라는 특징을 지닌 FileStore instance를 반환한다.|
|﻿new(Type)|﻿newInstance와 유사하나, 반환되는 instance가 자기 자신 class의 instance가 아니라 다른 class의 instance다|﻿BufferedReader br = Files.newBufferedReader(path);﻿|﻿ Files라는 class에는 "BufferedReader class"의 instance를 반환하는 getBufferedReader라는 메소드가 있다. path라는 특징을 지닌 BufferedReader instance를 반환한다. |
|﻿(Type)|﻿get(Type)과 new(Type)의 간결한 버전이다.|﻿List<Complaint> lit=Collections.list(매개변수);﻿|﻿ Collections class는 list class의 instance를 반환한다.|
	   
   
	
***
　   
　   
    
> [ 느낀점 ]  💬  	
　   
　   
    
    
- 주제

1장과 2장-item1을 읽고,

생성자 대신 '정적 팩터리 메서드'를 사용했을 때 얻을 수 있는 이점 5가지에 대해 학습할 수 있었다.
　   
　   
    
    
- 생소했던 부분

이번 포스팅에서 다룬 내용들은 전반적으로 스프링 공부를 하며 알고 있었던 부분이 많았지만,

딱 하나, "service provide framework"가 생소했다. 도서에서도 간단하게 두 문단 정도로만 설명하고 있었기 때문에 추가로 공부가 꼭 필요했다.
　   
　   
    
    
- 고난과 해결

사실 이 책에서 "service provide framework"에 대해 적어둔 두 문단을 몇 십번이나 다시 읽었는 지 모르겠다.

JDBC 사용법을 찾아보고, 다양한 키워드로 서칭도 하고, Effective Java 강의도 찾아보고, DriverManager, Driver의 class, interface code를 직접 열어서 분석해보고서야 감이 잡혔다.

아무래도 도서가 번역본이다 보니, 번역투에서 애매하게 표현된 부분이 있었다.

영어 원문으로도 구매해야 하나 진지하게 고민중이다. 책을 계속 읽어가며 필요성을 느끼면 구매할 생각이다.
　   
　   
    
    
- 깨달은 점

이론적인 내용만 보는 것보다 실제로 어떻게 사용되는지 코드를 보는 것이 이해가 잘 되고 와닿는다.

DriverManager, Driver의 class, interface code를 직접 열어서 분석해보는 시간을 가지며, 문제 이해에 큰 도움이 되었다.

그리고 그 과정에서 오늘도 아직 내가 공부해야 할 것이 많다는 것을 뼈저리게 느끼게 되었다.
　   
　   
    
    
- 우테코는 신이에요

service provide framework에 대해 이해가 가지 않아 서칭을 할 때, 많은 포스팅에서는 책에 나온 내용 이상으로 설명되어 있지 않았다.

유일하게 우테코 3기에서 Effective Java 스터디를 하신 선배님들이 이 내용에 대해 매우 자세하게 설명해두셨다.

DriverManager, Driver code를 열어보는 것도 여기서 얻은 공부 방법이었다. 접근 방법이 너무 최고였을뿐더러 글이 너무 쏙쏙 읽혔다.

우테코 선배님이 작성하신 글을 보면서, 읽는 이를 배려하는 글쓰기에 대해 배우게 된다.

물론, 도서를 작성하는 것이라면 아래 내용이 해당되지 않겠지만, 여기는 얼마든지 커스터마이징이 가능한 블로그 환경이다.
　   
　   
1. 읽기 쉬운 글 쓰려면 우선 글의 구성을 정하고 난 뒤 써야한다.

2. 한 문단에 글이 너무 길면 안된다.

3. 중간 중간에 사진 자료, 적절한 코드 예시를 첨부한다.   
　   
　   
확실히 시간은 더 많이 들어가지만, 이렇게 정리하는 습관은 매우 유익하다.    

😀 나중에 내가 다시 이 글을 봤을 때 이해하기 쉽기 위해,

😀 글을 쓰면서 스스로 이해한 것을 더욱 꼼꼼히 확인하기 위해,

😀 남에게 설명하는 스킬을 향상키실 수 있다

😀 발표 자료를 만드는 과정과 유사하다. 자연스럽게 발표에도 도움이 된다   

　    
모든 글에 대해서는 어렵더라도 주1회 1개는 퀄리티 높은 글을 작성해보려 한다.   
　   
　   
    
    
- 다음 장부터는 이렇게

이 책은 아이템끼리 얽혀있다. 초장인데도 이미 여러 개의 item들이 등장하고 있다.

따라서 처음 읽을 때 한번에 100% 다 이해하려기보다는, 모르는 단어가 없도록 꼼꼼히 공부하되,

쭉 보고 다시 여러 번 복습하면 볼 때마다 분명히 새로운 관점이 등장할 것이라 생각하고 있다.
　   
　   
    
    
- 완독을 위한 응원

마지막으로, 이 책을 읽을 많은 독자들에게 하고 싶은 얘기가 있다.

초반에야 모르는 개념들이 많아서 막힌다고 느껴질 수 있어도(=학습 곡선이 높다),

꼼꼼히 공부하다보면 모르는 개념들이 많이 해소가 되고, 이 책의 스타일에도 익숙해지며 점차 읽는 속도가 빨라질 것이다!

이건 어릴 때부터 책을 많이 읽다보니 + 그냥 엉덩이 붙이고 끝까지 물고 늘어져서 끝까지 완수해내고 나니 얻은 경험에서 유추되는 패턴이랄까..ㅎ

또, 항상 하는 생각이지만 프로그래밍 분야에선 지름길보단, 모든 step을 차근차근 밟는 것이 가장 빠른 방법이라 생각한다.

목표를 달성하는 그 날까지 화이팅!!!!! 😊


﻿
