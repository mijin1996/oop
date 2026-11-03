# 1·2학기 프로그래밍 퀘스트북

1학기 자바·Git과 2학기 C# 수업 자료를 한 파일로 정리했어요. 스테이지마다 **한 줄 요약 → 핵심 → 코드** 순서예요.

- [1학기 · 자바 & Git](#1학기--자바--git) (14 스테이지)
- [2학기 · C# 실무 패턴](#2학기--c-실무-패턴) (19 스테이지)

---

# 1학기 · 자바 & Git

객체지향 3대 특징: **캡슐화**(숨기고 창구로만) · **상속**(물려받고 다른 점만 추가) · **다형성**(여러 이름으로 부르기)

**스테이지 목록**: 01. 클래스(Class)와 인스턴스(Instance) · 02. 참조(Reference) · 생성자(Constructor) · static · 03. 캡슐화(Encapsulation) · getter / setter · 04. 상속(Inheritance) · 오버라이드(Override) · super · 05. 추상 클래스(Abstract Class) · 인터페이스(Interface) · 06. 다형성(Polymorphism) · 07. 단위 테스트(Unit Test) · 08. 문자열(String) 다루기 · 09. 정규표현식(Regular Expression) · 10. 컬렉션(Collection) · List / Set / Map · 11. Object 3형제 · 정렬(Sorting) · 복사(Copy) · 12. 제네릭(Generic) · 열거형(enum) · 13. 기초 자료구조(Data Structure) · 14. Git & GitHub

## PART 1 · 객체지향 기초

_수업 자료: 8~13장_

### STAGE 01 · 클래스(Class)와 인스턴스(Instance)

_수업 자료 · 8장 인스턴스와 클래스_

> **한 줄 요약** 클래스는 붕어빵 틀, 인스턴스는 그 틀로 구운 진짜 붕어빵이에요.

- 오브젝트(Object) = 현실의 모든 것 (용사, 슬라임, 아이템…)
- 클래스 안에 **속성**은 필드(Field)로, **동작**은 메소드(Method)로 적어요.
- `new`로 인스턴스를 만들고 `변수.필드`, `변수.메소드()`로 써요.
- 이름 규칙: 클래스는 `Hero` (첫 글자 대문자), 필드·메소드는 `findWeakPoint` (두 번째 단어부터 대문자)
- `final` 필드는 값이 안 바뀌는 상수(Constant), `this`는 "나 자신"

```java
// Hero.java
public class Hero {
    String name;
    int hp;
    final int MAX_HP = 100;   // 상수: 못 바꿈

    void attack() {
        System.out.println(this.name + "의 공격!");
    }
}

Hero h = new Hero();   // 붕어빵 굽기
h.name = "용사";
h.hp = 100;
h.attack();            // 용사의 공격!
```

### STAGE 02 · 참조(Reference) · 생성자(Constructor) · static

_수업 자료 · 9장 클래스_

> **한 줄 요약** 클래스 타입 변수엔 인스턴스가 아니라 인스턴스가 있는 "주소"가 들어 있어요.

- 기본형(Primitive Type, `int`, `double`, `boolean`)은 값을, 참조형(Reference Type, `Hero`, `String`)은 주소를 저장해요.
- 참조형 변수를 다른 변수에 대입하면 **주소만 복사** → 둘이 같은 인스턴스를 가리켜요.
- 생성자 = 클래스 이름과 같고 리턴 타입이 없는 메소드. `new` 직후 자동 실행!
- 생성자를 하나도 안 만들면 컴파일러가 기본 생성자를 만들어 줘요.
- 오버로드(Overload): 매개변수가 다르면 같은 이름 여러 개 OK. `this(...)`로 다른 생성자를 재사용
- `static` = 클래스에 딱 1개, 모두가 공유. static 메소드 안에선 static 아닌 멤버를 못 써요.
- 필드 기본값: 숫자 0, boolean false, String·배열 null

```java
// Cleric.java
public class Cleric {
    static final int MAX_HP = 50;   // 모든 성직자가 공유
    String name;
    int hp;

    Cleric(String name, int hp) {
        this.name = name;
        this.hp = hp;
    }

    Cleric(String name) {
        this(name, MAX_HP);   // 위 생성자 재사용
    }
}

Cleric c1 = new Cleric("아서스");
Cleric c2 = c1;               // 주소만 복사!
c2.hp = 10;
System.out.println(c1.hp);    // 10
```

### STAGE 03 · 캡슐화(Encapsulation) · getter / setter

_수업 자료 · 10장 캡슐화_

> **한 줄 요약** 중요한 값은 금고(private)에 넣고, 정해진 창구(getter/setter)로만 꺼내요.

| 접근 제어자 | 누가 쓸 수 있나 |
|---|---|
| `private` | 내 클래스 안에서만 |
| (안 씀) | 같은 패키지 |
| `protected` | 같은 패키지 + 상속받은 자식 |
| `public` | 어디서나 |

- 캡슐화의 정석: 클래스 `public`, 메소드 `public`, 필드 `private`
- getter는 읽기, setter는 쓰기 담당
- setter 안에서 **이상한 값을 막는 것**이 캡슐화의 진짜 이유!
- 잘못된 값이면 `throw new IllegalArgumentException("메시지")`로 멈춰요.

```java
// Wand.java
public class Wand {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        if (name == null || name.length() < 3) {
            throw new IllegalArgumentException("이름은 3글자 이상!");
        }
        this.name = name;
    }
}
```

### STAGE 04 · 상속(Inheritance) · 오버라이드(Override) · super

_수업 자료 · 11장 상속_

> **한 줄 요약** 부모 것을 그대로 물려받고, 새로 생긴 부분만 적으면 돼요. 복붙은 이제 그만!

- `extends`로 상속해요. Java는 부모가 **딱 1명** (다중 상속 Multiple Inheritance 금지)
- 오버라이드: 부모 메소드를 자식이 다시 작성 (`@Override` 붙이기)
- `super.메소드()`로 부모 기능 호출. 생성자 첫 줄엔 `super()`가 자동으로 들어가요.
- `final` 클래스는 상속 불가, `final` 메소드는 오버라이드 불가
- 올바른 상속은 **is-a**: "자식은 부모의 한 종류다"가 말이 되어야 해요.

```java
// PoisonSlime.java
public class Slime {
    public void attack() {
        System.out.println("슬라임의 몸통 박치기!");
    }
}

public class PoisonSlime extends Slime {
    private int poisonCount = 5;

    @Override
    public void attack() {
        super.attack();   // 부모 공격 먼저
        if (poisonCount > 0) {
            System.out.println("추가로, 독 포자를 살포했다!");
            poisonCount--;
        }
    }
}
```

### STAGE 05 · 추상 클래스(Abstract Class) · 인터페이스(Interface)

_수업 자료 · 12장 추상클래스와 인터페이스_

> **한 줄 요약** 추상 클래스는 미완성 설계도, 인터페이스는 "이건 꼭 할 줄 알아야 해" 약속서예요.

- `abstract` 메소드는 내용 없이 이름만! 하나라도 있으면 클래스도 `abstract`
- 추상 클래스는 `new` 불가. 자식은 추상 메소드를 **반드시** 오버라이드 → 깜빡하거나 오타 내면 바로 에러로 알려 줘요.
- 인터페이스 메소드는 자동으로 `public abstract`, 필드는 자동으로 상수(`public static final`)
- 클래스 → 클래스: `extends` (1개만) / 인터페이스 → 클래스: `implements` (여러 개 OK)
- 인터페이스 이름은 "~할 수 있는" 느낌으로: `Drawable`, `Attackable`

```java
// Wizard.java
public abstract class Character {
    String name;
    int hp;

    public abstract void attack();   // 내용은 자식이 채움
}

public interface Magical {
    void fireball();
}

public class Wizard extends Character implements Magical {
    @Override
    public void attack() {
        System.out.println("지팡이로 때렸다!");
    }

    @Override
    public void fireball() {
        System.out.println("파이어볼!");
    }
}
```

### STAGE 06 · 다형성(Polymorphism)

_수업 자료 · 13장 다형성_

> **한 줄 요약** 손흥민은 "사람"이라고도, "축구선수"라고도 부를 수 있어요. 객체도 마찬가지!

- 부모(또는 인터페이스) 타입 변수에 자식 인스턴스를 담을 수 있어요.
- 공식: **선언은 넓게, 생성은 구체적으로** → `Drawable d = new Circle();`
- 상자(변수) 타입 = 부를 수 있는 메소드 결정 / 내용(인스턴스) 타입 = 실제 동작 결정
- 서로 다른 클래스를 한 배열에 담아 반복문 한 번으로 처리 → 코드 중복 싹!
- 다시 자식 타입으로 바꿀 땐 `instanceof`로 확인 후 캐스트. 틀리면 `ClassCastException`

```java
// Polymorphism.java
public interface Drawable {
    void draw();
}

public class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("원 그리기");
    }
}

public class Rect implements Drawable {
    @Override
    public void draw() {
        System.out.println("사각형 그리기");
    }
}

Drawable[] shapes = { new Circle(), new Rect() };
for (Drawable s : shapes) {
    s.draw();   // 각자 다르게 동작!
}
```

## PART 2 · 테스트

_수업 자료: 8-2, 9-2장_

### STAGE 07 · 단위 테스트(Unit Test)

_수업 자료 · 8-2장 Test 코드 작성 · 9-2장 Test 이론_

> **한 줄 요약** 코드를 다 짰다고 끝이 아니에요. 테스트로 "진짜 잘 된다"를 증명해야 완성!

- 수동 테스트(Manual Test, print로 확인) → 단위 테스트(메소드 1개) → 통합 테스트(Integration Test, 여러 개 함께)
- test 폴더의 같은 위치에 `클래스이름Test` 파일을 만들어요.
- 순서는 **Given(준비) → When(실행) → Then(검증)**, 검증은 `assertEquals`, `assertTrue`
- 객체 상태를 바꾸는 메소드가 테스트 대상이에요.
- 좋은 테스트 기법: 동등 분할(Equivalence Partitioning, 그룹마다 대표값 하나), 경계값 분석(Boundary Value Analysis, 0, -1, 최댓값 같은 끝자락)
- 화이트박스(White-box) = 코드 속을 보며 검사 / 블랙박스(Black-box) = 입력과 결과만 보고 검사
- 예외가 나야 정상인 경우는 `assertThrows`로 확인

```java
// SlimeTest.java
class SlimeTest {
    @Test
    @DisplayName("피해를 입으면 hp가 줄어든다")
    void takeDamage() {
        // Given
        Slime slime = new Slime();
        slime.hp = 50;

        // When
        slime.takeDamage(10);

        // Then
        assertEquals(40, slime.hp);
    }

    @Test
    @DisplayName("hp는 0 아래로 내려가지 않는다")
    void hpNotNegative() {
        Slime slime = new Slime();
        slime.hp = 5;
        slime.takeDamage(100);   // 경계값 테스트
        assertEquals(0, slime.hp);
    }
}
```

## PART 3 · 자바 응용

_수업 자료: 응용 1~4장 + 자료구조_

### STAGE 08 · 문자열(String) 다루기

_수업 자료 · 응용 1장 문자열 조작_

> **한 줄 요약** 문자열은 붙이고, 자르고, 바꾸고, 쪼개고, 찾을 수 있어요.

| 메소드 | 하는 일 ("HELLO" 기준) |
|---|---|
| `substring(0, 2)` | 자르기 → HE |
| `replace("L", "X")` | 바꾸기 → HEXXO |
| `split(",")` | "1,2,3" → 1 / 2 / 3 |
| `toLowerCase()` | 소문자로 → hello |
| `indexOf("E")` | 위치 찾기 → 1 (0부터 셈!) |
| `contains / endsWith` | 들어 있나? / 이걸로 끝나나? |
| `length / isEmpty` | 길이 / 비었나? |

- 내용 비교는 `==` 말고 `equals()`!
- String은 **불변(Immutable)** → `+`로 계속 붙이면 매번 새 객체가 생겨 느려요. 반복해서 붙일 땐 `StringBuilder`
- `String.format`: `%s` 문자열, `%d` 정수, `%.2f` 소수 둘째 자리, `%,d` 콤마, `%05d` 0 채우기

```java
// StringDemo.java
String s = "HELLO";
System.out.println(s.substring(0, 2));     // HE
System.out.println(s.replace("L", "X"));   // HEXXO

StringBuilder sb = new StringBuilder();
for (int i = 1; i <= 5; i++) {
    sb.append(i).append(",");   // 메서드 체인
}
System.out.println(sb.toString());   // 1,2,3,4,5,

String msg = String.format("%s 입문 %d일", "Java", 14);
// Java 입문 14일
```

#### 더 알아보기: trim / toUpperCase / toLowerCase / indexOf / endsWith

| 메소드 | 뜻 | 결과 종류 |
|---|---|---|
| `trim()` | 앞뒤 공백 지우기 (가운데 공백은 그대로) | 문자열 |
| `toUpperCase()` / `toLowerCase()` | 대문자로 / 소문자로 | 문자열 |
| `indexOf()` | 처음 나오는 위치 (0부터 셈, 없으면 -1) | 숫자 |
| `endsWith()` | 이걸로 끝나나? (반대는 `startsWith()`) | true / false |

```java
"  Hello Java  ".trim();          // "Hello Java"
"Hello".toUpperCase();            // "HELLO"
"Hello".toLowerCase();            // "hello"
"HELLO".indexOf("L");             // 2 (처음 나온 L)
"HELLO".lastIndexOf("L");         // 3 (뒤에서부터 찾기)
"HELLO".indexOf("Z");             // -1 (없음)
"photo.jpg".endsWith(".jpg");     // true
```

### STAGE 09 · 정규표현식(Regular Expression)

_수업 자료 · 응용 1장 정규표현식_

> **한 줄 요약** "이런 모양의 글자"를 기호로 적는 규칙이에요. 아이디·비번 검사에 딱!

| 기호 | 뜻 · 예시 |
|---|---|
| `.` | 아무 글자 1개 · j.p → jap, jsp |
| `*` | 앞 글자 0번 이상 · ab* → a, ab, abb |
| `{n,m}` | n~m번 반복 · a{2,3} → aa, aaa |
| `[ ]` | 괄호 속 중 1개 · UR[LIN] → URL, URI, URN |
| `[A-Z]` | 범위 속 1개 · 대문자 하나 |
| `^ / $` | 맨 앞 / 맨 뒤 · ^j.*p$ |

- `matches()`로 모양 검사, `split()`으로 쪼개기, `replaceAll()`로 바꾸기
- 연습 사이트: regexr.com 에서 직접 넣어 보면 금방 익숙해져요.

```java
// RegexDemo.java
// 첫 글자 대문자 + 대문자/숫자 7개 = 총 8자리
boolean ok = "A1B2C3D4".matches("[A-Z][A-Z0-9]{7}");   // true

String[] words = "abc,def:ghi".split("[,:]");   // abc, def, ghi
String r = "a1b2c3".replaceAll("[0-9]", "#");   // a#b#c#
```

### STAGE 10 · 컬렉션(Collection) · List / Set / Map

_수업 자료 · 응용 2장 컬렉션_

> **한 줄 요약** 배열은 크기가 고정, 컬렉션은 넣는 만큼 쭉쭉 늘어나요.

| 종류 | 특징 · 언제 쓰나 |
|---|---|
| `List` | 순서대로, 중복 OK · 순서가 중요할 때 |
| `Set` | 순서 없음, 중복 X · 중복 제거, 빠른 검색 |
| `Map` | key-value 쌍, key 중복 X · 이름으로 값 찾기 |

- 컬렉션엔 `int` 같은 기본형을 못 넣어요 → `List<Integer>`처럼 래퍼 클래스(Wrapper Class) 사용
- Set은 `get()`이 없어서 for-each로 꺼내요. `contains`는 List보다 훨씬 빨라요.
- HashMap은 순서를 보장하지 않아요 (출력 순서가 바뀔 수 있음)
- 섞어 쓰기도 가능: `Map<String, List<String>>`

```java
// CollectionDemo.java
List<String> names = new ArrayList<>();
names.add("홍길동");
names.add("홍길동");      // 중복 OK → 2개

Set<String> colors = new HashSet<>();
colors.add("red");
colors.add("red");        // 중복 무시 → 1개

Map<String, Integer> ages = new HashMap<>();
ages.put("홍길동", 20);
ages.put("한석봉", 25);
for (String key : ages.keySet()) {
    System.out.println(key + "의 나이는 " + ages.get(key) + "살");
}
```

### STAGE 11 · Object 3형제 · 정렬(Sorting) · 복사(Copy)

_수업 자료 · 응용 3장 인스턴스 기본 조작_

> **한 줄 요약** 모든 클래스의 조상은 Object! toString · equals · hashCode를 물려받아요.

- `toString()`: println 했을 때 보이는 글자를 정해요.
- `equals()`: "내용이 같으면 같은 거야" 규칙 → List의 `contains`, `remove`가 사용
- `hashCode()`: Set·Map이 빠르게 찾을 때 사용 → **equals를 바꾸면 hashCode도 같이!**
- 같은 객체는 항상 같은 해시값. 하지만 해시값이 같다고 꼭 같은 객체는 아니에요.
- 정렬: `Comparable`(클래스 안에 기본 규칙) / `Comparator`(그때그때 즉석 규칙). 작으면 음수, 같으면 0, 크면 양수
- 얕은 복사(Shallow Copy) = 안에 든 객체는 주소만 공유 / 깊은 복사(Deep Copy) = 안에 든 객체까지 새로 만들기

```java
// Book.java
public class Book implements Comparable<Book> {
    String title;
    int year;

    @Override
    public String toString() {
        return "Book(" + title + ", " + year + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Book b)) {
            return false;
        }
        return year == b.year && Objects.equals(title, b.title);
    }

    @Override
    public int hashCode() {
        return Objects.hash(title, year);
    }

    @Override
    public int compareTo(Book other) {
        return other.year - this.year;   // 최신순
    }
}

Collections.sort(books);   // compareTo 규칙대로 정렬
```

### STAGE 12 · 제네릭(Generic) · 열거형(enum)

_수업 자료 · 응용 4장 제네릭, 열거형_

> **한 줄 요약** 제네릭은 이름표 붙은 상자, enum은 정해진 보기 중에서만 고르는 객관식이에요.

- 타입이 없으면 실행 중에 에러가 나기 쉽고, IDE가 실수를 미리 못 잡아요.
- `<T>`로 타입을 나중에 정해요 → 꺼낼 때 캐스트가 필요 없고 **타입 안전(Type Safety)**
- enum = 정해 둔 값만 허용 → 오타나 엉뚱한 숫자 상수를 막아 줘요.
- enum도 필드와 생성자를 가질 수 있어요.

```java
// StrongBox.java
public class StrongBox<T> {
    private T item;

    public void put(T item) {
        this.item = item;
    }

    public T get() {
        return item;
    }
}

public enum KeyType {
    PADLOCK(1024), BUTTON(10000), DIAL(30000), FINGER(1000000);

    private final int limit;

    KeyType(int limit) {
        this.limit = limit;
    }
}

StrongBox<String> box = new StrongBox<>();
box.put("다이아몬드");
String gem = box.get();   // 캐스트 필요 없음!
```

### STAGE 13 · 기초 자료구조(Data Structure)

_수업 자료 · 기초 자료구조_

> **한 줄 요약** 스택은 접시 쌓기(위에서부터 꺼냄), 큐는 급식 줄(먼저 온 사람 먼저)이에요.

| 자료구조 | 핵심 · 특징 |
|---|---|
| 배열(Array) | 번호(인덱스)로 바로 접근 · 빠르지만 크기 고정 |
| 연결 리스트(Linked List) | 노드끼리 줄줄이 연결 · 넣고 빼기 빠름, 찾기는 느림 |
| 스택(Stack) | LIFO 나중에 넣은 게 먼저 · 실행 취소 |
| 큐(Queue) | FIFO 먼저 넣은 게 먼저 · 대기열 |

- 상황에 맞는 자료구조를 고르는 게 프로그램 속도를 좌우해요.

```java
// StackQueue.java
Deque<String> stack = new ArrayDeque<>();
stack.push("A");
stack.push("B");
System.out.println(stack.pop());    // B (나중에 넣은 게 먼저)

Queue<String> queue = new LinkedList<>();
queue.offer("A");
queue.offer("B");
System.out.println(queue.poll());   // A (먼저 넣은 게 먼저)
```

## PART 4 · Git & GitHub

_수업 자료: 버전 관리_

### STAGE 14 · Git & GitHub

_수업 자료 · Git & GitHub 기초_

> **한 줄 요약** Git은 코드의 세이브 포인트, GitHub는 그 세이브 파일을 올려 두는 클라우드예요.

- 로컬 저장소(Local Repository, 내 컴퓨터) ↔ 리모트 저장소(Remote Repository, GitHub)
- 기본 흐름: 수정 → `add`(올릴 파일 고르기) → `commit`(기록) → `push`(업로드)
- `clone` = 처음 통째로 복사, `pull` = 최신 내용으로 맞추기
- 처음 한 번만 `git config`로 이름·이메일 설정
- `.gitignore`에 적은 파일은 기록에서 빠져요.
- 커밋 메시지 규칙: `feat` 기능 추가, `fix` 버그 수정, `docs` 문서, `refactor` 정리, `test` 테스트
- `reset --hard`는 그 뒤 기록이 사라지니까 조심!
- 내 닉네임과 같은 이름의 저장소에 README.md를 만들면 GitHub 프로필 대문이 돼요.

```bash
# terminal
git config --global user.name "내이름"   # 처음 1번
git init                      # 지금 폴더를 저장소로
git status                    # 뭐가 바뀌었나 확인
git add .                     # 바뀐 파일 전부 담기
git commit -m "feat: 슬라임 공격 추가"
git push                      # GitHub에 올리기

git clone <주소>              # 남의 저장소 처음 복사
git pull                      # 최신 내용 받기
git log                       # 기록 보기
git reset --hard <커밋ID>     # 그 시점으로 되돌리기 (주의!)
```

---

# 2학기 · C# 실무 패턴

데이터 흐름: **DataSource**(창고) → **Repository**(주문 창구) → **Service**(규칙 심판) → 화면

**스테이지 목록**: 01. 예외 처리(Exception Handling) · 02. 파일 조작(File I/O) · 03. 데이터 형식(Data Format) · 직렬화(Serialization) · 04. 람다식(Lambda Expression) · 델리게이트(Delegate) · 05. 고계 함수(Higher-Order Function) · LINQ · 06. 동기(Synchronous) · 비동기(Asynchronous) · 병렬(Parallelism) · 07. async / await · Task.WhenAll · 08. HTTP · REST API · 09. TCP vs UDP · 10. DataSource · 11. Model · Repository · 12. 테스트 심화 · TDD(Test-Driven Development) · Mock · 13. DTO(Data Transfer Object) · Mapper · 14. Result 패턴(Result Pattern) · 15. 응집도(Cohesion) · 결합도(Coupling) · 16. 코드 설계 원칙(Design Principles) 6가지 · 17. SOLID 원칙(SOLID Principles) · 18. 디자인 패턴(Design Pattern) · 19. Service 계층(Service Layer) · 아키텍처(Architecture)

## PART 1 · C# 기본기

_수업 자료: 09~10장 예외, 파일, JSON, 람다, LINQ_

### STAGE 01 · 예외 처리(Exception Handling)

_수업 자료 · C# 09-1 예외_

> **한 줄 요약** 예외 처리는 게임이 꺼지는 대신 "다시 시도" 버튼을 띄워 주는 일이에요.

| 에러 종류 | 언제 알 수 있나 |
|---|---|
| 문법 에러(Syntax Error) | 컴파일하면 바로 빨간 줄 |
| 런타임 에러(Runtime Error) | 실행 중에 갑자기 멈춤 |
| 논리 에러(Logic Error) | 실행은 되는데 값이 이상함 |

- 예외 처리로 막는 건 주로 **런타임 에러** (파일 없음, 0으로 나누기, 인터넷 끊김)
- `try` 안에서 문제가 생기면 바로 `catch`로 넘어가요.
- `finally`는 성공하든 실패하든 **항상** 실행돼요.
- `throw`로 예외를 직접 던질 수 있고, `Exception`을 상속해 나만의 예외도 만들어요.

```csharp
// Program.cs
int num = 0;
try {
    int result = 10 / num;   // 0으로 나누기!
    Console.WriteLine(result);
} catch (DivideByZeroException e) {
    Console.WriteLine("0으로 나눌 수 없어요: " + e.Message);
    num = 0;
} finally {
    Console.WriteLine("항상 실행됨");
}

// 나만의 예외 만들기
public class NotEnoughMpException : Exception {
    public NotEnoughMpException(string message) : base(message) {
    }
}
```

### STAGE 02 · 파일 조작(File I/O)

_수업 자료 · C# 09-2 파일 조작_

> **한 줄 요약** 파일은 공책처럼 다뤄요. 펴고, 읽거나 쓰고, 덮기!

- 기본 순서: 파일 열기 → 읽기 또는 쓰기 → 닫기
- `File.WriteAllText(경로, 내용)`으로 쓰고 `File.ReadAllText(경로)`로 읽어요.
- `File.Exists(경로)`로 파일이 있는지 먼저 확인하면 안전해요.
- 파일이 없거나 권한이 없을 수 있으니 try-catch와 같이 써요.
- `File.Copy(원본, 사본)`으로 복사도 한 줄이면 끝!

```csharp
// FileDemo.cs
string path = "save.txt";

File.WriteAllText(path, "레벨 10, 골드 500");   // 쓰기

if (File.Exists(path)) {
    string text = File.ReadAllText(path);       // 읽기
    Console.WriteLine(text);
}

try {
    File.Copy("save.txt", "backup.txt");        // 복사
} catch (IOException e) {
    Console.WriteLine("복사 실패: " + e.Message);
}
```

### STAGE 03 · 데이터 형식(Data Format) · 직렬화(Serialization)

_수업 자료 · C# 09-3 여러가지 데이터 형식_

> **한 줄 요약** 직렬화는 객체를 택배 상자(JSON)에 포장하는 것, 역직렬화는 상자를 풀어 다시 객체로 만드는 거예요.

| 형식 | 생김새 · 특징 |
|---|---|
| `CSV` | 값을 콤마로 구분 · 엑셀 같은 표 |
| `XML` | <태그>로 감싸기 · 포함 관계 표현 |
| `JSON` | {객체} [리스트] key:value · 통신 표준, 가벼움 |

- 직렬화(Serialization): 객체 → JSON 문자열 / 역직렬화(Deserialization): JSON → 객체
- C#은 `System.Text.Json`의 `JsonSerializer`를 써요.
- 직렬화가 안 되는 것: `static` 멤버, `private` 멤버, 서로를 계속 가리키는 순환 참조(Circular Reference)
- 옵션을 주면 JSON 표준처럼 소문자로 시작하는 이름(카멜케이스 camelCase)으로 바꿀 수 있어요.

```csharp
// JsonDemo.cs
public class User {
    public string Name { get; set; } = "";
    public string Email { get; set; } = "";
}

var user = new User { Name = "John", Email = "john@example.com" };

// 직렬화: 객체 → JSON
string json = JsonSerializer.Serialize(user);
// {"Name":"John","Email":"john@example.com"}

// 역직렬화: JSON → 객체
User? loaded = JsonSerializer.Deserialize<User>(json);
Console.WriteLine(loaded?.Name);   // John
```

### STAGE 04 · 람다식(Lambda Expression) · 델리게이트(Delegate)

_수업 자료 · C# 10-2 람다식과 함수_

> **한 줄 요약** 람다는 이름 없는 즉석 함수예요. "이거 넣으면 → 이게 나와"를 한 줄로!

- 1급 객체(First-class Object) = 변수에 담을 수 있는 것: 값, 인스턴스, 그리고 **함수**도!
- 델리게이트 = 메서드를 담는 변수 타입. 결과가 있으면 `Func`, 없으면 `Action`
- `=>` 왼쪽은 매개변수, 오른쪽은 실행할 코드
- 메서드는 클래스에 속하고 이름이 있어요. 함수는 이름이 없어도 돼요(익명 함수 Anonymous Function).
- C#은 객체지향과 함수형을 함께 쓰는 멀티 패러다임(Multi-paradigm) 언어예요.

```csharp
// Lambda.cs
// 일반 메서드
int Add(int a, int b) {
    return a + b;
}

// 같은 일을 하는 람다식
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(10, 20));   // 30

// 리턴값이 없으면 Action
Action<string> hello = name => Console.WriteLine("안녕, " + name);
hello("용사");                     // 안녕, 용사

// 함수를 값처럼 넘기기
var list = new List<int> { 1, 2, 3 };
list.ForEach(x => Console.WriteLine(x));
```

### STAGE 05 · 고계 함수(Higher-Order Function) · LINQ

_수업 자료 · C# 10-2 고계 함수_

> **한 줄 요약** 고계 함수는 "함수를 재료로 받는 함수"예요. for + if를 한 줄로 줄여 줘요.

| 함수 | 하는 일 |
|---|---|
| `Where` | 조건에 맞는 것만 거르기 |
| `Select` | 다른 모양으로 바꾸기 |
| `Any` | 하나라도 있나? (true/false) |
| `Aggregate` | 하나가 될 때까지 합치기 |
| `OrderBy` | 정렬하기 |
| `ToList / ToHashSet` | 리스트로 / 중복 없는 Set으로 |

- 결과는 보통 `IEnumerable`이라서 `ToList()`로 바꿔 써요.
- `ToHashSet()`을 쓰면 중복이 싹 사라져요.
- 이어 붙여 쓸 수 있어요: `Where(...).Select(...).ToList()`

```csharp
// LinqDemo.cs
var scores = new List<int> { 90, 45, 70, 100, 30 };

// Where: 60점 이상만
var pass = scores.Where(s => s >= 60).ToList();     // 90, 70, 100

// Select: 모두 +5점
var bonus = scores.Select(s => s + 5).ToList();     // 95, 50, 75, 105, 35

// Any: 100점이 있나?
bool perfect = scores.Any(s => s == 100);           // true

// Aggregate: 전부 더하기
int sum = scores.Aggregate((a, b) => a + b);        // 335

// 이어서 쓰기: 합격 점수를 높은 순으로
var top = scores
    .Where(s => s >= 60)
    .OrderByDescending(s => s)
    .ToList();                                      // 100, 90, 70
```

## PART 2 · 비동기 & 네트워크

_수업 자료: 11장, 14장_

### STAGE 06 · 동기(Synchronous) · 비동기(Asynchronous) · 병렬(Parallelism)

_수업 자료 · C# 11 비동기 프로그래밍_

> **한 줄 요약** 동기는 라면 물이 끓을 때까지 멍하니 기다리기, 비동기는 그동안 계란 까기!

| 개념 | 뜻 |
|---|---|
| 동기(Sync) | 순서대로, 앞 일이 끝나야 다음 일 |
| 비동기(Async) | 안 기다리고 다른 일 먼저 진행 |
| 동시성(Concurrency) | 혼자 번갈아 처리 (싱글 코어, 선생님 1명) |
| 병렬성(Parallelism) | 진짜 동시에 처리 (멀티 코어, 선생님 + 조교) |

- 비동기의 핵심: **메인 스레드(Main Thread)를 절대 막지 않는다** → 다운로드 중에도 화면이 버벅이지 않아요.
- 파일·네트워크 같은 무거운 I/O 작업은 비동기가 기본이에요.
- `Task<T>` = 나중에 결과가 나올 작업
- 옛날 방식 `ContinueWith()` 콜백은 겹겹이 쌓이면 **콜백 지옥(Callback Hell)**이 되고 try-catch도 못 써요.

```csharp
// SyncAsync.cs
// 동기: 다 쓸 때까지 여기서 멈춤
File.WriteAllText("hero.json", json);
Console.WriteLine("저장 끝");

// 비동기 + 콜백: 끝나면 실행 (중첩되면 콜백 지옥!)
File.WriteAllTextAsync("hero.json", json)
    .ContinueWith(task => {
        Console.WriteLine("저장 끝");
    });
Console.WriteLine("저장 기다리는 동안 다른 일");
```

### STAGE 07 · async / await · Task.WhenAll

_수업 자료 · C# 11 비동기 프로그래밍_

> **한 줄 요약** await는 "여기서 잠깐 기다려 줘" 표시예요. 비동기 코드를 위에서 아래로 읽히게 해 줘요.

- 메서드 앞에 `async`, 기다릴 작업 앞에 `await`
- 비동기 메서드는 `Task`나 `Task<T>`를 리턴하고, 이름 끝에 `Async`를 붙이는 게 관례
- 평범한 `try-catch`로 에러 처리가 돼요.
- `Task.WhenAll()`은 여러 작업을 **동시에 시작**하고 모두 끝날 때까지 기다려요.
- 유니티에서는 코루틴 대신 `UniTask`를 많이 써요.

```csharp
// AsyncDemo.cs
async Task<string> LoadAsync(string name, int sec) {
    await Task.Delay(sec * 1000);   // sec초 기다리기
    return name + " 로딩 완료";
}

// 하나씩 기다리면: 1 + 2 + 3 = 6초
string a = await LoadAsync("맵", 1);
string b = await LoadAsync("캐릭터", 2);
string c = await LoadAsync("사운드", 3);

// 동시에 시작하면: 가장 긴 3초면 끝!
string[] all = await Task.WhenAll(
    LoadAsync("맵", 1),
    LoadAsync("캐릭터", 2),
    LoadAsync("사운드", 3));

try {
    await LoadAsync("보스", 1);
} catch (Exception e) {
    Console.WriteLine("로딩 실패: " + e.Message);
}
```

### STAGE 08 · HTTP · REST API

_수업 자료 · C# 14 네트워크 통신_

> **한 줄 요약** HTTP는 편지 주고받기예요. 요청 편지 1통을 보내면 답장 1통이 꼭 와요.

| 메서드 / 코드 | 뜻 |
|---|---|
| `GET` | 데이터 읽기 (주소 뒤에 ?id=1&name=a) |
| `POST` | 데이터를 담아 보내기 (로그인, 주문) |
| `PUT / PATCH` | 전체 수정 / 일부 수정 |
| `DELETE` | 삭제 |
| `200 / 404 / 500` | 성공 / 찾을 수 없음 / 서버 오류 |

- HTTP는 **상태를 기억하지 않는**(Stateless) 프로토콜 → 세션(Session, 서버)과 쿠키(Cookie, 클라이언트)로 기억을 보충해요.
- 응답이 너무 늦으면 실패로 처리해요(타임아웃 Timeout). Android 10초, iOS 60초
- RESTful API = 주소(URL) + 메서드(GET, POST…) + JSON으로 약속한 통신 규칙
- C#은 `HttpClient`, 유니티는 `UnityWebRequest`, 테스트 도구는 Postman

```csharp
// HttpDemo.cs
var client = new HttpClient();
string url = "https://jsonplaceholder.typicode.com/posts";

// GET: 1번 글 가져오기
var response = await client.GetAsync(url + "/1");

if (response.IsSuccessStatusCode) {        // 200번대면 성공
    string json = await response.Content.ReadAsStringAsync();
    Console.WriteLine(json);
} else {
    Console.WriteLine("에러: " + (int)response.StatusCode);   // 404 등
}

// POST: 새 글 보내기
string body = JsonSerializer.Serialize(new { title = "안녕" });
var content = new StringContent(body, Encoding.UTF8, "application/json");
await client.PostAsync(url, content);
```

### STAGE 09 · TCP vs UDP

_수업 자료 · C# 14 네트워크 통신_

> **한 줄 요약** TCP는 등기우편(꼭 도착하고 확인도 OK), UDP는 전단지 뿌리기(빠르지만 몇 장 잃어버려도 그만)예요.

| 방식 | 특징 · 어디에 쓰나 |
|---|---|
| `TCP` | 연결 유지, 도착 보장 · 이메일, 파일 전송, 웹 |
| `UDP` | 연결 없음, 도착 보장 X · 실시간 게임, 방송 |
| `HTTP` | 요청-응답 1:1, 상태 없음 · 로직 간단, TCP보다 느림 |

- TCP는 **Stateful**: 연결되면 끊을 때까지 계속 주고받고, 한쪽 문제를 다른 쪽이 알아챌 수 있어요.
- TCP는 HTTP보다 빠르지만 타임아웃이나 요청 구분을 개발자가 직접 만들어야 해요.
- UDP는 흐름 제어(Flow Control)가 없고 단순해서 여러 명에게 한 번에 보내기(멀티캐스트 Multicast, 브로드캐스트 Broadcast)에 좋아요.
- 소켓(Socket) = TCP/UDP를 쓰게 해 주는 저수준 API. IP 주소 + 포트 번호로 접속해요.

```csharp
// SocketDemo.cs
// TCP: IP 주소 + 포트로 연결한 뒤 보내기
using var tcp = new TcpClient();
await tcp.ConnectAsync("127.0.0.1", 7777);
var stream = tcp.GetStream();
byte[] data = Encoding.UTF8.GetBytes("hello");
await stream.WriteAsync(data);

// UDP: 연결 없이 그냥 던지기
using var udp = new UdpClient();
byte[] msg = Encoding.UTF8.GetBytes("ping");
await udp.SendAsync(msg, msg.Length, "127.0.0.1", 8888);
```

## PART 3 · 데이터 계층

_수업 자료: 12, 13, 15, 16장_

### STAGE 10 · DataSource

_수업 자료 · C# 12 DataSource_

> **한 줄 요약** DataSource는 요리 재료 창고예요. 재료(데이터)를 넣고 꺼내는 일만 해요.

- 데이터와 로직이 뒤섞이면 스파게티 코드(Spaghetti Code)! 능력치 하나 바꾸려 해도 다시 빌드해야 해요.
- DataSource = 앱이 쓰는 원천 데이터 (파일, JSON, CSV, DB, 서버…)
- 하는 일: 저장소와 직접 통신하며 **CRUD** (Create 만들기, Read 읽기, Update 고치기, Delete 지우기)
- 인터페이스로 기능만 정해 두면 파일 저장 → 서버 저장으로 바꾸기 쉬워요.
- 인터페이스 이름 앞엔 `I`를 붙이는 관례: `IUserDataSource`
- 유니티의 `PlayerPrefs`도 DataSource의 한 종류예요.

```csharp
// JsonFileUserDataSource.cs
public interface IUserDataSource {
    Task<List<User>> LoadUsersAsync();
    Task SaveUsersAsync(List<User> users);
}

public class JsonFileUserDataSource : IUserDataSource {
    private readonly string _path;

    public JsonFileUserDataSource(string path) {
        _path = path;
    }

    public async Task<List<User>> LoadUsersAsync() {
        string json = await File.ReadAllTextAsync(_path);
        return JsonSerializer.Deserialize<List<User>>(json) ?? new List<User>();
    }

    public async Task SaveUsersAsync(List<User> users) {
        string json = JsonSerializer.Serialize(users);
        await File.WriteAllTextAsync(_path, json);
    }
}
```

### STAGE 11 · Model · Repository

_수업 자료 · C# 13-1 Model, Repository_

> **한 줄 요약** DataSource가 창고라면 Repository는 주문 창구예요. "레벨 10 이상 플레이어 주세요" 같은 요청을 골라서 처리해요.

| 역할 | 하는 일 |
|---|---|
| `Model` | 데이터 모양만 정의 (순수, 불변) |
| `DataSource` | 저장 매체와 직접 대화 (파일, 서버) |
| `Repository` | 어떤 데이터를 줄지 골라서 전달 |

- Repository는 DataSource를 숨기고 위쪽 코드에 늘 같은 방식으로 데이터를 줘요.
- `GetPlayersByLevel()`처럼 게임에 맞는 조회 기능을 제공해요.
- 장점: 관심사 분리(Separation of Concerns), 테스트 쉬움(가짜 DataSource), 저장 방식 교체 쉬움
- 주의: 파일·서버 코드는 DataSource에, 복잡한 규칙은 더 위 계층(Service)에!
- DataSource는 생성자로 받아요 (의존성 주입 Dependency Injection).

```csharp
// PlayerRepository.cs
public record Player(string Id, string Name, int Level);   // Model

public class PlayerRepository : IPlayerRepository {
    private readonly IPlayerDataSource _dataSource;

    public PlayerRepository(IPlayerDataSource dataSource) { // 주입
        _dataSource = dataSource;
    }

    public async Task<List<Player>> GetPlayersByLevelAsync(int minLevel) {
        var players = await _dataSource.LoadPlayersAsync();
        return players.Where(p => p.Level >= minLevel).ToList();
    }
}
```

### STAGE 12 · 테스트 심화 · TDD(Test-Driven Development) · Mock

_수업 자료 · C# 13-0 Test 이론_

> **한 줄 요약** Mock은 영화의 스턴트 대역이에요. 진짜 서버 대신 가짜가 정해진 대답을 해 줘요.

- TDD: **테스트를 먼저** 쓰고, 그 테스트를 통과하도록 코드를 작성
- Test Double = 테스트하기 어려운 대상을 대신하는 가짜 객체. Mock은 원하는 결과를 돌려주는 가짜
- 서버·DB에 기대면 테스트가 느리고, 인터넷이 끊기면 실패해요.
- 해결: 클래스 안에서 `new` 하지 말고 **생성자로 인터페이스를 받기** → 테스트 때 Mock으로 교체
- 꼭 테스트할 것: 네트워크 끊김, 잘못된 데이터, 경계값
- 좋은 테스트 FRIEND: Fast, Reliable, Independent, Easy to maintain, Nearly compact, Dependencies less

```csharp
// PlayerRepositoryTest.cs
public class MockPlayerDataSource : IPlayerDataSource {
    public Task<List<Player>> LoadPlayersAsync() {
        var fake = new List<Player> {
            new Player("1", "용사", 5),
            new Player("2", "마법사", 12),
        };
        return Task.FromResult(fake);
    }
}

[Fact]
public async Task 레벨10이상만_가져온다() {
    // Given
    var repo = new PlayerRepository(new MockPlayerDataSource());

    // When
    var result = await repo.GetPlayersByLevelAsync(10);

    // Then
    Assert.Single(result);
    Assert.Equal("마법사", result[0].Name);
}
```

### STAGE 13 · DTO(Data Transfer Object) · Mapper

_수업 자료 · C# 15 Dto, Mapper_

> **한 줄 요약** DTO는 택배를 일단 받아 두는 검수대예요. 상자가 찌그러져(null) 있어도 앱이 터지지 않게 막아 줘요.

- 서버도 실수해요: 필드(Field)가 빠지거나, 숫자 자리에 글자가 오거나, null이 오거나…
- 흐름: **JSON → DTO → (Mapper) → Model**
- DTO: JSON을 있는 그대로 받는 그릇. 필드는 **전부 nullable**(`?`)
- Model: 앱에서 실제로 쓰는 깨끗한 객체. 필드에 null 없음
- Mapper: DTO → Model 변환, 빈 값은 기본값으로. **확장 메서드(Extension Method)**로 많이 만들어요.
- `?` null 허용 / `??` null이면 오른쪽 값 / `!` "null 아니야"라고 컴파일러에 장담

```csharp
// PokemonMapper.cs
public class PokemonDto {              // 전부 nullable
    public int? Id { get; set; }
    public string? Name { get; set; }
    public int? Height { get; set; }
}

public record Pokemon(int Id, string Name, int Height);   // Model

public static class PokemonMapper {
    public static Pokemon ToModel(this PokemonDto dto) {   // 확장 메서드
        return new Pokemon(
            dto.Id ?? 0,
            string.IsNullOrEmpty(dto.Name) ? "알 수 없음" : dto.Name,
            dto.Height ?? 0);
    }
}

Pokemon pikachu = dto.ToModel();
```

### STAGE 14 · Result 패턴(Result Pattern)

_수업 자료 · C# 16 Result 패턴_

> **한 줄 요약** Result는 시험 결과표예요. "합격 + 점수" 또는 "불합격 + 이유" 둘 중 하나가 꼭 들어 있어요.

- 서버 요청 실패는 종류가 많아요: 인터넷 없음, 타임아웃, 404, 이상한 값…
- try-catch만으로는 이걸 다 구분해서 넘기기 어려워요.
- Result = 성공이면 **데이터**, 실패면 **에러 정보**를 담아서 리턴
- `record` = 불변(Immutable) + Equals·ToString 자동 / `sealed`로 종류를 Success, Failure로 딱 정해요.
- `switch` 패턴 매칭(Pattern Matching)으로 모든 경우를 빠짐없이 처리
- 장점: 타입 안전(Type Safety), 에러 처리 누락 방지, 코드만 봐도 어떤 에러가 날지 보여요.

```csharp
// Result.cs
public enum PokemonError {
    NotFound,
    Timeout,
    Unknown
}

public abstract record Result<T> {
    public sealed record Success(T Data) : Result<T>;
    public sealed record Failure(PokemonError Error) : Result<T>;
}

Result<Pokemon> result = await repo.GetPokemonAsync("ditto");

switch (result) {
    case Result<Pokemon>.Success s:
        Console.WriteLine("찾았다! " + s.Data.Name);
        break;
    case Result<Pokemon>.Failure f:
        Console.WriteLine("실패: " + f.Error);
        break;
}
```

## PART 4 · 설계 원칙 & 패턴

_수업 자료: 17장_

### STAGE 15 · 응집도(Cohesion) · 결합도(Coupling)

_수업 자료 · C# 17 설계 원칙_

> **한 줄 요약** 좋은 클래스는 전문점이에요! 한 가지 일에 집중하고(응집도↑) 남에게 덜 기대요(결합도↓).

| 개념 | 뜻 · 목표 |
|---|---|
| 응집도(Cohesion) | 한 클래스가 한 목적에 집중하는 정도 · 높게 |
| 결합도(Coupling) | 다른 클래스에 기대는 정도 · 낮게 |

- 좋은 예: `UserAuthentication`은 로그인, 로그아웃, 비밀번호 변경만 담당
- 나쁜 예: `Util` 안에 문자열 처리, 파일 읽기, 날짜 계산이 뒤죽박죽
- 결합도 낮추기: 특정 클래스 말고 **인터페이스**를 통해 필요한 걸 받아요.

```csharp
// UserAuthentication.cs
// 높은 응집도: 로그인 관련 일만 모음
public class UserAuthentication {
    private readonly IUserRepository _users;   // 인터페이스에 의존 → 결합도 낮음

    public UserAuthentication(IUserRepository users) {
        _users = users;
    }

    public bool Login(string id, string pw) {
        return _users.CheckPassword(id, pw);
    }

    public void Logout() {
        Console.WriteLine("로그아웃");
    }
}

// 낮은 응집도 (피하기!)
// Util 클래스에 FormatString(), ReadFile(), CalculateDate()를 몰아넣기
```

### STAGE 16 · 코드 설계 원칙(Design Principles) 6가지

_수업 자료 · C# 17 설계 원칙_

> **한 줄 요약** 선배 개발자들이 고생하며 얻은 "이렇게 짜면 덜 고생한다" 규칙 모음이에요.

| 원칙 | 뜻 |
|---|---|
| `DRY` | Don't Repeat Yourself · 같은 코드 반복 금지 → 메서드로 분리 |
| `PIE` | Program Intently and Expressively · 알기 쉬운 이름, 매직 넘버에 이름 붙이기 |
| `SRP` | Single Responsibility Principle · 클래스 하나에 책임 하나 |
| `OCP` | Open-Closed Principle · 기존 코드 수정 없이 기능 추가 |
| `SDP` | Stable Dependencies Principle · 잘 안 바뀌는 것(인터페이스)에 의존 |
| `ADP` | Acyclic Dependencies Principle · 의존 관계가 빙글빙글 돌지 않게 |

- OCP 예: `Slime`, `Goblin`을 따로 때리지 말고 `Character`를 때려라 → 새 몬스터가 생겨도 Hero 코드는 그대로
- SDP 예: `Random`을 직접 쓰지 말고 감싼 인터페이스를 주입받기 → 테스트 때 원하는 숫자를 줄 수 있어요.
- 외부 객체는 생성자로 받아요 (의존성 주입).

```csharp
// Principles.cs
const int MaxHp = 100;   // PIE: 매직 넘버에 이름 붙이기

// OCP: 몬스터가 늘어도 Hero는 그대로
public abstract class Character {
    public int Hp { get; set; } = MaxHp;
}

public class Slime : Character {
}

public class Goblin : Character {
}

public class Hero {
    public void Attack(Character target) { // Slime, Goblin 모두 OK
        target.Hp -= 10;
    }
}

// SDP: 바뀔 수 있는 Random 대신 인터페이스에 의존
public interface IDice {
    int Roll();
}
```

### STAGE 17 · SOLID 원칙(SOLID Principles)

_수업 자료 · C# 17 SOLID 원칙_

> **한 줄 요약** 객체지향 설계의 기본기 5가지를 앞 글자만 따서 SOLID라고 불러요.

| 글자 | 원칙 |
|---|---|
| `S` | 단일 책임(Single Responsibility) · 클래스는 한 가지 일만 |
| `O` | 개방-폐쇄(Open-Closed) · 수정 없이 확장 |
| `L` | 리스코프 치환(Liskov Substitution) · 자식은 부모 자리에 들어가도 OK (is-a) |
| `I` | 인터페이스 분리(Interface Segregation) · 큰 인터페이스를 기능별로 쪼개기 |
| `D` | 의존 역전(Dependency Inversion) · 구체 클래스 말고 역할(인터페이스)에 의존 |

- I 예: `Bionic` 하나에 다 넣지 말고 `IAttackable`, `IMovable`, `IHealable`로 쪼개기
- D 예: 메딕은 특정 유닛이 아니라 `IHealable`을 치료해요.
- D 비유: 상사가 "김대리!" 대신 "보고서 담당자!"를 부르면 김대리든 박대리든 맡을 수 있어요.

```csharp
// Medic.cs
public interface IHealable {
    void Heal(int amount);
}

public class Marine : IHealable {
    public int Hp { get; private set; } = 40;

    public void Heal(int amount) {
        Hp += amount;
    }
}

public class Medic {
    // 특정 유닛이 아닌 "치료받을 수 있는 것"에 의존 (D)
    public void Treat(IHealable target) {
        target.Heal(10);
    }
}
```

### STAGE 18 · 디자인 패턴(Design Pattern)

_수업 자료 · C# 17 디자인 패턴_

> **한 줄 요약** 디자인 패턴은 자주 나오는 문제의 모범 답안 모음집이에요. (GoF 23가지 중 기본만!)

| 패턴 | 한 줄 설명 |
|---|---|
| 빌더(Builder) | 옵션을 하나씩 골라 조립 (서브웨이) |
| 팩토리(Factory) | 객체 만들기를 공장에 맡김 (맥도날드 주방) |
| 싱글턴(Singleton) | 인스턴스는 딱 1개만 (렌트카 회사) |
| 전략(Strategy) | 알고리즘을 갈아 끼우기 |
| 옵저버(Observer) | 상태가 바뀌면 구독자에게 알림 |
| 퍼사드(Facade) | 복잡한 내부를 간단한 창구 하나로 |

- 장점: 개발자끼리 말이 잘 통하고 객체지향 원칙도 더 잘 이해돼요.
- 게임 예: 옵저버로 플레이어가 죽으면 UI, 사운드, 업적을 한 번에 처리

```csharp
// GameManager.cs
public class GameManager {
    // 싱글턴: 어디서 불러도 같은 하나
    private static GameManager? _instance;
    public static GameManager Instance => _instance ??= new GameManager();

    private GameManager() { // 밖에서 new 금지
    }

    // 옵저버: 사망 이벤트를 구독
    public event Action? OnPlayerDied;

    public void PlayerDied() {
        OnPlayerDied?.Invoke();   // 구독자 모두에게 알림
    }
}

GameManager.Instance.OnPlayerDied += () => Console.WriteLine("UI: 게임 오버");
GameManager.Instance.OnPlayerDied += () => Console.WriteLine("사운드: 으악!");
GameManager.Instance.PlayerDied();
```

### STAGE 19 · Service 계층(Service Layer) · 아키텍처(Architecture)

_수업 자료 · C# 17 아키텍처 패턴_

> **한 줄 요약** 스타크래프트에서 마린 버튼을 누르면 "미네랄이 부족합니다"를 판단하는 심판이 바로 Service예요.

| 계층 | 하는 일 |
|---|---|
| `View` | 버튼과 화면, 입력과 표시만 |
| `Service` | 규칙 판단 (미네랄 50? 인구수?) |
| `Repository` | 필요한 데이터 골라 주기 |
| `DataSource` | 저장소와 직접 통신 |

- Service = 화면을 모르는 채로 **비즈니스 규칙(Business Rule)**을 적용하고 결과를 돌려주는 계층
- 하는 일: 입력 검증, 규칙 적용, 여러 Repository 조합
- 데이터 실패(NotFound, 네트워크)는 Repository가, 규칙 실패(이름 빈칸, 전설 포켓몬 금지)는 Service가 알려요.
- Service는 화면이 아니라 **도메인(Domain)**으로 나눠요: `HomeService` 대신 `WalletService`, `ProfileService`
- 큰 구조 패턴: MVC, MVP, MVVM, MVI, 클린 아키텍처

```csharp
// MarineService.cs
public class MarineService {
    private readonly IMineralRepository _minerals;

    public MarineService(IMineralRepository minerals) {
        _minerals = minerals;
    }

    public async Task<Result<Marine>> TrainAsync() {
        int mineral = await _minerals.GetAmountAsync();
        if (mineral < 50) {
            return new Result<Marine>.Failure(GameError.NotEnoughMinerals);
        }

        await _minerals.SpendAsync(50);
        return new Result<Marine>.Success(new Marine());
    }
}
```

