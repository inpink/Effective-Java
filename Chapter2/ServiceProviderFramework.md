> # Effective-Java
🔖 CS Books - effective java(joshua bloch) learning and summarizing   
📗 effective java  ( joshua bloch )   
**⭐ https://blog.naver.com/willyouspeedup/223102011676 네이버 블로그**
　   
  
　   
# ﻿서비스 제공자 프레임워크(service provider framework)


>  SPF는, 프레임워크(framework)의 일종이다.   💬  


　   
**대부분의 프레임워크를 사용할 때를 생각해보자.**

우리는 이미 구현된 클래스의 인스턴스를 반환받아 사용한다.

~~~
PriorityQueue<Integer[]> heapq = new PriorityQueue<>(
				(o1, o2) -> o1[0] == o2[0] ? o1[1] - o2[1] : o1[0] - o2[0]);
~~~
﻿
위의 코드는 Java Collections Framework 中 PriorityQueue를 사용하는 코드이다.

자바 개발자가 PriorityQueue를 이미 다 구현해놓았다.

우리(코드 사용자)는 이를 가져다 쓰기만 하면 된다.

﻿

- 즉, 일반적인 프레임워크에서는

1) 프레임워크 개발자

2) 코드 사용자

두 단위이다.

　   
- 하지만, service provider framework는,

1) 프레임워크 개발자

2) 제공자(Provider)

3) 코드 사용자

세 단위이다.

﻿
 - 요약하자면,

1. 프레임워크 개발자(1)는 "인터페이스"만을 구현해놓는다. 인터페이스 안에는, "제공자(Provider)"가 구현해야 할 메소드가 있다.

2. Provider(2)는, 메소드를 구현하는 구현체(Class)를 만든다.

3. 코드 사용자(3)는, Provider가 구현해놓은 jar 파일을 다운받아서 구현된 프레임워크 코드를 사용할 수 있다.   
　   
***
　   
> 왜 굳이 Provider를 나눠놨을까? 💬  

　   
자바에서 DB 개발을 한다고 생각해보자.

DB 업체는 매우 많다. 이 순간에도 새롭게 생기고 있으며, 이미 있는 DB 회사들도 기능을 변경시킨다.

자바 측에서, 이 DB들에 접근할 수 있는 각각의 기능들을 다 만들고 수정해줘야 한다면?

수 많은 DB 업체들의 요청을 감당할 수 없을 것이다.

　   
=> 그래서 사용하는 것이 service provider framework이다.

자바는 인터페이스만을 제공한다. DB 회사에서 코드를 구현해서 jar 파일로 굽는다. 자바에서 그 DB를 사용하길 원하는 개발자는 jar 파일을 다운받아서 구현체(class)를 객체(Instance)로 만들어 사용한다.
　   
　   
- 예시를 통해 더 자세히 보자

service provider framework에는 JDBC, Spring 등이 있다.

JDBC를 예를 들어 설명해보겠다.
　   
    
　   
# ﻿JDBC(Connection,Driver,DriverManager분석)

> ﻿JDBC 사용자(3) 관점 맛보기
　   
- JDBC란

﻿자바에서 DB에 접근하기 위해 사용하는 'service provider framework'이다.﻿
 ![image](https://github.com/inpink/Effective-Java/assets/108166692/e9d419f0-a723-495b-9fe5-d91e8015b404)

　   
다음과 같은 과정을 거쳐, 자바에서 DB에 접근하여 데이터를 추가하고, 조회하고, 수정하는 등의 작업을 해줄 수 있다.

DB는 MySql을 사용한다고 해보자.

　   
﻿

1) MySql에서 제공하는 jar 파일을 다운받아서 자바 편집 플랫폼(이클립스, 인텔리제이 등)에 넣는다


2) JDBC service provider framework를 불러온다.


3) jar 파일 안에는, MySql 회사에서 구현한 Driver Class(구현체)가 있다. 내 코드에서 구현체를 불러와야 한다.

- 이는, 리플렉션(Reflection, item 65)를 이용한다. 아래 코드가 리플렉션의 일종이다.

~~~
Class.forName("com.mysql.cj.jdbc.Driver");
~~~
- com.mysql.cj.jdbc.Driver이라는 이름을 가진 mysql회사가 구현한 Driver Class(구현체)를 "리플렉션"으로 찾아오는 것이다.
　  
　   
 4) mysql을 이용한 내 DB에 접속연결한다.
