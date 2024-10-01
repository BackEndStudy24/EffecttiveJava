# 객체의 생성과 파괴 

## 아이템 1

---
### AClass 에서 메서드를 만들어서 BClass 에서 생성자를 만들어서 메서드 호출 vs AClass 에서 정적 메서드를 만들어 BClass 에서 AClass.MethodName으로 호출하기

- **후자선택시 장점 5가지**
1. 이름을 가질 수 있다.
2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

- **후자 선택시 단점 2가지**
1. 상속을 하려면 public 이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다. -> 오히려 장점이 될 수 있지않나?
2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

- **결론**
- 정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 그렇다고 하더라도
정적 팩터리를 사용하는 게 유리한 경우가 더 많으므로 **무작정 public 생성자를 제공하던 습관이 있다면 고치자.**


## 아이템 2

---

### 생성자에 매개변수가 많다면 빌더를 고려하자

1. 점층적 생성자 패턴
```java
public class NutritionFacts {
private final int servingSize;  // 필수
private final int servings;     // 필수
private final int calories;     // 선택
private final int fat;          // 선택
private final int sodium;       // 선택
private final int carbohydrate; // 선택

	public NutritionFacts(int servingSize, int servings) {
		this(servingSize, servings, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories) {
		this(servingSize, servings, calories, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories, int fat) {
		this(servingSize, servings, calories, fat, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
		this(servingSize, servings, calories, fat, sodium, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
		this.servingSize = servingSize;
		this.servings = servings;
		this.calories = calories;
		this.fat = fat;
		this.sodium = sodium;
		this.carbohydrate = carbohydrate;
	}
}
```

- 위 내용은 점층적 생성자 패턴을 활용한 코드이다 하지만 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다는 단점이 생긴다.
- 코드를 읽을때 각 값의 의미가 무엇인지 헷갈리고 매개변수가 몇개인지도 주의해서 세어 보아야 할것이다.

2. 자바 빈즈 패턴

```java
public class NutritionFacts {

	private int servingSize = -1; // 필수
	private int servings = -1; // 필수
	private int calories = 0;
	private int fat = 0;
	private int sodium = 0;
	private int carbohydrate = 0;

	public NutritionFacts() { }

	public void setServingSize(int servingSize) {
		this.servingSize = servingSize;
	}
	public void setServings(int servings) {
		this.servings = servings;
	}
	public void setCalories(int calories) {
		this.calories = calories;
	}
	public void setFat(int fat) {
		this.fat = fat;
	}
	public void setSodium(int sodium) {
		this.sodium = sodium;
	}
	public void setCarbohydrate(int carbohydrate) {
		this.carbohydrate = carbohydrate;
	}
    
}
```

- 자바빈즈는 심각한 담점이 있다 **자바빈즈 패턴에서는 객체 하나를 만들렴녀 메서드를 여러개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다**
- 점층적 생성자 패턴에서는 매개변수들이 유효한지를 생성자에서만 확인하면 일관성을 유지할 수 있었다 하지만 세터를 사용하면 이러한 장치가 완전히 사라진다.
- 일관성이 깨진 객체를 만들게 되면 버그를 심은 코드와 그 버그 때문에 런타임에 문제를 겪는 코드가 물리적으로 멀리 떨어져있어 디버깅도 쉽지않다.

3. 빌더 패턴

```java
public class NutritionFacts {

	private int servingSize; // 필수
	private int servings; // 필수
	private int calories;
	private int fat;
	private int sodium;
	private int carbohydrate;

	public static class Builder {
    
		// 필수 매개변수
		private int servingSize;
		private int servings;
        
		// 선택 매개변수
		private int calories     = 0;
		private int fat          = 0;
		private int sodium       = 0;
		private int carbohydrate = 0;

		private Builder(int servingSize, int servings) {
			this.servingSize = servingSize;
			this.servings = servings;
		}
        
		public Builder servingSize(int servingSize) {
			this.servingSize = servingSize;
			return this;
		}

		public Builder servings(int servings) {
			this.servings = servings;
			return this;
		}

		public Builder calories(int calories) {
			this.calories = calories;
			return this;
		}

		public Builder fat(int fat) {
			this.fat = fat;
			return this;
		}

		public Builder sodium(int sodium) {
			this.sodium = sodium;
			return this;
		}

		public Builder carbohydrate(int carbohydrate) {
			this.carbohydrate = carbohydrate;
			return this;
		}

		public NutritionFacts build() {
			NutritionFacts nutritionFacts = new NutritionFacts();
			nutritionFacts.fat = this.fat;
			nutritionFacts.sodium = this.sodium;
			nutritionFacts.servings = this.servings;
			nutritionFacts.carbohydrate = this.carbohydrate;
			nutritionFacts.servingSize = this.servingSize;
			nutritionFacts.calories = this.calories;
			return nutritionFacts;
		}
	}

}
```

