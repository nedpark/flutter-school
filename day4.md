## Day 4 
# Dart 언어 기초 (데이터 묶음 컬렉션과 레코드)

프로그래밍을 하다 보면 데이터 하나(변수 하나)만 다루는 일은 거의 없습니다.  
학생 100명의 명단, 쇼핑몰의 상품 목록 등 **"여러 개의 데이터를 한 묶음으로 깔끔하게 관리하는 도구"**가 바로 **컬렉션(Collection)**입니다.

---

## 💡 컬렉션 3총사의 핵심 특징

데이터를 담는 목적에 따라 3가지 주요 컬렉션을 선택합니다:

- **List (리스트)**: **번호표 붙은 사물함**입니다. 첫 번째(0번)부터 순서대로 번호가 매겨지며, 같은 값을 여러 번 담을 수 있습니다.
- **Map (맵)**: **단어와 뜻이 적힌 사전**입니다. 이름표(Key)와 실제 값(Value)이 짝을 이루며, 이름표는 중복될 수 없습니다.
- **Set (셋)**: **중복 없는 당첨 구슬 주머니**입니다. 순서가 없으며, 중복된 데이터를 자동으로 걸러내 고유한 값만 유지합니다.

---

## 1. List (리스트): 번호표가 붙어 있는 사물함

가장 기본적이고 많이 쓰이는 자료구조입니다. 대괄호 `[]`로 감싸서 만듭니다.

> [!IMPORTANT]
> **왜 첫 번째 칸이 1번이 아니라 0번인가요? (Zero-based Indexing)**  
> 컴퓨터는 시작 지점으로부터 "얼마나 떨어져 있는가(거리/오프셋)"를 따집니다.  
> 첫 번째 사물함은 시작점에서 0칸 떨어져 있으므로 **`index 0`**이 첫 번째 칸입니다!

```dart
void main() {
  // 글자들을 담는 List 생성
  List<String> fruits = ['사과', '바나나', '딸기'];

  // 1. 데이터 꺼내기: 0번부터 시작합니다!
  print(fruits[0]); // 사과 (첫 번째)
  print(fruits[1]); // 바나나 (두 번째)
  print(fruits[2]); // 딸기 (세 번째)

  // 2. 새로운 요소 추가 (맨 뒤에 붙음)
  fruits.add('포도');
  print(fruits); // [사과, 바나나, 딸기, 포도]

  // 3. 특정 요소 삭제
  fruits.remove('바나나');
  print(fruits); // [사과, 딸기, 포도]

  // 4. 총 개수와 포함 여부 확인
  print('과일 개수: ${fruits.length}'); // 3
  print('딸기가 있나요? ${fruits.contains("딸기")}'); // true
}
```

---

## 2. Map (맵): 단어와 뜻이 적힌 사전

Key(키)를 찾으면 그에 해당하는 Value(값)가 튀어나오는 사전 같은 구조입니다. 중괄호 `{}`로 만듭니다.

```dart
void main() {
  // 이름(Key)과 나이(Value)를 짝지어 저장
  Map<String, int> userAges = {
    '홍길동': 20,
    '김철수': 25,
  };

  // 1. 값 꺼내기 (사물함 번호 대신 Key 이름을 넣음)
  print(userAges['홍길동']); // 20

  // 2. 새로운 짝꿍 추가하기 또는 값 수정하기
  userAges['이영희'] = 28; // 추가
  userAges['홍길동'] = 21; // 기존 값 수정
  print(userAges); // {홍길동: 21, 김철수: 25, 이영희: 28}

  // 3. Key가 있는지 확인하기
  print(userAges.containsKey('김철수')); // true
  print(userAges.containsKey('이순신')); // false
}
```

---

## 3. Set (셋): 중복을 허용하지 않는 주머니

중복된 데이터가 들어오면 자동으로 튕겨내고 단 하나만 유지합니다.

```dart
void main() {
  // '사과'를 두 번 넣어도 하나만 저장됩니다!
  Set<String> uniqueFruits = {'사과', '바나나', '사과', '딸기'};
  print(uniqueFruits); // {사과, 바나나, 딸기}

  // 중복 없는 태그 목록, 당첨 번호 등에 유용하게 쓰입니다.
}
```

