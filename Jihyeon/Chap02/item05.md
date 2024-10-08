# Item 05 - 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
- 이전 아이템에 있었던 정적 유틸리티와 싱글턴은 자원이 하나인 것을 상정하고 짠 코드다.

### 사용하는 자원에 따라 동작이 달라지는 클래스에는 의존 객체 주입을 사용해야 한다.

```java
public class SpellChecker{
private final Lexicon dictionary}{
this.dictionary = Objects.requireNonNull(dictionary);
}
public boolean isValid(String word){
}
public List<String> suggestions(String typo){
}
```

### 다른 방식: 생성자에 자원 팩터리를 넘겨주기
- 팩터리 메서드 패턴
  ```Java
  Mosaic create (Supplyier<? extends Tile> tileFactory){
  }
  ```

### 단점
  - 의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 함.
