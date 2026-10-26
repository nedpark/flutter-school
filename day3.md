## Day 3 
# Dart 언어 기초 (객체지향 프로그래밍과 클래스)

프로그래밍을 배울 때 가장 큰 산으로 느껴지는 **객체지향 프로그래밍(OOP: Object-Oriented Programming)**을 배웁니다.  
하지만 겁먹을 필요 없습니다. 맛있는 **붕어빵**을 떠올리면 아주 쉽게 이해할 수 있습니다!

---

## 💡 붕어빵 틀(Class)과 붕어빵(Instance)의 개념

객체지향의 핵심 용어를 붕어빵에 빗대어 이해해 봅시다:

- **클래스 (Class)**: 붕어빵을 찍어내는 **'틀(설계도)'**입니다. 앙꼬 종류나 굽는 규칙을 정의하지만, 그 자체를 먹을 수는 없습니다.
- **객체 / 인스턴스 (Object / Instance)**: 틀에 반죽과 앙꼬를 넣고 실제로 구워낸 **'진짜 붕어빵(실체)'**입니다. 컴퓨터 메모리에 실제로 자리를 차지합니다.
- **속성 (Property / Field)**: 붕어빵의 상태와 특징 (팥 앙꼬, 가격 1000원 등 명사형 데이터).
- **메서드 (Method)**: 붕어빵이나 스마트폰이 수행할 수 있는 동작 (소리내기, 화면 켜기 등 동사형 기능).

---

## 1. 첫 번째 클래스 만들어보기

```dart
// 1. 클래스(설계도) 정의하기
class Person {
  // 속성(Field): 사람이 가지는 기본 정보
  String name;
  int age;

  // 생성자(Constructor): 사람이 태어날 때(객체가 만들어질 때) 이름과 나이를 정해주는 문
  // this.name은 "이 객체 자신의 name 속성에 넣어라"라는 뜻입니다.
  Person(this.name, this.age);

  // 메서드(Method): 사람이 할 수 있는 행동
  void introduce() {
    print('안녕하세요! 제 이름은 $name이고, 나이는 $age세입니다.');
  }
}

void main() {
  // 2. 설계도를 바탕으로 실제 사람(인스턴스) 2명 만들기!
  // (참고: 과거 언어들처럼 'new' 키워드를 앞에 붙이지 않아도 됩니다.)
  Person person1 = Person('홍길동', 20);
  Person person2 = Person('김영희', 25);

  // 3. 점(.) 연산자를 이용해 속성에 접근하거나 메서드 실행하기
  print(person1.name); // 홍길동
  print(person2.age);  // 25

  person1.introduce(); // 안녕하세요! 제 이름은 홍길동이고, 나이는 20세입니다.
  person2.introduce(); // 안녕하세요! 제 이름은 김영희이고, 나이는 25세입니다.
}
```

---

## 2. 정보 은닉: Dart의 프라이버시 규칙 (`_`)

> [!NOTE]
> **다른 언어와 꼭 비교해보세요!**  
> 다른 프로그래밍 언어(Java, C++ 등)에는 `public`, `private`, `protected` 같은 키워드가 있지만, **Dart에는 그런 키워드가 없습니다!**

- 이름 앞에 **언더스코어(`_`)**를 붙이면: **Private** (현재 파일 안에서만 접근 가능).
- 언더스코어가 없으면: **Public** (누구나 자유롭게 접근 가능).

```dart
class BankAccount {
  String owner;       // 공개 속성: 누구나 확인 가능
  double _balance;    // 비밀 속성: 은행원(클래스 내부)만 수정 가능하게 보호!

  BankAccount(this.owner, this._balance);

  // getter: 비밀 잔액을 확인만 할 수 있도록 안전한 창구 열어주기
  double get balance => _balance;

  // 메서드: 안전한 입금 규칙을 통과해야만 잔액 늘리기
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
      print('$amount원이 입금되었습니다. (현재 잔액: $_balance원)');
    } else {
      print('0원 이하의 금액은 입금할 수 없습니다!');
    }
  }
}

void main() {
  var myAccount = BankAccount('철수', 10000);
  print('${myAccount.owner}님의 잔액: ${myAccount.balance}원');

  myAccount.deposit(5000); // 5000원이 입금되었습니다. (현재 잔액: 15000원)
}
```

---

## 3. 상속(Inheritance): 부모의 유산 물려받기 (`extends`)

이미 만들어둔 클래스를 기반으로, 새로운 기능을 덧붙여 더 강력한 클래스를 만드는 기법입니다.

- `extends`: "부모 클래스의 유산을 물려받겠다!"
- `super.속성`: 부모 생성자에게 값을 그대로 넘겨주는 최신 Dart 문법!

