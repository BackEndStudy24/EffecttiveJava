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
