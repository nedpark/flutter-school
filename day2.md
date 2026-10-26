## Day 2 
# Dart 언어 기초 (함수, 조건문, 그리고 똑똑한 Dart 3 스위치)

Day 1에서 변수(이름표 붙은 상자)를 배웠다면, 오늘은 프로그램에 **"동작(Action)"**과 **"판단(Logic)"**을 부여하는 방법을 배웁니다.

---

## 1. 함수(Function)란 무엇인가요? (음료 자판기 비유)

함수는 특정 작업을 수행하도록 미리 만들어 둔 **"명령어들의 묶음"**입니다.  
실생활의 **음료수 자판기**에 빗대어 생각하면 아주 직관적입니다:

1. **입력값 (매개변수 / Parameter)**: 자판기에 넣는 동전과 누르는 메뉴 버튼입니다 (함수가 계산하거나 일하는 데 필요한 재료).
2. **동작 과정 (함수 본체 / Body)**: 자판기 안에서 음료수를 골라 꺼내는 일련의 처리 과정입니다.
3. **결과물 (반환값 / Return value)**: 자판기 배출구로 툭 떨어지는 시원한 캔 음료수입니다 (함수 밖으로 넘겨주는 최종 결과).

---

### A. 가장 간단한 함수 만들기

```dart
// 1. 반환값이 없는 함수: 그냥 인사만 출력하고 끝납니다. (void 사용)
void sayHello() {
  print('안녕하세요! 반가워요.');
}

// 2. 재료(매개변수)를 받아 결과(반환값)를 내놓는 함수
// String: 결과물로 글자를 돌려준다는 뜻
// (String name): 일할 때 '이름'이라는 재료를 받는다는 뜻
String makeGreeting(String name) {
  return '반갑습니다, $name님!'; // return: 결과를 함수 밖으로 던져줍니다.
}

// 3. 두 숫자를 더해주는 함수
int addNumbers(int a, int b) {
  return a + b;
}

void main() {
  // 함수 호출하기 (버튼 누르기)
  sayHello();

  // 결과를 받아서 변수에 저장하기
  String message = makeGreeting('철수');
  print(message); // 반갑습니다, 철수님!

  int sum = addNumbers(10, 20);
  print('합계: $sum'); // 합계: 30
}
```

---

### B. 화살표 함수 (Fat Arrow Function)
함수의 내용이 딱 한 줄의 계산식이나 반환문이라면, `=>` (뚱뚱한 화살표)를 써서 아주 간단하게 줄일 수 있습니다.

```dart
// 일반 함수
int squareClassic(int x) {
  return x * x;
}

// 화살표 함수 (중괄호 {}와 return 생략 가능!)
int square(int x) => x * x;

void main() {
  print(square(5)); // 25
}
```

---

### C. 명명된 매개변수 (Named Parameters): Flutter의 핵심 문법!

> [!IMPORTANT]
> **Flutter 코드를 작성할 때 90% 이상 사용되는 문법입니다!**  
> 일반 함수는 매개변수의 **순서**를 정확히 외워야 하지만, 중괄호 `{}`로 감싼 **명명된 매개변수**는 **이름표**를 붙여서 호출하므로 순서가 바뀌어도 상관없고 코드 읽기가 매우 편해집니다.

- **`required`**: "이 값은 필수니까 안 넣으면 에러를 내겠다!"
- **기본값 (`= 값`)**: "값을 안 넣으면 이 기본값을 쓰겠다!"

```dart
// 이름(name)은 필수! 나이(age)는 안 넣으면 기본 20세, 도시(city)는 안 넣으면 기본 서울
void printUserProfile({
  required String name,
  int age = 20,
  String city = '서울',
}) {
  print('[$name] 나이: $age세, 거주지: $city');
}

void main() {
  // 호출할 때 '이름표: 값' 형태로 부릅니다.
  printUserProfile(name: '홍길동', age: 30, city: '부산');

  // 순서가 뒤바뀌어도 이름표가 있으므로 전혀 문제없습니다!
  printUserProfile(city: '제주', name: '김영희', age: 25);

  // age와 city를 생략하면 정해둔 기본값이 적용됩니다!
  printUserProfile(name: '이순신'); // [이순신] 나이: 20세, 거주지: 서울
}
```

---

## 2. 조건문: 컴퓨터에게 판단력 선물하기 (if-else)

상황에 따라 다른 행동을 해야 할 때 사용합니다. (예: 신호등 색상에 따라 멈추거나 걷기)

