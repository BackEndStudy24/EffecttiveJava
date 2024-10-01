# Item 01 - 생성자 대신 정적 팩터리 메서드를 고려하라

-  클래스는 생성자와 별도로 정적 팩터리 메서드static factory method를 제공할 수 있다.

### 🧐 WHY? 장점이 뭐길래?
#### 1. 이름을 가질 수 있다.
- 생성자는 클래스와 이름이 동일해야 하기 때문에 메서드의 목적을 명확하게 알기 힘들다.
- 반면에 정적 팩터리 메서드는 이름을 커스텀할 수 있어서 해당 메서드의 특성을 직관적이게 보여줄 수 있음.
(ex. 값이 소수인 BigInteger를 반환할 때, BigInteger보다는 BigInteger.probablePrime이 더 쉽게 파악이 가능함)
- 하나의 시그니쳐(매개변수의 타입, 개수, 순서)로는 생성자를 하나만 만들 수 있기에, 각 생성자가 어떤 역할을 하는지 기억하기 힘들다.
  -> 한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면 생성자를 정적 팩터리 메서드로 바꾸고 그 특징, 차이가 잘 드러나는 이름을 지어주어야 한다.
#### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
  - 비슷한 기법으로 플라이웨이트 패턴이 있음
  - - 플라이 웨이트 패턴: 인스턴스를 공유시켜 new 연산자로 생기는 메모리 낭비를 줄이는 패턴
  - 반복되는 요청에 같은 객체를 반환하는 정적 팩터리 방식의 클래스는 인스턴스의 주기를 통제할 수 있다.(이런 클래스를 **인스터스 통제 클래스**라고 한다.
  - - 왜 인스턴스를 통제하는가?
    - 인스턴스를 통제하면 클래스를 싱글턴 / 인스턴스 불가로 만들 수 있음
    - 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있음
    - -> 메모리의 효율, 객체(데이터)의 일관성, 동일한 값을 가진 인스턴스가 여러 개 생성되지 않도록 보장
#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
 - 하위 타입이란 extends나 interface를 말한다.
 - 왜 이게 장점인가? 객체를 유연하게 생성하고 변경할 수 있음, 구현체를 숨길 수 있음.
#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
#### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
  - 정적 팩토리 메서드가 인터페이스나 상위 클래스의 타입을 반환하도록 설계되기 때문에, 실제로 반환할 구체적인 하위 클래스가 나중에 추가되더라도 문제가 없다.
  - 이런 유연함은 서비스 제공자 프레임워크를 만드는 근간이 됨(ex.JDBC)
  - 서비스 제공자 프레임워크 패턴에는 여러 변형이 있는데, 브리지 패턴이 그 중 하나임
### 🧐 정적 팩터리 메서드의 단점
#### 1. 상속을 하려면 public이나 protected 생성자가 필요해 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
#### 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
</br>

### 🚩 결론
정적 팩터리 메서드와 public 생성자는 각각의 장단점과 쓰임새가 있으니 적절히 사용하는 것이 좋다!


# Item 02 - 생성자에 매개변수가 많다면 빌더를 고려하라

### 🧐 WHY? 다른 방법은 없을까?
#### 1. 점층적 생성자 패턴
```Java

    public class NutritionFacts {
    private final int servingSize;  // (mL, 1회 제공량)     필수
    private final int servings;     // (회, 총 n회 제공량)  필수
    private final int calories;     // (1회 제공량당)       선택
    private final int fat;          // (g/1회 제공량)       선택
    private final int sodium;       // (mg/1회 제공량)      선택
    private final int carbohydrate; // (g/1회 제공량)       선택

    public NutritionFacts(int servingSize, int servings) {
        this.sevingSize = 	servingSize;
  		this.servings	=	servings;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories) {
        this.sevingSize = 	servingSize;
  		this.servings	=	servings;
  		this.calories	=	calories;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat) {
        this.sevingSize = 	servingSize;
  		this.servings	=	servings;
  		this.calories	=	calories;
  		this.fat 		=	fat;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium) {
        this.sevingSize = 	servingSize;
  		this.servings	=	servings;
  		this.calories	=	calories;
  		this.fat 		=	fat;
  		this.sodium		=	sodium;
    }
    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize  = servingSize;
        this.servings     = servings;
        this.calories     = calories;
        this.fat          = fat;
        this.sodium       = sodium;
        this.carbohydrate = carbohydrate;
    }
    }

```
- 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

#### 2. 자바빈즈 패턴
```JAVA
public class NutritionFacts {
    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.

    private int servingSize  = -1; // 필수; 기본값 없음
    private int servings     = -1; // 필수; 기본값 없음
    private int calories     = 0;
    private int fat          = 0;
    private int sodium       = 0;
    private int carbohydrate = 0;

    public NutritionFacts() 
  	{ 
  	}

    // Setters
    public void setServingSize(int val)
  	{ 
		servingSize = val; 
  	}
 	
    public void setServings(int val)
  	{ 
  		servings = val; 
  	}
   
  	public void setCalories(int val)     
  	{ 
  		calories = val; 
  	}
   
  	public void setFat(int val)          
  	{ 
  		fat = val; 
  	}
   
  	public void setSodium(int val)       
  	{ 
  		sodium = val; 
  	}
 	
    public void setCarbohydrate(int val) 
  	{ 
  		carbohydrate = val; 
  	}
```
- 자바빈즈 패턴은 객체를 하나 만드려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓인다. -> 스레드 작업을 하려면 프로그래머가 추가 작업을 해야한다..
- 대안 방법으로 얼리고(freezing)이 나오기는 했으나 다루기 어려워 실전에서는 자주 쓰이지 않는다.
### 👍 빌더패턴: 점층적 생성자와 자바빈즈 패턴의 장점만 가지고 있다!
```JAVA
public class NutritionFacts 
{

    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;


    public static class Builder 
	{
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) 
  		{
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { 	
  			calories = val;      
  			return this; 
  		}
        public Builder fat(int val)
        { 
  			fat = val;           
  			return this; 
  		}
        public Builder sodium(int val)
        { 
  			sodium = val;        
  			return this; 
  		}
        public Builder carbohydrate(int val)
        { 
  			carbohydrate = val;  
  			return this; 
  		}

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }


    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
```
- 필드 매개변수만으로 생성자(or 정적 팩터리)를 호출해 빌더 객체를 얻는다.
- 빌더 패턴은 명명된 선택적 매개변수(name optional parameter)를 흉내낸 것이다.
- 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
- 빌더를 사용하면 가변인수 매개변수를 여러 개 사용할 수 있다.

### 🧐 빌더패턴의 단점
- 점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다. 하지만 API는 시간이 지날수록 매개변수가 많아지는 경향이 있으므로 빌더로 시작하는 것이 좋다.


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

# Item 04 - 인스턴스화를 막으려면 private 생성자를 사용하라

- 매개변수를 받지 않는 public 생성자가 만들어지면 사용자는 이게 자동 생성된 것인지 구분할 수 없다.
- 추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다. 하위 클래스를 만들어 인스턴스화하면 된다
- pirvate 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.