- 점층적 생성자 패턴, 세터 패턴의 장점만 모아둔 빌더패턴이다
- NutritionFacts 클래스는 불변이며, 모든 매개변수의 기본값들을 한곳에 모아뒀다. 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다. 이런 방식을 플루언트API, 메서드 연쇄라 한다.
- 하지만 빌더 생성 비용이 크지는 않지만 성능에 민감한 상황에서는 문제가 될 수 있다.
- 또한 점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다. 하지만 API는 시간이 지날수록 매개변수가 많아지는 경향이 있음을 명시하자



- **결론**
- **생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는게 더 낫다.** 매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.



## 아이템 3

---

### private 생성자나 열거 타입으로 싱글턴임을 보증하라

- **클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.**

```java
// public static final 필드 방식의 싱글턴
public class Seonghun {
    public static final Seonghun INSTATNCE = new Seonghun();
    private Seonghun() {...}
    public void leaveTheBuilding() {...}
}
```
- 위 코드는 인스턴스가 전체 시스템에서 하나뿐임이 보장되지만 권한이 있는 클라이언트가 리플렉션 API인 AccessibleObject.setAccessible을 이용해 private 생성자를 호출할 수 있다!
- 이러한 예외를 막기 위해 생성자를 수정하여 두번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.

```java
// 정적 팩터리 방식의 싱글턴
public class Seonghun {
    private static final Seonghun INSTATNCE = new Seonghun();
    private Seonghun() {...}
    public static Seonghun getInstance() { return INSTATNCE; }
    public void leaveTheBuilding() {...}
}
```

- 위 코드에서 Seonghun.getInstance는 항상 같은 객체의 참조를 반환하므로 제2의 Seonghun 인스턴스란 결코 만들어지지 않는다.
- 위 코드의 장점은 -> API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점이다
- -> 원한다면 정적 팩터리를 제너릭 싱글턴 팩터리로 만들 수 있따는 점이다.
- -> 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다는 점이다. 
```java
// 싱글턴임을 보장해주는 readResolve 메서드
private Object readResolve() {
    // '진짜' Seonghun을 반환하고, 가짜 Seonghun은 가비지 컬렉터에 맡긴다.
    return INSTANCE;
}
```

- 싱글턴을 만드는 세번째 방법은 원소가 하나인 열거 타입을 선언하는 것이다.
- 대부분의 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.
- 단, 만들려는 싱글턴이 Enum 외의 클래스를 사옷ㄱ해야 한다면 이 방법은 사용할 수 없다

## 아이템 4

---

### 인스턴스화를 막으려거든 private 생성자를 사용하라

- 단순히 정적 메서드와 정적 필드만을 담은 클래스를 만들고 싶을때가 있을것이다. -> 오 인정
- 아니 ㅋㅋ위 내용 읽고 오 ㅇㅈ 했는데 다음줄이 개웃김
- "객체 지향적으로 사고하지 않는 이들이 종종 남용하는 방식이기에 그리 곱게 보이지는 않지만, 분명 나름의 쓰임새가 있다." -> 님...아... ㅠㅠ

- 추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다. 다행히도 인스턴스화를 막는 방법은 아주 간단하다.
- 컴파일러가 기본 생성자를 ㅁ나드는 경우는 오직 명시된 생성자가 없을때 뿐이니 private 생성자를 추가해주면 클래스의 인스턴스화를 막을 수 있다.

```java
public class UtilityClass {
    // 기본 생성자가 ㅁ나들어지는 것을 막는다(인스턴스화 방지용).
    private UtilityClass() {
        throw new AssertionError();
    }
}
```

- 위 코드에서 명시적 생성자가 private이니 클래스 바깥에서는 접근 X 
