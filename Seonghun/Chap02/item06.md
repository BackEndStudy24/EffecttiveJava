## 아이템 6

---
### 불필요한 객체 생성을 피하라

- 아래 두 코드가 예시인데 String.matches는 객체 생성 비용이 비싸기때문에 재사용 효율이 매우 좋음! 그래서 2번 코드처럼 재사용 하는게 효율이 좋다!
- 하지만 다른곳에서 사용하지 않는다면 필드는 쓸데없이 초기화가 된거고 지연 초기화를 사용하여 불필요한 초기화를 없앨 수 있지만 권하지는 않는다고 합니다!
- 왜냐하면 지연 초기화는 코드를 복잡하게 만드는데, 성능은 크게 개선되지 않을 때가 많기 때문이다!

```java
public class ex6 {
	static boolean isRomanNumeral (String s){
		return s.matches("^(?=.)" + (X));
	}
}
```

```java
public class ex6 {
		private static final Pattern ROMAN = Pattern.compile(
			"^(?=.)");
	
	static boolean ex6 (String s){
		return ROMAN.matcher(s).matches();
	}
}
```

- 아래 코드는 필자가 실제로 코테 문제를 풀때 의문점을 가졌던 문제인데 여기서 뼈를 떄렸다
- long이 아닌 Long으로 선언해서 불필요한 인스턴스가 2^31개나 생성됨! 에바다.
- 박싱된 기본 타입보다는 기본 ㅏ입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자!
- 
```java
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++ ){
        sum += i;
    }
    return sum;
}
```