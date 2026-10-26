## Day 1 
# Dart 언어 기초 (타입, 변수, 연산자, 그리고 안전한 상자 Null Safety)

환영합니다! 코딩이 처음이신가요? 전혀 걱정하지 마세요.  
이 페이지의 내용을 하나씩 천천히 읽고 따라 하시다 보면, 컴퓨터와 대화하는 프로그래밍의 기초를 즐겁게 배우실 수 있습니다.

---

## 💡 프로그래밍이란 무엇인가요?

프로그래밍은 **"컴퓨터에게 우리가 원하는 행동을 순서대로 적어주는 레시피"**와 같습니다.  
컴퓨터는 매우 똑똑하지만, 우리가 명확하고 구체적인 규칙으로 지시하지 않으면 아무것도 하지 못합니다.

우리가 배울 **Dart(다트)**는 구글(Google)이 개발한 현대적인 프로그래밍 언어로, 스마트폰 앱 프레임워크인 **Flutter(플러터)**를 만들기 위해 전 세계 수많은 개발자들이 사용하고 있습니다.

---

## 🛠️ 실습 준비: DartPad 사용법

설치할 것이 전혀 없습니다! 지금 바로 인터넷 브라우저로 [DartPad](https://dartpad.dev/)를 열어보세요.

1. 화면 왼쪽 에디터에 코드를 입력합니다.
2. 상단 중앙의 파란색 **[ ▶ Run ]** 버튼을 누릅니다.
3. 오른쪽 콘솔(Console) 창에 컴퓨터가 출력한 결과가 나타납니다!

---

## 1. 첫 번째 프로그램과 `main` 함수의 비밀

모든 Dart 프로그램은 항상 **`main()`**이라는 특별한 이름의 함수에서부터 시작됩니다. 컴퓨터에게 "여기서부터 읽어라!"라고 알려주는 정문(Front Door) 역할을 합니다.

```dart
void main() {
  // 1. 화면에 글자를 출력하는 명령어입니다.
  print("안녕하세요, 플러터 스쿨에 오신 것을 환영합니다!");
  
  // 2. 다른 문장도 출력해볼까요?
  print("Hello World!");
}
```

### 🔍 코드 한 줄씩 뜯어보기
- **`void`**: "이 함수는 실행하고 나서 특별한 결과값을 남기지 않는다(비어있다)"라는 뜻입니다.
- **`main()`**: 프로그램의 출발점입니다. 모든 Dart 코드는 이 괄호 뒤의 중괄호(`{}`) 안에서 시작합니다.
- **`{` 와 `}`**: 컴퓨터가 순서대로 실행할 명령문들을 담아두는 '바구니'입니다.
- **`print(...)`**: 괄호 안에 적힌 글자나 숫자를 오른쪽 콘솔 화면에 보여주는 명령어입니다.
- **세미콜론 (`;`)**: **[매우 중요!]** 문장의 끝을 알리는 마침표(`.`)입니다. 명령문 끝에 세미콜론을 빼먹으면 컴퓨터가 문장이 끝난 줄 모르고 에러(빨간 줄)를 냅니다.
- **주석 (`//`, `/* */`)**: 컴퓨터는 무시하고 사람만 읽을 수 있는 메모장입니다.
  - `//` : 한 줄 메모
  - `/* ... */` : 여러 줄 메모

---

## 2. 변수(Variable)란 무엇인가요? (이름표 붙은 상자 비유)

프로그래밍에서 **변수(Variable)**는 데이터를 담아두는 **"이름표가 붙은 상자"**입니다.  
물건을 상자에 넣어두고 필요할 때마다 이름표를 불러서 꺼내 쓰는 원리입니다.

Dart는 상자에 어떤 종류의 물건을 넣을 수 있는지 **타입(Type)**을 정해두는 꼼꼼한 언어입니다.

```dart
void main() {
  // 1. 문자열 상자 (String): 글자를 담습니다. 작은따옴표(')나 큰따옴표(")로 감쌉니다.
  String name = '홍길동';
  print(name); // 상자 안의 내용물(홍길동) 출력

  // 2. 정수 상자 (int): 소수점이 없는 숫자(1, 2, 0, -10 등)를 담습니다.
  int age = 20;
  print(age);

  // 3. 실수 상자 (double): 소수점이 있는 숫자(3.14, 0.5 등)를 담습니다.
  double height = 175.5;
  print(height);

  // 4. 참/거짓 상자 (bool): 오직 true(참) 또는 false(거짓) 둘 중 하나만 담습니다.
  bool isStudent = true;
  print(isStudent);

  // 5. 만능 자동 추론 상자 (var):
  // 일일이 타입을 적지 않아도, 컴퓨터가 처음 들어간 값을 보고 타입을 자동으로 알아챕니다.
  var country = '대한민국'; // 자동으로 String 상자로 결정됨!
  // country = 100; // 에러! 이미 글자 상자로 정해졌기 때문에 숫자를 넣을 수 없습니다.
  print(country);
}
```

---

## 3. 사운드 널 안정성 (Sound Null Safety): 안전한 상자 만들기

> [!NOTE]
> **컴퓨터 역사상 가장 많은 버그는 무엇이었을까요?**  
> 바로 "상자가 비어있는데(null), 무언가 들어있다고 착각하고 꺼내 쓰려다 프로그램이 튕기는 현상"입니다. 이를 **'10억 달러짜리 실수'**라고 부릅니다.

최신 **Dart 3**는 프로그램이 갑자기 튕기는 것을 막기 위해 **사운드 널 안정성(Sound Null Safety)**이라는 강력한 안전벨트를 갖추고 있습니다.

1. **기본 상자 (`String`, `int` 등)**: 절대 비어있을(null) 수 없습니다! 반드시 내용물이 들어있어야 합니다.
2. **널 허용 상자 (`String?`, `int?` 등)**: 물음표(`?`)를 붙이면 "이 상자는 비어있을 수도 있다"고 컴퓨터에게 미리 알립니다.

```dart
void main() {
  // A. 일반 상자 (절대 null 불가)
  String user = 'Ned';
  // user = null; // 컴파일 에러! 컴퓨터가 사전에 에러를 잡아내 프로그램 다운을 방지합니다.

  // B. 물음표(?)가 붙은 널 허용 상자
  String? nickname = null; // 비어있어도 괜찮습니다!
  print(nickname); // null 출력

  nickname = '플러터꿈나무';
  print(nickname); // 플러터꿈나무 출력

  // C. 널 병합 연산자 (??) - 안전망 기본값 지정하기
  // "상자가 비어있으면(null이면) 대신 이 기본값을 쓰자!"
  String? userCity = null;
  String cityToDisplay = userCity ?? '서울'; // userCity가 비어있으므로 '서울'이 채택됨!
  print('사용자 도시: $cityToDisplay'); // 사용자 도시: 서울

  // D. 강제 느낌표 (!) - "개발자인 내가 100% 보증하는데 여기 내용물 무조건 있어!"
  // (만약 실제로 비어있으면 프로그램이 튕기므로 정말 확실할 때만 씁니다.)
  String? definitelyHasValue = '확실한 값';
  String safeValue = definitelyHasValue!;
  print(safeValue);
}
```

---

## 4. 상수(Constant): 변하지 않는 값 (`final` vs `const`)

상자 안에 값을 딱 한 번 넣은 뒤, **절대로 바꾸지 못하게 자물쇠로 잠그는 것**을 상수라고 합니다.

- **`final`**: 실행할 때(런타임) 결정되는 불변값 (예: 현재 스마트폰 시각, 사용자가 방금 입력한 이름).
- **`const`**: 코드를 작성하는 순간(컴파일 타임) 이미 완전히 정해진 영원한 불변값 (예: 원주율 3.14159, 앱 이름 고정 문구).

```dart
void main() {
  // final: 실행될 때의 현재 시간을 딱 한 번 저장하고 변경 금지!
  final String loginTime = DateTime.now().toString();
  print('로그인 시간: $loginTime');
  // loginTime = '다른 시간'; // 에러! final은 바꿀 수 없습니다.

  // const: 영원불변의 값
  const double pi = 3.14159;
  const String appTitle = 'My First Flutter App';
  print('$appTitle (원주율: $pi)');
}
```

---

## 5. 기본 연산자 (컴퓨터로 계산하기)

```dart
void main() {
  // 1. 산술 연산자
  int a = 10;
  int b = 3;

  print(a + b); // 덧셈: 13
  print(a - b); // 뺄셈: 7
  print(a * b); // 곱셈: 30
  
  // [주의] Dart에서 일반 나눗셈(/) 결과는 항상 소수점 실수(double)입니다!
  print(a / b); // 3.3333333333333335
  
  print(a % b);  // 나머지 연산자: 10을 3으로 나눈 나머지 -> 1
  print(a ~/ b); // 몫 연산자 (~/): 10을 3으로 나눈 정수 몫 -> 3

  // 2. 증감 연산자 (1씩 늘리거나 줄이기)
  int score = 100;
  score++; // 1 증가 (score = score + 1)
  print(score); // 101

  score--; // 1 감소
  print(score); // 100

  // 3. 비교 연산자 (결과는 항상 true 또는 false)
  print(10 > 5);   // 10이 5보다 크다 -> true
  print(10 < 5);   // 10이 5보다 작다 -> false
  print(10 == 10); // 10과 10은 같다 -> true
  print(10 != 5);  // 10과 5는 다르다 -> true

  // 4. 논리 연산자
  // && (AND): 둘 다 참이어야 참
  print(true && false); // false
  // || (OR): 둘 중 하나만 참이어도 참
  print(true || false); // true
  // ! (NOT): 반대로 뒤집기
  print(!true);         // false
}
```

---

## 6. 올바른 형 변환 (타입 바꾸기)

정수 10을 실수 10.0으로 바꾸거나, 문자 "123"을 숫자 123으로 바꿀 때 사용하는 올바른 방법입니다.

```dart
void main() {
  int age = 20;

  // ❌ 흔한 초보자의 실수:
  // double wrongAge = age as double; 
  // -> 이렇게 하면 컴퓨터가 "int와 double은 호환되지 않습니다!" 하고 에러를 냅니다.

  // ⭕ 올바른 방법: .toDouble() 메서드 사용하기
  double correctAge = age.toDouble();
  print('정수를 실수로: $correctAge'); // 20.0

  // 실수를 정수로 바꿀 때
  double price = 99.8;
  int cutPrice = price.toInt();   // 소수점 버림 -> 99
  int roundPrice = price.round(); // 반올림 -> 100
  print('버림: $cutPrice, 반올림: $roundPrice');

  // 문자를 숫자로 파싱(Parsing)할 때
  String inputScore = '95';
  int scoreNumber = int.parse(inputScore); // 문자를 진짜 숫자로 변환
  print(scoreNumber + 5); // 진짜 숫자이므로 덧셈 가능! -> 100
}
```

---

## ⚠️ 초보자 단골 실수 & 해결법 (FAQ)

> **Q1. 코드 밑에 빨간 줄이 그어지고 콘솔에 `Error: Expected ';' after this.`라고 떠요!**  
> **A1.** 명령문 끝에 세미콜론(`;`)을 빼먹지 않았는지 확인하세요. 모든 명령은 세미콜론으로 끝나야 합니다.
>
> **Q2. `print` 대신 `Print`라고 썼는데 왜 오류가 나나요?**  
> **A2.** Dart는 **대소문자를 엄격하게 구분**합니다. `print`는 소문자여야 하며, `String`은 대문자로 시작해야 합니다.
>
> **Q3. 문자열을 쓸 때 `'안녕"`처럼 한쪽은 작은따옴표, 한쪽은 큰따옴표로 써도 되나요?**  
> **A3.** 안 됩니다! 시작한 따옴표와 끝나는 따옴표의 종류가 반드시 일치해야 합니다 (`'안녕'` 또는 `"안녕"`).

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 여러분 자신의 정보를 변수에 담아 콘솔에 출력해보세요!
1. 여러분의 이름을 담는 `name` 변수 (String)
2. 여러분의 나이를 담는 `age` 변수 (int)
3. "Flutter 학습 중인가요?"를 나타내는 `isLearningFlutter` 변수 (bool)
4. `print`를 사용하여 한 줄로 멋지게 출력해보세요!

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
void main() {
  String name = '플러터 초보';
  int age = 25;
  bool isLearningFlutter = true;

  print('이름: $name, 나이: $age세, 플러터 학습 여부: $isLearningFlutter');
}
```
</details>
