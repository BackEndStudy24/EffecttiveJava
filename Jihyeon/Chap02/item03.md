# Item 03 - private 생성자나 열거 타입으로 싱글턴임을 보증하라
- 싱글턴(sigleton): 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트는 테스트하기 어려워질 수 있다.

  ```Java
  
  public enum Season
  {
	Spring, Summer, Fall, Winter
  }
    ```

  - 이 때, Spring, Summer, ... 이 상수들은 모두 Season의 객체이다..
  - enum 타입은 본질적으로 싱글턴을 보장하며, 각 상수는 해당 타입의 유일한 객체로 관리된다.
  - -> 즉, Season 열거형의 각 상수는 그 자체로 하나의 객체이며, 이는 enum이 private 생성자를 통해 생성되는 객체임을 보여준다.
 
### 🧐 How? 그럼 어떻게 싱글턴을 만들까?
#### 1. public static final 필드 방식
```JAVA
public class Cat
{
	public static final Cat INSTANCE = new Cat();

  	private Cat()
  	{
  	}

  	public void meow(String str)
  	{
  		System.out.println("meow");
  	}
}
```
- 장점1: 해당 클래스가 싱글턴임이 API에 명백히 드러남
- 장점2: 코드가 매우 간결하다

#### 2. 정적 팩터리 방식
```JAVA
public class Cat
{
	private static final Cat INSTANCE = new Cat();

  	private Cat()
  	{
  	}

	public static Cat getInstance()
  	{
  		return INSTANCE;
  	}

  	public void meow(String str)
  	{
  		System.out.println("meow");
  	}
}
```
- 장점1: API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있음
- 장점2: 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음
- 장점3: 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.

#### 하지만 위의 두 가지 방식은 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다.. 
- 2번 방식에서 가짜 인스턴스를 예방하려면 readResolve 메서드를 추가해줘야 한다.

### 👍 Best! 열거 타입 방식의 싱글턴

```JAVA
public enum Cat
{
  	INSTANCE;

	public void meow(String str) 
  	{
   		System.out.println("meow");
  	}
}
```
- 매우 간결하고, 추가 노력 없이 직렬화할 수 있고, 리플렉션 공격에도 안전하다!

### 🚩 결론
대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다! (단, Enum 외의 클래스를 상속받는 싱글턴이라면 이 방법은 사용x)

