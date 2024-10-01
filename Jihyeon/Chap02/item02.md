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
