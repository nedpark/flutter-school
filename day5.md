## Day 5 
# Dart 언어 기초 (함수형 프로그래밍과 유용한 꿀팁)

Dart 언어 공부의 마지막 날입니다! 내일부터는 드디어 실제 스마트폰 화면을 만드는 Flutter로 넘어갑니다.  
오늘은 코드를 훨씬 짧고 우아하게 만들어주는 **함수형 프로그래밍(Functional Programming)** 기법들과 실무 꿀팁 문법들을 배웁니다.

---

## 1. 함수형 프로그래밍이란? (요리 레시피 전달하기)

일반적인 코딩이 "재료를 썰어라, 냄비에 넣어라, 불을 켜라" 하고 일일이 지시하는 방식이라면,  
함수형 코딩은 **"자, 여기 가공 레시피(함수)가 있으니 컨베이어 벨트를 통과시켜서 완성해줘!"**라고 선언하는 방식입니다.

데이터 원본을 훼손하지 않고, 깔끔하게 새로운 결과 목록을 만들어냅니다.

---

## 2. 컬렉션 가공 삼총사 (`where`, `map`, `fold`)

리스트 데이터를 다룰 때 가장 많이 쓰이는 3가지 대표 가공 메서드입니다:

1. **`where` (체로 거르기 / 필터링)**: 리스트 안에서 특정 조건(예: 짝수만)을 만족하는 알맹이만 골라냅니다.
2. **`map` (공장 컨베이어 벨트 변환)**: 모든 요소를 정해진 규칙(예: 과일 ➔ 주스로 변환, 숫자 ➔ 2배)에 따라 다른 형태로 바꿉니다.
3. **`fold` (눈사람 뭉치기 / 누적 연산)**: 시작값(예: 0)부터 시작하여 요소들을 차례차례 누적해 단 하나의 결과값(총합 등)으로 완성합니다.

```dart
void main() {
  List<int> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

  // 1. where: 체에 걸러내듯 원하는 조건만 쏙 뽑아내기 (필터링)
  var evens = numbers.where((n) => n.isEven);
  print('짝수만: ${evens.toList()}'); // [2, 4, 6, 8, 10]

  // 2. map: 각 요소를 다른 형태로 변신시키기 (변환)
  var doubled = numbers.map((n) => n * 2);
  print('2배로 변신: ${doubled.toList()}'); // [2, 4, 6, 8, ...]

  // 3. fold: 눈덩이를 굴리듯 초기값에서부터 차례차례 누적하기
  // 0부터 시작해서 (누적값 acc + 현재숫자 curr)을 계속 더함
  int sum = numbers.fold(0, (acc, curr) => acc + curr);
  print('1부터 10까지의 총합: $sum'); // 55

  // 4. 메서드 체이닝 (연쇄 작업): 한 줄로 연결해서 처리하기!
  // "짝수만 골라서 -> 100을 곱한 뒤 -> 예쁜 문자열 목록으로 만들기"
  List<String> result = numbers
      .where((n) => n.isEven)
      .map((n) => '$n점')
      .toList();

  print('최종 결과: $result'); // [2점, 4점, 6점, 8점, 10점]
}
```

> [!TIP]
> **왜 뒤에 `.toList()`를 붙이나요?**  
> `where`나 `map`의 결과물은 게으른(Lazy) 상태인 `Iterable` 형태입니다.  
> 이를 진짜 리스트 사물함 `[ ]`으로 굳혀서 사용하려면 끝에 `.toList()`를 붙여주는 것이 좋습니다.

---

## 3. 계단식 표기법 (Cascade Notation: `..` 와 `?..`)

동일한 객체에게 연달아 심부름을 시킬 때, 이름표를 계속 반복해서 부르지 않고 점 두 개(`..`)로 간결하게 묶는 방법입니다.

```dart
class Robot {
  String name = '';
  int battery = 0;

  void sayHi() => print('삐리비리! 나는 $name 로봇입니다.');
  void charge() => print('배터리를 100% 충전했습니다.');
}

void main() {
  // 계단식 표기법 (..) 사용:
  // 로봇을 만들고 -> 이름을 짓고 -> 배터리를 채우고 -> 인사하고 -> 충전하기를 한 번에!
  Robot myRobot = Robot()
    ..name = '알파고'
    ..battery = 100
    ..sayHi()
    ..charge();

  // 널 안전 계단식 (?..)
  // 만약 로봇 변수가 null이면 뒤의 심부름을 아예 실행하지 않고 안전하게 패스합니다.
  Robot? maybeRobot = null;
  maybeRobot
    ?..name = '베타'
    ..sayHi(); // null이므로 에러 없이 조용히 넘어감!
}
```

---

## 4. 고급 널 테크닉: `late` 키워드와 타입 승격

### A. `late` 키워드 (지연 초기화 약속)
"지금 당장은 값을 못 채우지만, **누군가 이 변수를 쓰기 전에는 내가 반드시 값을 채워둘 것을 약속할게!**"라는 키워드입니다.

```dart
class ProfileCard {
  // 생성자나 화면이 열리는 시점(initState)에 채워질 것을 약속
  late final String userToken;

  void loadToken() {
    userToken = 'secret_abc_123'; // 나중에 채움!
  }

  void showToken() {
    print('내 토큰: $userToken');
  }
}

void main() {
  var card = ProfileCard();
  card.loadToken(); // 값 채우기
  card.showToken(); // 출력
}
```

> [!WARNING]
> 만약 약속을 어기고 `card.loadToken()`을 실행하지 않은 채 `card.showToken()`을 부르면 `LateInitializationError`가 발생하며 프로그램이 튕깁니다. 꼭 확신이 있을 때만 사용하세요!

### B. 스마트 타입 승격 (Type Promotion)
Dart 컴파일러는 매우 똑똑해서, 우리가 `if (name != null)` 처럼 검사를 거치면 그 블록 안에서는 자동으로 "아, 여긴 절대 null이 아니구나!" 하고 일반 Non-nullable 타입으로 알아서 승격(Promotion)시켜 줍니다.

```dart
void printNameLength(String? maybeName) {
  // 여기서는 maybeName이 null일 수도 있으므로 maybeName.length 호출 불가!

  if (maybeName != null) {
    // 훌륭합니다! Dart가 이 안에서는 maybeName을 진짜 String으로 승격시켜줍니다!
    print('이름 길이: ${maybeName.length}글자');
  } else {
    print('이름이 비어있습니다.');
  }
}
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 5개의 단어가 들어있는 리스트가 있습니다.
`List<String> words = ['apple', 'banana', 'cat', 'elephant', 'dog'];`
- 글자 수가 4글자 이상인 단어만 골라내서(`where`)
- 대문자로 변환(`map((w) => w.toUpperCase())`)한 뒤
- 새로운 리스트로 출력해보세요!

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
void main() {
  List<String> words = ['apple', 'banana', 'cat', 'elephant', 'dog'];

  List<String> bigWords = words
      .where((w) => w.length >= 4)
      .map((w) => w.toUpperCase())
      .toList();

  print(bigWords); // [APPLE, BANANA, ELEPHANT]
}
```
</details>