```dart
// 부모 클래스 (기본 사람)
class Person {
  String name;
  int age;

  Person(this.name, this.age);

  void eat() {
    print('$name님이 밥을 맛있게 먹습니다 🍚');
  }
}

// 자식 클래스 (학생): Person의 모든 속성과 기능을 물려받고, '학번'만 추가!
class Student extends Person {
  int studentId; // 학생만의 고유 정보 추가

  // 부모의 name, age는 super.name, super.age로 즉시 부모에게 전달!
  Student(super.name, super.age, this.studentId);

  // 학생만의 고유 기능 추가
  void study() {
    print('$name(학번: $studentId) 학생이 시험공부를 합니다 ✍️');
  }
}

void main() {
  Student student = Student('김민수', 17, 2026101);
  
  student.eat();   // 부모에게 물려받은 기능 정상 동작!
  student.study(); // 학생만의 고유 기능 동작!
}
```

---

## 4. 믹스인 (Mixin): RPG 게임 스킬 장착하기 (`with`)

상속은 부모가 딱 하나뿐이지만, **믹스인(Mixin)**은 게임 캐릭터가 '비행 스킬', '수영 스킬'을 여러 개 자유롭게 장착하듯 원하는 기능 부품을 레고처럼 끼워 넣을 수 있습니다.

```dart
mixin Flyable {
  void fly() => print('파닥파닥 날아갑니다 🦅');
}

mixin Swimmable {
  void swim() => print('어푸어푸 수영합니다 🏊');
}

// 오리는 새이면서, 날 수도 있고 수영도 할 수 있습니다!
class Duck with Flyable, Swimmable {}

void main() {
  Duck donald = Duck();
  donald.fly();  // 파닥파닥 날아갑니다
  donald.swim(); // 어푸어푸 수영합니다
}
```

---

## 5. Dart 3 봉인 클래스 (Sealed Class): 완벽한 상태 목록 만들기

스마트폰 화면이 **로딩 중 / 데이터 도착 / 오류 발생** 3가지 상태 중 하나라고 할 때, 개발자가 깜빡하고 한 가지 상태를 빼먹는 실수를 원천 차단해 주는 고마운 기능입니다.

```dart
// sealed class: 이 파일 안에서 정의한 상태 외에는 다른 상태가 없음을 엄격히 보증!
sealed class UiState {}

class LoadingState extends UiState {}
class SuccessState extends UiState {
  final String content;
  SuccessState(this.content);
}
class ErrorState extends UiState {
  final String errorMsg;
  ErrorState(this.errorMsg);
}

// Dart 3의 강력한 패턴 매칭
String renderScreen(UiState state) {
  // LoadingState, SuccessState, ErrorState 중 하나라도 빼먹으면 컴파일 에러가 발생합니다!
  return switch (state) {
    LoadingState() => '⏳ 화면 로딩 중...',
    SuccessState(content: var data) => '🎉 화면 렌더링 성공: $data',
    ErrorState(errorMsg: var msg) => '❌ 오류: $msg',
  };
}

void main() {
  print(renderScreen(LoadingState()));
  print(renderScreen(SuccessState('사용자 프로필 데이터')));
}
```

---

## ⚠️ 초보자 단골 실수 & 해결법 (FAQ)

> **Q1. 클래스 안에서 변수를 만들었는데 `Field must be initialized` 빨간 에러가 떠요!**  
> **A1.** Day 1에서 배운 Null Safety 때문입니다! 기본 변수는 null이 될 수 없으므로, 생성자에서 `this.변수명`으로 초기화해 주거나 `String?`처럼 물음표를 붙여주어야 합니다.
>
> **Q2. 다른 언어에서는 `new Person()`처럼 `new`를 쓰던데 Dart는 왜 안 쓰나요?**  
> **A2.** Dart 2 버전부터 개발자의 타이핑 수고를 덜어주기 위해 `new` 키워드를 생략할 수 있게 되었습니다. 그냥 `Person()`이라고 쓰시면 됩니다!

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 카페 메뉴를 표현하는 `Coffee` 클래스를 만들어보세요!
1. `name` (커피 이름, String)
2. `price` (가격, int)
3. `isIced` (아이스 여부, bool)
4. 생성자를 만들고, `orderInfo()` 메서드를 만들어 "아이스 아메리카노(2500원) 주문 완료!" 같은 문장을 출력해보세요.

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
class Coffee {
  String name;
  int price;
  bool isIced;

  Coffee(this.name, this.price, this.isIced);

  void orderInfo() {
    String temp = isIced ? '아이스' : '따뜻한';
    print('$temp $name($price원) 주문 완료!');
  }
}

void main() {
  Coffee myDrink = Coffee('아메리카노', 2500, true);
  myDrink.orderInfo(); // 아이스 아메리카노(2500원) 주문 완료!
}
```
</details>
