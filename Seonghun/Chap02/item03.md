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
