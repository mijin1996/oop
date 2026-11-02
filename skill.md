---
name: questbook-code-style
description: 수업에서 배운 자바·C# 원칙(캡슐화, SOLID, DataSource→Repository→Service, DTO·Mapper, Result 패턴, async/await, Given-When-Then 테스트)에 맞춰 코드를 짤 때 사용
---

# 규칙

1학기(자바·Git)와 2학기(C# 실무 패턴) 수업에서 배운 내용을 기준으로 코드를 작성한다. 사용자는 중학생이므로 코드는 수업에서 배운 문법 안에서 쓰고, 설명은 한국어로 짧고 쉽게 한다. 아래 예시 코드는 규칙을 보여 주는 기준이며, 실제 과제에 맞게 이름과 내용을 바꿔 쓴다.

## 0. 시작 전에 정하기

- **언어**: 사용자가 말한 언어를 따른다. 말이 없으면 Unity나 `.cs` 파일이 보이면 C#, `.java`면 자바로 작성한다.
- **규모**: 콘솔 연습 문제처럼 작은 과제는 클래스 몇 개로 끝낸다. 데이터를 읽고 저장하거나 서버와 통신하는 과제일 때만 계층 구조(2장)를 적용한다. 작은 과제에 계층을 억지로 만들지 않는다.
- Player, Enemy, Manager 등 각 기능에 맞게끔 코드 소스의 파일을 구분하여 정리한다.
- 기능(UI, Manager, Effect, Sound etc.)이 다양하니 각각의 스크립트를 구분하여 적용 작성한다.
- 코드와 파일을 중복이 없어야 한다.
- 구현 요청자가 애매한 요청을 했을 경우, 재 질문하여 의도를 파악한 후 프로젝트에 적용 및 코드를 작성한다.
- 

## 1. 모든 코드에 적용하는 기본 규칙

### 1-1. 중괄호와 이름

- **K&R 스타일**: 여는 중괄호 `{`는 같은 줄 끝에 쓴다(`if (x) {`). `else`, `catch`, `finally`는 닫는 중괄호와 같은 줄에 이어 쓴다(`} else {`).
- 클래스·인터페이스·enum 이름은 명사, 첫 글자 대문자(PascalCase).
- 인터페이스는 앞에 `I`를 붙이고(C#) "~할 수 있는" 느낌으로 짓는다.
- 자바 필드·메소드는 camelCase. C#은 메서드·프로퍼티는 PascalCase, private 필드는 `_camelCase`.
- 비동기 메서드 이름 끝에는 `Async`를 붙인다.
- 매직 넘버에는 이름을 붙인다 (PIE 원칙).

```csharp
public interface IAttackable {            // 인터페이스: I + "~할 수 있는"
    void TakeDamage(int amount);
}

public class MonsterInfo : IAttackable {  // 클래스: 명사, PascalCase
    private const int MaxHp = 100;        // 매직 넘버에 이름 붙이기
    private int _hp = MaxHp;              // private 필드: _camelCase

    public void TakeDamage(int amount) {  // 메서드: PascalCase
        _hp -= amount;
    }

    public async Task SaveAsync() {       // 비동기 메서드: 끝에 Async
        await Task.Delay(100);
    }
}
```

```java
public class MonsterInfo {
    private static final int MAX_HP = 100;   // 자바 상수: 대문자 + _
    private int hp = MAX_HP;                 // 자바 필드: camelCase

    public void findWeakPoint() {            // 자바 메소드: camelCase
        System.out.println("약점 발견!");
    }
}
```

### 1-2. 캡슐화 (Encapsulation)

- 클래스 `public`, 메서드 `public`, **필드 `private`**.
- 바깥에서 값을 바꿔야 하면 setter(자바) 또는 프로퍼티(C#)를 거치게 하고, 그 안에서 값을 검증한다.
- 잘못된 값은 예외로 막는다: `IllegalArgumentException`(자바) / `ArgumentException`(C#). HP처럼 "음수면 0"이 자연스러운 값은 0으로 고정한다.
- 한번 정해지면 안 바뀌는 값은 `final`(자바) / `readonly` 또는 `get;`만 있는 프로퍼티(C#)로 만든다.

```java
public class Wizard {
    private final String name;   // 한번 정하면 못 바꿈
    private int hp;

    public Wizard(String name, int hp) {
        if (name == null || name.length() < 3) {
            throw new IllegalArgumentException("이름은 3글자 이상!");
        }
        this.name = name;
        setHp(hp);
    }

    public String getName() {
        return name;
    }

    public int getHp() {
        return hp;
    }

    public void setHp(int hp) {
        if (hp < 0) {
            hp = 0;              // 음수면 0으로
        }
        this.hp = hp;
    }
}
```

```csharp
public class Wizard {
    public string Name { get; }    // 읽기만 가능
    private int _hp;

    public int Hp {
        get {
            return _hp;
        }
        set {
            _hp = value < 0 ? 0 : value;   // 음수면 0으로
        }
    }

    public Wizard(string name, int hp) {
        if (string.IsNullOrEmpty(name) || name.Length < 3) {
            throw new ArgumentException("이름은 3글자 이상!");
        }
        Name = name;
        Hp = hp;
    }
}
```

### 1-3. 상속과 다형성 (Inheritance, Polymorphism)

- 상속은 **is-a**가 말이 될 때만 쓴다("Slime은 Character의 한 종류"). 부품 관계(has-a)는 필드로 가진다.
- 공통 동작은 추상 클래스나 인터페이스로 묶고, "선언은 넓게, 생성은 구체적으로" 쓴다.
- 여러 종류는 부모 타입 리스트에 담아 반복문 하나로 처리한다. 종류별 `if-else` 분기를 늘리지 않는다(OCP).
- 오버라이드에는 `@Override`(자바) / `override`(C#)를 붙인다.

```csharp
public abstract class Character {               // 공통 부모 (추상 클래스)
    public string Name { get; }
    public int Hp { get; protected set; } = 50;

    protected Character(string name) {
        Name = name;
    }

    public abstract void Attack();              // 내용은 자식이 채움
}

public class Slime : Character {                // Slime is a Character (O)
    public Slime() : base("슬라임") {
    }

    public override void Attack() {
        Console.WriteLine("몸통 박치기!");
    }
}

public class Goblin : Character {
    private readonly Weapon _weapon;            // Goblin has a Weapon → 필드로

    public Goblin(Weapon weapon) : base("고블린") {
        _weapon = weapon;
    }

    public override void Attack() {
        Console.WriteLine(_weapon.Name + "(으)로 공격!");
    }
}

// 선언은 넓게(Character), 생성은 구체적으로(Slime, Goblin)
List<Character> monsters = new List<Character> {
    new Slime(),
    new Goblin(new Weapon("단검")),
};

foreach (Character m in monsters) {
    m.Attack();   // if-else 없이 각자 다르게 동작
}
```

### 1-4. Object 기본기 (toString · equals · hashCode · 정렬 · 복사)

- 값 객체는 `toString`, `equals`, `hashCode`를 함께 재정의한다. C#에서는 `record`를 쓰면 자동으로 된다.
- 정렬 기준은 `Comparable`/`compareTo`(기본 규칙) 또는 `Comparator`(즉석 규칙)로 만든다.
- 복사본이 원본과 완전히 따로 움직여야 하면 깊은 복사(Deep Copy)를 쓴다.

```java
public class Book implements Comparable<Book> {
    private final String title;
    private final int year;

    public Book(String title, int year) {
        this.title = title;
        this.year = year;
    }

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
        return other.year - this.year;   // 기본 정렬: 최신순
    }
}

Collections.sort(books);                              // compareTo 규칙
books.sort(Comparator.comparing(b -> b.toString()));  // 즉석 규칙
```

```csharp
// record: Equals, GetHashCode, ToString이 자동
public record Book(string Title, int Year);

public class Hero {
    public string Name { get; set; } = "";
    public Sword Sword { get; set; } = new Sword("목검");

    public Hero DeepCopy() {
        return new Hero {
            Name = Name,
            Sword = new Sword(Sword.Name),   // 안의 객체까지 새로 만들기
        };
    }
}
```

### 1-5. 컬렉션 · enum · 제네릭

- 순서가 중요하거나 중복 허용 → `List`
- 중복 제거, 빠른 검색 → `Set`
- 이름(key)으로 값 찾기 → `Map` / `Dictionary`
- 정해진 값만 허용 → `enum`
- 타입이 나중에 정해지는 그릇 → 제네릭 `<T>`

```csharp
List<int> scores = new List<int> { 90, 90, 70 };          // 순서 O, 중복 O
HashSet<string> cities = new HashSet<string> { "서울", "서울" };   // 중복 X → 1개
Dictionary<string, int> ages = new Dictionary<string, int> {
    ["홍길동"] = 20,
    ["한석봉"] = 25,
};
Console.WriteLine(ages["홍길동"]);                          // key로 찾기

public enum KeyType {
    Padlock,
    Button,
    Dial,
    Finger,
}

public class StrongBox<T> {                                 // 제네릭
    private T? _item;

    public void Put(T item) {
        _item = item;
    }

    public T? Get() {
        return _item;
    }
}
```

## 2. 데이터를 다루는 앱의 계층 구조 (C# 2학기 기준)

흐름: **View(화면) → Service(규칙 판단) → Repository(데이터 골라 주기) → DataSource(저장소와 통신)**

| 계층 | 할 일 | 하면 안 되는 일 |
|---|---|---|
| Model | 데이터 모양만 정의. 불변(`record`), null 없음 | 기능, 파일/서버 접근 |
| DTO | JSON을 그대로 받는 그릇. 필드는 전부 nullable(`?`) | 비즈니스 로직 |
| Mapper | DTO → Model 변환, null·빈 값은 기본값으로. 확장 메서드로 작성 | 파일/서버 접근 |
| DataSource | 파일·서버·DB와 직접 통신(CRUD). DTO나 원본 데이터를 돌려줌 | Model 변환, 게임 규칙 |
| Repository | DataSource를 숨기고, 필요한 데이터를 골라 Model로 돌려줌. 실패는 Result로 | 파일/서버 직접 접근, 복잡한 규칙 |
| Service | 입력 검증, 게임 규칙 적용, 여러 Repository 조합 | 화면(UI) 코드 |

규칙:
- 각 계층은 **인터페이스**를 먼저 만들고 구현체는 그 인터페이스를 구현한다(DIP, SDP).
- 의존하는 객체는 클래스 안에서 `new` 하지 않고 **생성자로 주입**받는다. 이래야 테스트 때 Mock으로 바꿀 수 있다.
- 클래스 하나는 한 가지 일만 한다(SRP). `Util`처럼 관련 없는 기능을 몰아넣지 않는다.
- Service는 화면이 아니라 도메인 기준으로 나눈다: `WalletService`, `PokemonService` (O) / `HomeService`, `SettingsPopupService` (X)
- 의존 관계가 순환하지 않게 한다(ADP). 아래 계층은 위 계층을 모른다.

추천 폴더 구조:
```
Core/            Result.cs, AppError.cs
Models/          Pokemon.cs
Dtos/            PokemonDto.cs
Mappers/         PokemonMapper.cs
DataSources/     IPokemonDataSource.cs, PokemonApiDataSource.cs
Repositories/    IPokemonRepository.cs, PokemonRepository.cs
Services/        PokemonService.cs
Tests/           PokemonRepositoryTest.cs, MockPokemonDataSource.cs
```

### 2-1. 전체 예시: 포켓몬 정보 가져오기

```csharp
// ---------- Core/AppError.cs, Core/Result.cs ----------
public enum AppError {
    NotFound,
    Network,
    Timeout,
    InvalidName,
    LegendaryNotAllowed,
}

public abstract record Result<T> {
    public sealed record Success(T Data) : Result<T>;
    public sealed record Failure(AppError Error) : Result<T>;
}

// ---------- Models/Pokemon.cs : 순수 데이터, null 없음 ----------
public record Pokemon(int Id, string Name, int Height, bool IsLegendary);

// ---------- Dtos/PokemonDto.cs : JSON 그대로, 전부 nullable ----------
public class PokemonDto {
    [JsonPropertyName("id")]
    public int? Id { get; set; }

    [JsonPropertyName("name")]
    public string? Name { get; set; }

    [JsonPropertyName("height")]
    public int? Height { get; set; }

    [JsonPropertyName("is_legendary")]
    public bool? IsLegendary { get; set; }
}

// ---------- Mappers/PokemonMapper.cs : 확장 메서드 ----------
public static class PokemonMapper {
    public static Pokemon ToModel(this PokemonDto dto) {
        return new Pokemon(
            dto.Id ?? 0,
            string.IsNullOrEmpty(dto.Name) ? "알 수 없음" : dto.Name,
            dto.Height ?? 0,
            dto.IsLegendary ?? false);
    }
}

// ---------- DataSources : 서버와 직접 통신, DTO 반환 ----------
public interface IPokemonDataSource {
    Task<PokemonDto?> GetPokemonAsync(string name);   // 없으면 null
}

public class PokemonApiDataSource : IPokemonDataSource {
    private const string BaseUrl = "https://pokeapi.co/api/v2/pokemon/";
    private readonly HttpClient _client;

    public PokemonApiDataSource(HttpClient client) {
        _client = client;
    }

    public async Task<PokemonDto?> GetPokemonAsync(string name) {
        var response = await _client.GetAsync(BaseUrl + name);
        if (response.StatusCode == HttpStatusCode.NotFound) {
            return null;
        }
        response.EnsureSuccessStatusCode();             // 그 밖의 실패는 예외로

        string json = await response.Content.ReadAsStringAsync();
        return JsonSerializer.Deserialize<PokemonDto>(json);
    }
}

// ---------- Repositories : 데이터 골라 주기 + 데이터 실패를 Result로 ----------
public interface IPokemonRepository {
    Task<Result<Pokemon>> GetPokemonAsync(string name);
}

public class PokemonRepository : IPokemonRepository {
    private readonly IPokemonDataSource _dataSource;

    public PokemonRepository(IPokemonDataSource dataSource) { // 생성자 주입
        _dataSource = dataSource;
    }

    public async Task<Result<Pokemon>> GetPokemonAsync(string name) {
        try {
            PokemonDto? dto = await _dataSource.GetPokemonAsync(name);
            if (dto == null) {
                return new Result<Pokemon>.Failure(AppError.NotFound);
            }
            return new Result<Pokemon>.Success(dto.ToModel());
        } catch (TimeoutException) {
            return new Result<Pokemon>.Failure(AppError.Timeout);
        } catch (HttpRequestException) {
            return new Result<Pokemon>.Failure(AppError.Network);
        }
    }
}

// ---------- Services : 게임 규칙 판단 + 규칙 실패를 Result로 ----------
public class PokemonService {
    private readonly IPokemonRepository _repository;

    public PokemonService(IPokemonRepository repository) {
        _repository = repository;
    }

    public async Task<Result<Pokemon>> CatchAsync(string name) {
        if (string.IsNullOrWhiteSpace(name)) {                     // 입력 검증
            return new Result<Pokemon>.Failure(AppError.InvalidName);
        }

        var result = await _repository.GetPokemonAsync(name.Trim().ToLower());
        if (result is Result<Pokemon>.Success s && s.Data.IsLegendary) { // 규칙 적용
            return new Result<Pokemon>.Failure(AppError.LegendaryNotAllowed);
        }
        return result;
    }
}

// ---------- View(화면)에서 사용 ----------
var service = new PokemonService(
    new PokemonRepository(
        new PokemonApiDataSource(new HttpClient())));

var caught = await service.CatchAsync("pikachu");
switch (caught) {
    case Result<Pokemon>.Success s:
        Console.WriteLine("잡았다! " + s.Data.Name);
        break;
    case Result<Pokemon>.Failure { Error: AppError.NotFound }:
        Console.WriteLine("그런 포켓몬은 없어요.");
        break;
    case Result<Pokemon>.Failure f:
        Console.WriteLine("실패: " + f.Error);
        break;
}
```

### 2-2. 파일 저장용 DataSource 예시

```csharp
public interface IItemDataSource {
    Task<List<Item>> LoadAllItemsAsync();
    Task SaveAllItemsAsync(List<Item> items);
}

public class JsonFileItemDataSource : IItemDataSource {
    private readonly string _path;

    public JsonFileItemDataSource(string path) {
        _path = path;
    }

    public async Task<List<Item>> LoadAllItemsAsync() {
        if (!File.Exists(_path)) {
            return new List<Item>();
        }
        string json = await File.ReadAllTextAsync(_path);
        return JsonSerializer.Deserialize<List<Item>>(json) ?? new List<Item>();
    }

    public async Task SaveAllItemsAsync(List<Item> items) {
        string json = JsonSerializer.Serialize(items);
        await File.WriteAllTextAsync(_path, json);
    }
}
```

## 3. 예외와 에러 처리

- 런타임 에러(파일 없음, 0으로 나누기, 네트워크 끊김)는 `try-catch`로 잡고, 꼭 해야 하는 정리는 `finally`에 둔다.
- 가장 넓은 `Exception`만 잡지 말고 가능한 한 구체적인 예외(`IOException`, `HttpRequestException`, `TimeoutException`, `JsonException`)를 잡는다.
- 나만의 예외가 필요하면 `Exception`을 상속한다.
- 실패 종류가 여러 개인 작업(서버 요청 등)은 **Result 패턴**으로 돌려준다(2-1 예시 참고).
  - 데이터 실패(NotFound, Timeout, Network)는 Repository가 만든다.
  - 규칙 실패(InvalidName, LegendaryNotAllowed)는 Service가 만든다.
  - 받는 쪽은 `switch` 패턴 매칭으로 Success / Failure를 모두 처리한다.

```csharp
public class NotEnoughMpException : Exception {
    public NotEnoughMpException(string message) : base(message) {
    }
}

public void CastFireball(int mp) {
    if (mp < 10) {
        throw new NotEnoughMpException("MP가 부족해요");
    }
    Console.WriteLine("파이어볼!");
}

StreamReader? reader = null;
try {
    reader = new StreamReader("save.txt");
    Console.WriteLine(reader.ReadToEnd());
} catch (FileNotFoundException) {        // 구체적인 예외부터
    Console.WriteLine("세이브 파일이 없어요");
} catch (IOException e) {
    Console.WriteLine("읽기 실패: " + e.Message);
} finally {
    reader?.Close();                    // 성공하든 실패하든 닫기
}
```

## 4. 비동기와 네트워크

- 파일·네트워크 같은 I/O는 비동기로 작성한다: `async` / `await`, 리턴 타입은 `Task` / `Task<T>`.
- `ContinueWith` 콜백 대신 `async/await`를 쓴다(콜백 지옥 방지, try-catch 가능).
- 서로 기다릴 필요 없는 작업 여러 개는 `Task.WhenAll`로 동시에 시작한다.
- HTTP는 `HttpClient`(C#) / `UnityWebRequest`(Unity)를 쓴다. 메서드는 읽기 `GET`, 보내기 `POST`, 수정 `PUT`/`PATCH`, 삭제 `DELETE`. 상태 코드 200 성공, 404 없음, 500 서버 오류.
- JSON은 `System.Text.Json`의 `JsonSerializer`로 직렬화/역직렬화한다. 서버 데이터는 DTO로 받은 뒤 Mapper로 Model을 만든다.
- Unity에서는 코루틴 대신 `UniTask`를 우선 고려한다.

```csharp
// (X) 콜백 방식: 중첩되면 콜백 지옥
File.ReadAllTextAsync("a.json").ContinueWith(t1 => {
    File.ReadAllTextAsync("b.json").ContinueWith(t2 => {
        Console.WriteLine(t1.Result + t2.Result);
    });
});

// (O) async / await: 위에서 아래로 읽힘
async Task<string> LoadBothAsync() {
    string a = await File.ReadAllTextAsync("a.json");
    string b = await File.ReadAllTextAsync("b.json");
    return a + b;
}

// (O) 서로 상관없는 작업은 동시에 시작
async Task LoadSceneAsync() {
    Task<string> map = File.ReadAllTextAsync("map.json");
    Task<string> items = File.ReadAllTextAsync("items.json");
    Task<string> sounds = File.ReadAllTextAsync("sounds.json");

    string[] all = await Task.WhenAll(map, items, sounds);   // 가장 긴 것만큼만 걸림
    Console.WriteLine("로딩 끝: " + all.Length + "개");
}

// POST로 JSON 보내기
async Task<bool> SendScoreAsync(HttpClient client, int score) {
    string body = JsonSerializer.Serialize(new { score = score });
    var content = new StringContent(body, Encoding.UTF8, "application/json");
    var response = await client.PostAsync("https://example.com/scores", content);
    return response.IsSuccessStatusCode;
}
```

## 5. 함수형 도구

- 반복문 + if로 거르는 코드는 LINQ로 줄인다: `Where`(거르기), `Select`(바꾸기), `Any`(있나?), `Aggregate`(합치기), `OrderBy`(정렬), 끝에 `ToList()` / `ToHashSet()`.
- 짧은 동작은 람다식(`=>`)으로, 결과가 있으면 `Func`, 없으면 `Action`.
- 자바에서는 문자열을 반복해서 붙일 때 `+` 대신 `StringBuilder`를 쓰고, 내용 비교는 `==` 대신 `equals()`를 쓴다.

```csharp
var players = new List<Player> {
    new Player("1", "용사", 12),
    new Player("2", "마법사", 5),
    new Player("3", "궁수", 20),
};

// (X) for + if
var strongNames1 = new List<string>();
foreach (var p in players) {
    if (p.Level >= 10) {
        strongNames1.Add(p.Name);
    }
}

// (O) LINQ 한 줄
var strongNames2 = players
    .Where(p => p.Level >= 10)
    .OrderByDescending(p => p.Level)
    .Select(p => p.Name)
    .ToList();                                         // 궁수, 용사

bool hasBeginner = players.Any(p => p.Level < 10);     // true
int totalLevel = players.Select(p => p.Level).Aggregate((a, b) => a + b);   // 37

Func<int, int> twice = x => x * 2;                     // 결과 있음 → Func
Action<string> log = msg => Console.WriteLine(msg);    // 결과 없음 → Action
```

```java
StringBuilder sb = new StringBuilder();
for (int i = 1; i <= 100; i++) {
    sb.append(i).append(",");     // + 대신 append
}
String result = sb.toString();

String input = "yes";
if (input.equals("yes")) {        // == 대신 equals
    System.out.println("확인!");
}
```

## 6. 테스트

로직이 있는 클래스(특히 Repository, Service, Mapper, 상태를 바꾸는 메서드)를 만들면 테스트도 함께 만든다.

- 파일 이름: `클래스이름Test` (자바는 test 폴더 같은 위치, C#은 Tests 폴더)
- 순서는 **Given(준비) → When(실행) → Then(검증)** 주석을 달아 구분한다.
- 검증: 자바 JUnit 5 `assertEquals`, `assertTrue`, `assertThrows` / C# xUnit `Assert.Equal`, `Assert.True`, `Assert.Throws`
- 서버·DB·파일·랜덤에 기대는 부분은 인터페이스를 구현한 **Mock**으로 바꿔서 테스트한다.
- 테스트 케이스는 정상 값 + **경계값**(0, -1, 최댓값, 최댓값+1) + 실패 상황(null, 빈 문자열, 네트워크 끊김)을 포함한다.
- 자바는 `@DisplayName("한글 설명")`, C#은 한글 메서드 이름으로 무엇을 검사하는지 적는다.

```csharp
// Mock: 진짜 서버 대신 정해진 대답을 하는 가짜
public class MockPokemonDataSource : IPokemonDataSource {
    private readonly Dictionary<string, PokemonDto> _fake = new Dictionary<string, PokemonDto> {
        ["pikachu"] = new PokemonDto { Id = 25, Name = "pikachu", Height = 4, IsLegendary = false },
        ["mewtwo"] = new PokemonDto { Id = 150, Name = "mewtwo", Height = 20, IsLegendary = true },
    };

    public Task<PokemonDto?> GetPokemonAsync(string name) {
        _fake.TryGetValue(name, out PokemonDto? dto);
        return Task.FromResult(dto);
    }
}

// 항상 타임아웃이 나는 Mock
public class TimeoutPokemonDataSource : IPokemonDataSource {
    public Task<PokemonDto?> GetPokemonAsync(string name) {
        throw new TimeoutException();
    }
}

public class PokemonServiceTest {
    private PokemonService CreateService(IPokemonDataSource dataSource) {
        return new PokemonService(new PokemonRepository(dataSource));
    }

    [Fact]
    public async Task 있는_포켓몬은_성공한다() {
        // Given
        var service = CreateService(new MockPokemonDataSource());

        // When
        var result = await service.CatchAsync("pikachu");

        // Then
        var success = Assert.IsType<Result<Pokemon>.Success>(result);
        Assert.Equal(25, success.Data.Id);
    }

    [Fact]
    public async Task 빈_이름은_InvalidName() {
        var service = CreateService(new MockPokemonDataSource());

        var result = await service.CatchAsync("   ");

        Assert.Equal(new Result<Pokemon>.Failure(AppError.InvalidName), result);
    }

    [Fact]
    public async Task 전설_포켓몬은_잡을_수_없다() {
        var service = CreateService(new MockPokemonDataSource());

        var result = await service.CatchAsync("mewtwo");

        Assert.Equal(new Result<Pokemon>.Failure(AppError.LegendaryNotAllowed), result);
    }

    [Fact]
    public async Task 타임아웃이면_Timeout_에러() {
        var service = CreateService(new TimeoutPokemonDataSource());

        var result = await service.CatchAsync("pikachu");

        Assert.Equal(new Result<Pokemon>.Failure(AppError.Timeout), result);
    }
}
```

```java
class WizardTest {
    @Test
    @DisplayName("hp에 음수를 넣으면 0이 된다")
    void setHpNegative() {
        // Given
        Wizard wizard = new Wizard("간달프", 50);

        // When
        wizard.setHp(-10);

        // Then
        assertEquals(0, wizard.getHp());
    }

    @Test
    @DisplayName("이름이 3글자 미만이면 예외가 난다")
    void shortName() {
        assertThrows(IllegalArgumentException.class, () -> new Wizard("간", 50));
    }
}
```

## 7. 디자인 패턴 고르기

필요할 때만 쓴다. 패턴을 위한 패턴은 만들지 않는다.

| 상황 | 패턴 |
|---|---|
| 객체가 딱 하나만 있어야 함 (GameManager) | 싱글턴 |
| 종류에 따라 다른 객체를 만들어야 함 | 팩토리 |
| 옵션이 많은 객체를 단계적으로 조립 | 빌더 |
| 상황에 따라 알고리즘 교체 (공격 방식, AI 행동) | 전략 |
| 한 사건에 여러 곳이 반응 (사망 → UI, 사운드, 업적) | 옵저버 (`event Action`) |
| `Update()` 안의 if-else가 상태별로 길어짐 | 상태 패턴 |

```csharp
// 싱글턴 + 옵저버
public class GameManager {
    private static GameManager? _instance;
    public static GameManager Instance => _instance ??= new GameManager();

    private GameManager() {                     // 밖에서 new 금지
    }

    public event Action? OnPlayerDied;          // 구독할 수 있는 이벤트

    public void PlayerDied() {
        OnPlayerDied?.Invoke();                 // 구독자 모두에게 알림
    }
}

GameManager.Instance.OnPlayerDied += () => Console.WriteLine("UI: 게임 오버");
GameManager.Instance.OnPlayerDied += () => Console.WriteLine("사운드: 으악!");
GameManager.Instance.PlayerDied();

// 팩토리: 만드는 일을 한 곳에 모으기
public static class MonsterFactory {
    public static Character Create(string type) {
        return type switch {
            "slime" => new Slime(),
            "goblin" => new Goblin(new Weapon("단검")),
            _ => throw new ArgumentException("모르는 몬스터: " + type),
        };
    }
}

// 전략: 공격 방식을 갈아 끼우기
public interface IAttackStrategy {
    int CalculateDamage(int power);
}

public class NormalAttack : IAttackStrategy {
    public int CalculateDamage(int power) {
        return power;
    }
}

public class CriticalAttack : IAttackStrategy {
    public int CalculateDamage(int power) {
        return power * 2;
    }
}

public class Hero {
    public IAttackStrategy Strategy { get; set; } = new NormalAttack();

    public int Attack(int power) {
        return Strategy.CalculateDamage(power);
    }
}
```

## 8. Git

사용자가 커밋을 요청하면 커밋 메시지는 아래 규칙으로 쓴다. `reset --hard`처럼 기록이 사라지는 명령은 먼저 사용자에게 확인한다.

| 머리말 | 뜻 |
|---|---|
| `feat:` | 기능 추가 |
| `fix:` | 버그 수정 |
| `refactor:` | 동작은 그대로, 코드 정리 |
| `test:` | 테스트 추가·수정 |
| `docs:` | 문서 수정 |

```bash
git status                                   # 바뀐 파일 확인
git add .                                    # 올릴 파일 담기
git commit -m "feat: 포켓몬 잡기 Service 추가"
git commit -m "fix: 빈 이름이면 InvalidName 반환"
git commit -m "test: 타임아웃 Mock 테스트 추가"
git push                                     # GitHub에 올리기
```

## 9. 마무리 체크리스트

코드를 넘기기 전에 확인한다.
- [ ] 모든 `{`가 같은 줄 끝에 있다 (K&R)
- [ ] 필드는 private, 값 검증은 setter/프로퍼티/생성자 안에 있다
- [ ] 클래스마다 한 가지 일만 한다
- [ ] 다른 객체는 인터페이스로, 생성자를 통해 받는다
- [ ] 파일·서버 코드는 DataSource에만 있다 (계층 구조를 쓴 경우)
- [ ] 실패 가능한 작업은 try-catch 또는 Result로 처리했다
- [ ] I/O는 async/await를 썼다
- [ ] 로직이 있는 클래스에는 Given-When-Then 테스트가 있다

코드를 보여 줄 때는 맨 위에 어떤 계층/원칙을 적용했는지 한두 줄로 알려 주고, 코드에는 중학생이 이해할 수 있는 짧은 한국어 주석을 단다.
