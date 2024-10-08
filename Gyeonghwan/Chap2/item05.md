## item 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
1번 방식
```java
public class SpellChecker {
    private final Lexicon dictonary = new KoreanDictionary();
    
    private SpellChecker() {
    }
    // .....
}
```
* 위의 1번 방식은 `new`키워드를 사용하여 `KoreanDictionary`구현체에 강하게 결합되게 된다.
* 코드 간의 유연성이 떨어지고, 자원을 바꾸거나 테스트할 때 어려움이 생긴다.

2번 방식
```java
public class SpellChecker {
    private final Lexicon dictonary;
    
    public SpellChecker(Lexicon dictonary) {
        this.dictonary = Object.requireNonNull(dictonary);
    }
    // .....
}
```
* 위의 2번 방식은 의존성 주입(DI)를 사용하여 외부에서 자원을 전달 받는 방식이다.
* 이는 다양한 `Lexicon` 구현체를 전달받을 수 있어 유연성이 높고, mock(가짜 객체)를 전달하여 테스트도 쉽게 할 수 있다.

## 장점
1. 유연성 증가
    * 객체 간 결합도가 낮아지므로 다양한 자원을 주입할 수 있다.
2. 테스트 용이성
    * 테스트용 mock 객체를 주입할 수 있어 테스트가 용이하다.
3. 재사용성 향상
    * 특정 구현체에 종속되지 않으므로 재사용성이 높아진다.
4. 유지보수성
    * 외부에서 자원만 교체하면 되므로 유지보수에 좋다.

## 한 줄 정리
* 자원을 직접 클래스 내부에서 명시하지 말고 생성자를 통해 외부에서 객체를 주입받도록 해라.