---

## 4. 컬렉션을 다루는 마법의 문법들

### A. 펼침 연산자 (Spread Operator: `...` 와 `...?`)
바구니 안의 내용물들을 꺼내어 다른 바구니에 촤르륵 펼쳐 넣는 기법입니다.

```dart
void main() {
  List<int> first = [1, 2, 3];
  List<int> second = [4, 5];

  // ...first: [1, 2, 3]의 껍질을 벗겨 1, 2, 3만 알맹이로 꺼내 넣음
  List<int> total = [...first, ...second, 6];
  print(total); // [1, 2, 3, 4, 5, 6]

  // ...? (널 안전 펼침): 혹시 상자가 비어있어도(null이어도) 튕기지 않고 무시함
  List<int>? emptyList = null;
  List<int> safeTotal = [...first, ...?emptyList];
  print(safeTotal); // [1, 2, 3]
}
```

### B. 컬렉션 if문과 컬렉션 for문 (Flutter 화면 구성의 비밀무기!)
목록을 만들면서 그 자리에서 조건문과 반복문을 곧바로 쓸 수 있습니다.

```dart
void main() {
  bool isAdmin = true;

  // 관리자(isAdmin)일 때만 '관리자 메뉴'가 목록에 쏙 들어감!
  List<String> menu = [
    '홈 화면',
    '내 정보',
    if (isAdmin) '관리자 설정 페이지',
  ];
  print(menu); // [홈 화면, 내 정보, 관리자 설정 페이지]

  // 컬렉션 for: 숫자를 가공해서 리스트를 즉석 생성
  List<int> scores = [10, 20, 30];
  List<String> scoreLabels = [
    for (int s in scores) '$s점',
  ];
  print(scoreLabels); // [10점, 20점, 30점]
}
```

---

## 5. Dart 3 레코드 (Records): 클래스 없는 일회용 묶음 봉투

기존에는 함수에서 결과값 2개(예: 최솟값과 최댓값)를 돌려주려면 번거롭게 전용 클래스를 만들어야 했습니다.  
**Dart 3**에서는 괄호 `()`로 가볍게 묶어서 한 번에 여러 값을 주고받을 수 있습니다.

```dart
// (int, String): 숫자와 문자를 동시에 담아 반환하는 레코드 함수
(int, String) getUserInfo() {
  return (1, '홍길동'); // 두 개의 값을 하나의 봉투에 쏙 담아 반환!
}

void main() {
  // 1. 위치 기반 접근 ($1, $2)
  var info = getUserInfo();
  print('아이디: ${info.$1}, 이름: ${info.$2}');

  // 2. 구조 분해 할당 (Destructuring): 봉투를 열어 각각의 변수로 쏙 꺼내기!
  var (id, name) = getUserInfo();
  print('분해된 아이디: $id, 분해된 이름: $name'); // 분해된 아이디: 1, 분해된 이름: 홍길동
}
```

---

## ⚠️ 초보자 단골 실수 & 해결법 (FAQ)

> **Q1. `RangeError (index): Index out of range` 라는 빨간 에러가 콘솔에 떠요!**  
> **A1.** 리스트의 크기보다 큰 번호(인덱스)를 요청했을 때 발생합니다. 길이가 3인 리스트(`[0], [1], [2]`)에서 3번이나 4번을 꺼내려 하면 사물함이 없으므로 에러가 납니다. 인덱스 번호를 다시 확인하세요!

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 5명의 학생 점수 리스트가 있습니다.
`List<int> testScores = [70, 85, 90, 60, 100];`
1. 점수 목록의 총 개수(길이)를 출력해보세요.
2. 가장 첫 번째 학생의 점수(index 0)와 마지막 학생의 점수를 출력해보세요.

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
void main() {
  List<int> testScores = [70, 85, 90, 60, 100];

  print('총 학생 수: ${testScores.length}명');
  print('첫 번째 학생: ${testScores.first}점 (또는 testScores[0])');
  print('마지막 학생: ${testScores.last}점');
}
```
</details>
