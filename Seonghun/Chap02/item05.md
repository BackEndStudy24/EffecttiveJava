## 아이템 5

---
### 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

- **이 챕터에서는 DI를 다루는 내용인거같다.**
- 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식 -> 의존 객체 주입의 한 형태
- 아래와 같은 형태는 매우 단순해서 수많은 프로그래머가 이 방식에 이름이 있다는지 모르고 사용했다 -> 이 챕터를 통해 왜쓰는지 공부하자

```java
import java.util.Objects;

public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public boolean isValid(String word) {...}
    public List<String> suggestions(String typo) {...}
}
```

- **결론**
- 클래스가! 내부적으로 하나 이상의 자원에 의존하고!, 그 자원이 클래스 동작에 영향을 준다면 싱글턴, 정적 유틸클래스는 사용하지 않는게 좋다! 
- 대신 필요한 자원을 생성자에 넘겨주고 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해줌!