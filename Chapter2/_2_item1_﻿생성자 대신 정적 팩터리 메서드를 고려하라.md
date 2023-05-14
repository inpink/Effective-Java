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