```dart
void main() {
  int score = 85;

  if (score >= 90) {
    print('A 등급입니다! 참 잘했어요 🏆');
  } else if (score >= 80) {
    print('B 등급입니다. 훌륭해요 👍');
  } else if (score >= 70) {
    print('C 등급입니다. 조금만 더 힘내요 ✨');
  } else {
    print('재시험 대상입니다 😢');
  }

  // 삼항 연산자 (한 줄로 줄여 쓰는 조건문)
  // [조건] ? [참일 때의 값] : [거짓일 때의 값]
  int age = 17;
  String message = (age >= 18) ? '성인 요금입니다.' : '청소년 할인 요금입니다.';
  print(message); // 청소년 할인 요금입니다.
}
```

---

## 3. 똑똑한 분기문: Dart 3 Switch 표현식

여러 개의 선택지 중 하나를 고를 때 `if-else`를 너무 길게 쓰면 코드가 지저분해집니다.  
**Dart 3**에서는 마치 메뉴판에서 고르듯 깔끔한 **Switch Expression**을 지원합니다.

```dart
// 요일 번호를 한국어 요일명으로 바꿔주는 함수
String getDayName(int dayNumber) {
  // Dart 3 Switch Expression: 결과를 바로 return합니다!
  return switch (dayNumber) {
    1 => '월요일',
    2 => '화요일',
    3 => '수요일',
    4 => '목요일',
    5 => '금요일',
    6 => '토요일 (주말 🎉)',
    7 => '일요일 (주말 🎉)',
    _ => '잘못된 요일 번호입니다', // _ 는 위의 조건에 없는 '그 외 나머지 전부'를 의미합니다.
  };
}

void main() {
  print(getDayName(1)); // 월요일
  print(getDayName(6)); // 토요일 (주말 🎉)
  print(getDayName(9)); // 잘못된 요일 번호입니다
}
```

---

## 4. 반복문: 컴퓨터의 특기! 지치지 않고 반복하기 (for문)

사람은 같은 작업을 100번 반복하면 지치지만, 컴퓨터는 0.001초 만에 해냅니다.

### A. 일반 for문의 동작 과정 한눈에 보기
`for (초기값; 조건; 증가)`

```dart
void main() {
  for (int i = 1; i <= 3; i++) {
    print('$i번째 바퀴를 돕니다!');
  }
}
```

**컴퓨터 내부 동작 과정 (Trace Table):**
1. **1회전**: `i = 1`로 시작 ➔ `1 <= 3`은 참 ➔ `print('1번째...')` 실행 ➔ `i++`로 `i`가 2가 됨
2. **2회전**: `i = 2` ➔ `2 <= 3`은 참 ➔ `print('2번째...')` 실행 ➔ `i++`로 `i`가 3이 됨
3. **3회전**: `i = 3` ➔ `3 <= 3`은 참 ➔ `print('3번째...')` 실행 ➔ `i++`로 `i`가 4가 됨
4. **종료**: `i = 4` ➔ `4 <= 3`은 거짓! ➔ 반복문 탈출!

---

### B. 목록을 하나씩 꺼내는 향상된 for-in문 (가장 많이 씀)

```dart
void main() {
  List<String> fruits = ['사과', '바나나', '수박', '포도'];

  for (String fruit in fruits) {
    print('맛있는 $fruit를 먹었습니다 냠냠');
  }
}
```

---

## ⚠️ 초보자 단골 실수 & 해결법 (FAQ)

> **Q1. 함수를 만들었는데 콘솔에 아무것도 안 찍혀요!**  
> **A1.** 함수를 **'정의'**하기만 하고 **'호출'**하지 않았을 확률이 높습니다. `main()` 안에서 `함수이름();`으로 실행 버튼을 눌러주었는지 확인하세요.
>
> **Q2. 반환값이 있는 함수를 만들었는데 결과가 `null`이 나와요!**  
> **A2.** 함수 내부 끝에 `return 결과값;`을 적지 않았는지 확인하세요. `return`이 빠지면 컴퓨터는 아무것도 돌려주지 않습니다.

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 나이(age)를 입력받아서 영화 관람료를 계산해주는 함수 `calculateTicketPrice`를 만들어보세요!
- 8세 미만: 무료 (0원)
- 8세 이상 ~ 19세 이하: 학생 요금 (8000원)
- 20세 이상: 일반 요금 (14000원)

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
int calculateTicketPrice(int age) {
  if (age < 8) {
    return 0;
  } else if (age <= 19) {
    return 8000;
  } else {
    return 14000;
  }
}

void main() {
  print('7세 요금: ${calculateTicketPrice(7)}원');   // 0원
  print('15세 요금: ${calculateTicketPrice(15)}원'); // 8000원
  print('25세 요금: ${calculateTicketPrice(25)}원'); // 14000원
}
```
</details>