~~~
Connection conn = DriverManager.getConnection(DB_URL, ID, PW);
 ~~~
 　   
 5) 자바 SQL 라이브러리를 이용하여 DB에 접근하여 사용한다.
~~~
﻿ import java.sql.*

PreparedStatement pstmt = conn.prepareStatement();
~~~
　   

- 사용자가 JDBC를 사용하는 자세한 코드는

SPF를 충분히 학습한 뒤, 마지막에 다시 보도록 하겠다.
　   
　   
***
　   
> 리플렉션(Reflection)이란 💬  

프로젝트 내의 인터페이스, 클래스, 메소드 등을 찾아서 쓸 수 있게 해준다.

이들의 인스턴스를 생성해주기도 한다.
　   
　   
> 다음으로 볼 것 💬  

위의 내용은, 코드 사용자(3)의 관점으로 본 것이다.

프레임워크 개발자(1)와 Provider(2)의 관점으로 자세히 보자.
　   
　   
 ***
 　   
 > ﻿SPF의 핵심 컴포넌트 4가지 💬

﻿
a. 서비스 인터페이스(service interface) : Connection interface(JDBC)

b. 제공자 등록 API (provider registration API) : DriverManager.registerDriver method(JDBC)

c. 서비스 접근 API (service access API): DriverManager.getConnection method(JDBC)

d. 서비스 제공자 인터페이스(service provider interface) : Driver interface(JDBC)
 　   
 　   
﻿
- **a. 서비스 인터페이스(service interface)**

: 구현체의 동작을 정의

: 프레임워크 개발자(1)가 만든다. 인터페이스만 만들어놓고, 구현해야할 메소드를 적어놓는다.

: Provider(2)가 구현한다.

: JDBC에서는, "Connection"이다.

~~~
public class myDBDriver implements Connection {

	@Override
	public <T> T unwrap(Class<T> iface) throws SQLException {
		// Provider(DB회사)가 구현해줘야 할 내용 
		return null;
	}

	@Override
	public boolean isWrapperFor(Class<?> iface) throws SQLException {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public Statement createStatement() throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public PreparedStatement prepareStatement(String sql) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}
~~~
﻿
위 코드는, 실제로 Connection Interface에서 구현해줘야 할 메소드들의 극히 일부를 가져온 것이다.

이를 통해, 사용자는 같은 prepareStatement라는 메소드를 쓰지만

아래와 같이, 각 DB 회사에서 구현해놓은 대로, SQL문이 서로 다르게 처리되어 서로 다른 결과를 받을 수 있는 것이다.

﻿
 ~~~
 ﻿Connection conn = DriverManager.getConnection(DB_URL, ID, PW);
conn.prepareStatement(각 DB에 맞는 SQL문);
~~~
　   
　   
　   
- **b. 제공자 등록 API (provider registration API)**

: Provider(2)가 구현체(class)를 등록할 때 사용

: 프레임워크 개발자(1)가 개발하여 제공한다

: Provider는 서비스 인터페이스(a)의 메소드를 구현한다. 그 다음, 사용자(3)가 이를 찾아 쓸 수 있도록 해줘야 한다.

: 모든 DB업체가 자신들의 구현체를 JDBC Framework 내에 올려달라고 요구한다고 생각해보자. 수정 요구도 있을 것이다. 이를 수동으로 다 처리해줘야 한다면, JDBC 입장에선 여간 귀찮은 일이 아닐 수가 없다.

: Provier(2)는 '제공자 등록 API'를 이용하여 자신들의 구현체(class)를 특정 규칙을 가진 경로에 저장시킨다. 사용자(3)가 jar 파일에서 찾아 쓸 수 있게끔 하는 것이다.

: (바로 밑 c번에서 나오지만, 사용자(3)는 이 규칙을 가진 경로를 찾기 위해 서비스 접근 API(c)를 이용한다. )

: JDBC에서는, " DriverManager.registerDriver() "이다.
　   
　   
~~~
public static void registerDriver(java.sql.Driver driver,
            DriverAction da) //3,4
        throws SQLException {

        /* Register the driver if it has not already been added to our list */  //2
        if (driver != null) { //1
            registeredDrivers.addIfAbsent(new DriverInfo(driver, da));
        } else {
            // This is for compatibility with the original DriverManager
            throw new NullPointerException();
        }

        println("registerDriver: " + driver);

    }
 ~~~
 ﻿
위 코드는 실제로 JDBC의 DriverManager class에서 발췌한 registerDriver 메소드이다.
DriverManager는 Interface가 아니라 class이다. JDBC 개발자가 구현해놓는다.
　   　   
　   
　   
구조가 얽혀있어 전체를 다 설명하기엔 많이 복잡하지만, 코드를 간단히 리뷰해보겠다.

1. driver != null => driver class가 없으면, NullPointerException예외가 뜬다.

2. Register the driver if it has not already been added to our list => Driver 클래스가 이미 등록되어 있지 않은지 확인하고, 등록해준다.

3. DriverAction da=> 인터페이스다. 드라이버 등록 취소 deregister()할 때 사용한다. 당연히, 드라이버를 관리하는 내용이므로 사용자(3)가 사용하지 않는다. Provide(2)가 구현체(class)를 만들어 사용한다.

4. A JDBC Driver may choose to create its DriverAction implementation in a private class to avoid it being called directly. => DriverAction da를 쓰는 이유이다. da는 Provider(2)가 만든 DriverAction의 구현체(class)이다.

☆Provider는 구현체는 private로 만들고 매개변수를 통해 전달만 한다. 그럼, 이 구현체를 Provider가 전달하지 않고서는 마음대로 접근할 수 없게 막는 효과를 가질 수 있다.

　   
    ![image](https://github.com/inpink/Effective-Java/assets/108166692/d0801f80-6d35-4e87-abdf-a821c8a6f249)
　   
Docs를 참고하면 코드 이해에 도움이 된다.

https://docs.oracle.com/javase/8/docs/api/java/sql/DriverManager.html#registerDriver-java.sql.Driver-java.sql.DriverAction-

　   
　   
　   
　   
- **c. 서비스 접근 API (service access API)**

: 코드 사용자(3)가 jar 파일의 특정 경로에 위치한 구현체(Provider(2)가 서비스 인터페이스(a)를 구현한 class)를 찾기 위해 사용한다.

: 코드 사용자(3)가, 서비스 인터페이스(a)의 구현체의 객체(instance)를 받아오기 위해 쓰기도 한다.

=> class를 받아올지, instance를 받아올지, 어떻게 쓰일지는, 프레임워크 개발자(1)가 개발하기 나름이다.

: 프레임워크 개발자(1)가 개발하여 제공한다

: JDBC에서는 DriverManager.getConnection(DB 연결 정보) method 이다.

: JDBC에서는, 위의 API를 통해 Connection(a)의 객체를 반환한다.
　   
　   
~~~
 private static Connection getConnection(
        String url, java.util.Properties info, Class<?> caller) throws SQLException {
        ClassLoader callerCL = caller != null ? caller.getClassLoader() : null;
        if (callerCL == null || callerCL == ClassLoader.getPlatformClassLoader()) {
            callerCL = Thread.currentThread().getContextClassLoader();
        }

        if (url == null) {
            throw new SQLException("The url cannot be null", "08001");
        }

        println("DriverManager.getConnection(\"" + url + "\")");
        ensureDriversInitialized();
        SQLException reason = null;

        for (DriverInfo aDriver : registeredDrivers) {
            if (isDriverAllowed(aDriver.driver, callerCL)) {
                try {
                    println("    trying " + aDriver.driver.getClass().getName());
                    Connection con = aDriver.driver.connect(url, info);
                    if (con != null) {
                        // Success!
                        println("getConnection returning " + aDriver.driver.getClass().getName());
                        return (con);
                    }
                } catch (SQLException ex) {
                    if (reason == null) {
                        reason = ex;
                    }
                }
            } else {
                println("    skipping: " + aDriver.driver.getClass().getName());
            }
        }

        if (reason != null)    {
            println("getConnection failed: " + reason);
            throw reason;
        }

        println("getConnection: no suitable driver found for "+ url);
        throw new SQLException("No suitable driver found for "+ url, "08001");
    }
~~~
　   
위의 코드는, 실제로 DriverManager class에서 발췌한 getConnection(c)의 API이다.
사용자가 매개변수로 전달한 DB 정보를 이용해서, Connection 객체를 반환해주는 역할을 한다.
　   
　   
getConnection(c)는 JDBC개발자(1)가 만드는 것이다.
Connection의 구현체는, Provider(2)가 만드는 것이다.
　   
　   
그럼, getConnection(c)은 어떻게 Provider(2)가 만든 구현체(class)의 객체를 반환해주고 있을까?
아래 부분이 핵심이다.
　   
~~~
Connection con = aDriver.driver.connect(url, info);
~~~
﻿
사용자가 DriverManager.getConnection()으로,
Provider가 구현한 구현체의 인스턴스를 받아오려면,
Provider는 추가적으로 Driver Interface도 반드시 구현해줘야 한다
　   
　   
사용자(3) 요청 -> DriverManager class(1이 개발) -> Driver Interface(1이 개발)의 구현체(2이 개발)에 토스
위의 과정으로 진행된다.
　   
　   
　   
﻿
- **d. 서비스 제공자 인터페이스(service provider interface)**

: 서비스 인터페이스(a)의 구현체(class)의 "객체"를 반환하는 역할을 하는 "인터페이스"이다.

: 프레임워크 개발자(1)가 만든다. 인터페이스만 만들어놓고, 구현해야할 메소드를 적어놓는다.

: Provider(2)가 구현한다.

=> 이 또한, 프레임워크 개발자(1)가 의도하기 나름이다. 아예 서비스 제공자 인터페이스(d)가 없는 경우도 있다.

=> 없는 경우, 사용자(3)는 "리플렉션(Reflection)"을 이용하여 직접 객체를 받아와서 써야 한다.

: JDBC에서는 있다. Driver interface이다. 심지어, "필수"이다.

~~~
public class myDBDriver implements Driver {

	@Override
	public Connection connect(String url, Properties info) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public boolean acceptsURL(String url) throws SQLException {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public DriverPropertyInfo[] getPropertyInfo(String url, Properties info) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public int getMajorVersion() {
		// TODO Auto-generated method stub
		return 0;
	}

	@Override
	public int getMinorVersion() {
		// TODO Auto-generated method stub
		return 0;
	}

	@Override
	public boolean jdbcCompliant() {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public Logger getParentLogger() throws SQLFeatureNotSupportedException {
		// TODO Auto-generated method stub
		return null;
	}

}
~~~
　   
실제로 구현해줘야하는 Driver interface의 method 전부를 가져와봤다.
위의 코드처럼, Provider(2)는 Driver을 구현해주게 된다.
　   
　   
서비스 접근 API(c)에서 봤듯,
Provider(2)는 서비스 제공자 인터페이스(Driver, d)를 필수로 구현해줘야 한다!
　   
　   
　   
또,
JDBC 개발자는 DriverManager에서 Driver 구현체만을 올리게 해놓았다.
Driver 구현체에서, Connection 구현체로 다시 연결되는 것이다.
　   
　   
JDBC(1)는 처음부터 Provider(2)로부터 Connection 구현체를 받아오지 않는다.
Driver 구현체만을 받아와서, Driver가 Connection 구현체를 전달하도록 했다.
　   
　   
사용자(3) 요청 -> DriverManager class(1이 개발) -> Driver Interface(1이 개발)의 구현체(2이 개발)에 토스 -> Connection interface(1이 개발)의 구현체(2이 개발) 전달
위의 과정으로 확장된다.
　   
　   
　   
　   
~~~
public class myDBDriver implements Driver {

	@Override
	public Connection connect(String url, Properties info) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}
~~~
﻿
즉, Driver 구현체에서 Connection 구현체로 연결하는 것은 Provider가 아래 코드에서 구현해야 할 내용이다.
위는 Driver interface의 connect() method이다.
　   
　   
 ~~~
 Connection con = aDriver.driver.connect(url, info);
 ~~~
 ﻿위는 DriverManager의 getConnection method 중 일부이다.
　   
　     
  ***
　   
　   
  > ﻿SPF 개발자, Provider, 사용자 - 전체 관점에서 보는 Servide Provider Framework 💬

　   
　   
ㄱ. JDBC 개발자는 Connection,Driver Interface을 만들어놓음. DriverManager class도 구현해놓음.

ㄴ. DB 회사는 JDBC framework를 받아와서, Connection, Driver를 구현함. 이 때, DriverManager.registerDriver(Driver 구현체) 로 "특정 경로에" Driver을 등록해준다. (실제로 JDBC framework 자체가 변경되는 것이 아니라, jar 파일로 배포해야 함)

ㄷ. DB 회사는 구현 완료 후, jar파일로 배포함. Connection은 "실제 DB를 조작하는 로직들"이, Driver은 "connection의 class의 instance를 반환해주는 기능"을 함!

ㄷ. 사용자는 DB 회사의 JDBC jar 파일을 가져와서 "리플렉션"으로 driver의 "클래스(구현체)"를 가져옴

ㄹ. 사용자는 DriverManager.getConnection(내db정보)로 내 DB에 접속함 (DriverManager는 JDBC에서 만들어둔 것)

ㅁ. 이 때, DriverManager에서는 ☆사용자가 가져온 Driver를 이용하여☆ DB 접속을 시도함. 이 때, Driver에게 협력을 요청함.

ㅅ. Driver는, 접속할 수 있는 db정보인지 확인한 후, DB회사(Provider)가 만든 Connection의 구현체(Class)의 Instance를 반환해줌!

ㅇ. 사용자는, 이제 반환받은 connection instance로 사용자 DB에 접근할 수 있다.
　   
　   
　   
　   
***
　   

> ﻿JDBC 사용자(3) 관점 API 정리  💬


　   
![image](https://github.com/inpink/Effective-Java/assets/108166692/1d296a8d-1786-427f-be77-bcb4219d25fd) 　   
참고 도서 : 짧고 굵게 배우는 JSP 웹 프로그래밍과 스프링 프레임워크
　   
　   
　   
~~~
//1. JDBC Driver Load. 아래 코드는 mysql의 Driver 경로
Class.forName("com.mysql.cj.jdbc.Driver");

// 2. DB 연결
Connection connection = DriverManager.getConnection(dbURL,username,pwd);

// 3. SQL을 담는 PreparedStatement 객체 생성
PreparedStatement preparedStatement = connection.prepareStatement(
"INSERT into user(id, name, password) values(?,?,?)"
);
preparedStatement.setLong(1, 1);
preparedStatement.setString(2, "name");
preparedStatement.setString(3, "password");

// 4. SQL문 전송 + 5. 결과값 받기

//executeUpdate() : SELECT를 제외한 INSERT, DELETE, UPDATE, CREAT, DROP 등에 사용한다. 반환 값은, int형이다.
int ret = preparedStatement.executeUpdate(); 

//executeQuery() : SELECT문을 사용할 때 씀. 반환 값은, "ResultSet 객체" type이다.
ResultSet rs = preparedStatement.executeQuery();

// 6. 연결 해제
preparedStatement.close();
connection.close();
~~~
참고 블로그 : https://limdevbasic.tistory.com/26﻿
　   
　   
　   
***

> ﻿'정적 팩터리 메서드'에서 SPF를 왜 학습하는가? 💬

　   
SPF는, ☆정적 팩터리 메서드를 작성하는 시점에서는, "반환할 객체의 클래스가 존재하지 않아도 된다"
라는 특징을 사용한다.
　   
　   
실제로 Connection,Driver Interface의 class는 만들어지지 않았지만,
JDBC 개발자는 이와 상관없이 Connection,Driver Interface를 사용하는 DriverManager를 개발할 수 있다는 것이다!
　   
　   
***
　   
　   
> ﻿헤맸던 부분 💬

　   
　   
이 책에서는 service provider framework에서 service provider interface(4)가 쓰이는 경우도 있고, 아닌 경우도 있다고 설명하였다.
그리고 바로 그 다음 문장에서, JDBC를 예시로 들며 JDBC의 service provider interface는 Driver라고 설명하고 있다.
　   
　   
내가 들었던 의문은, service provider interface(4)은 안쓰일 수도 있다고 했는데?
그렇기에 JDBC에서 Driver가 필수적으로 사용된다는 것을 인지하지 못해서, 머리속에서 로직이 꼬였었다.
　   
　   
그렇다. 위에서 여러 번 언급했듯, JDBC에는 Driver가 무조건 쓰이고 있다. 무조건 Provider는 Driver(4)를 구현해줘야 한다.
　   
　   
즉, 이론은 이론일뿐이라는 것을 깨달았다.
큰 틀은 당연히 존재한다. 8-90%는 큰 틀을 참고하게 된다.
하지만 사용자와 개발자의 개발에 도움이 된다면, 1~20%에서 얼마든지 변형이 가능한 것이, 프로그래밍의 힘이라고 생각한다.
　   
　   
SPF를 실제로 적용할 때, 활용도는 매우 다양할 수 있다는 것을 인지하자!
　   
　   
***
　   
　   
> ﻿참고 문헌 추천 

　   
아래 포스팅에서 매우 잘 설명되어있다! 같이 읽어보길 강추한다. 우테코 짱짱맨🙌　   
https://sihyung92.oopy.io/java/service-provider-framework
