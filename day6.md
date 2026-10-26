## Day 6 
# Flutter 프레임워크 (레고 블록 위젯과 레이아웃, Material 3)

드디어 오늘부터 진짜 스마트폰 화면을 만드는 **Flutter(플러터)**의 세계로 들어왔습니다!  
스마트폰 화면에 글자를 띄우고, 버튼을 배치하고, 다른 화면으로 넘어가는 법을 배웁니다.

---

## 📱 DartPad를 Flutter 모드로 전환하기

[DartPad](https://dartpad.dev/)를 열고 다음 순서로 모드를 변경하세요:
1. 상단 메뉴의 **`New Pad`** 버튼을 누릅니다.
2. 팝업창에서 **`Flutter`**를 선택하고 Create를 누릅니다.
3. 오른쪽 화면이 검은 콘솔창에서 **스마트폰 모양의 흰 화면(Preview)**으로 바뀝니다!

---

## 🧱 Flutter의 대원칙: "모든 것은 위젯(Widget)이다!"

Flutter에서 화면을 만드는 과정은 **레고 블록을 조립하는 것**과 같습니다:

- **MaterialApp**: 앱 전체를 감싸는 가장 큰 도화지이자 테마 설정 바구니입니다.
  - **Scaffold**: 스마트폰 한 페이지의 뼈대(지붕 AppBar, 몸통 Body, 둥근 버튼 등)를 잡아줍니다.
    - **AppBar**: 상단 제목 표시줄 영역입니다.
    - **Body**: 화면의 실제 콘텐츠가 채워지는 본문 영역입니다.
      - **Center**: 자식 위젯을 가운데로 정렬해주는 배치 위젯입니다.
        - **Text**: 화면에 실제로 표시되는 텍스트 위젯입니다.

---

## 1. 첫 번째 Flutter 앱 만들어보기

```dart
import 'package:flutter/material.dart';

// 1. 앱의 시작점: runApp() 함수가 우리가 만든 앱 위젯을 화면에 띄웁니다.
void main() {
  runApp(const MyFirstApp());
}

// 2. 변하지 않는 정적 화면: StatelessWidget을 상속받습니다.
class MyFirstApp extends StatelessWidget {
  const MyFirstApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false, // 오른쪽 상단 'DEBUG' 리본 숨기기
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true, // 최신 구글 Material 3 디자인 적용
      ),
      home: Scaffold(
        appBar: AppBar(
          title: const Text('나의 첫 번째 플러터 앱'),
          backgroundColor: Colors.blueAccent,
          foregroundColor: Colors.white,
        ),
        body: const Center(
          child: Text(
            '반가워요, Flutter! 🚀',
            style: TextStyle(
              fontSize: 26,
              fontWeight: FontWeight.bold,
              color: Colors.blueAccent,
            ),
          ),
        ),
      ),
    );
  }
}
```

---

## 2. 기본 표시 위젯들 (글자, 아이콘, 이미지)

> [!NOTE]
> **DartPad에서의 이미지 출력 꿀팁**  
> DartPad는 로컬 컴퓨터의 파일(`assets/`)을 읽을 수 없습니다. 따라서 실습 시에는 인터넷 URL로 이미지를 불러오는 **`Image.network()`**나 내장 벡터 아이콘인 **`FlutterLogo()`**를 사용해야 에러 없이 바로 화면에 뜹니다!

```dart
import 'package:flutter/material.dart';

void main() => runApp(const WidgetDemoApp());

class WidgetDemoApp extends StatelessWidget {
  const WidgetDemoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('기본 표시 위젯')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: const [
              // 1. 아이콘 위젯 (크기와 색상 조절 가능)
              Icon(Icons.favorite, size: 60, color: Colors.redAccent),
              SizedBox(height: 16), // 위아래 여백을 주는 투명 상자

              // 2. 플러터 기본 로고 위젯
              FlutterLogo(size: 80),
              SizedBox(height: 16),

              // 3. 텍스트 위젯
              Text('아이콘과 로고가 예쁘게 배치되었습니다!'),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## 3. 버튼 4총사 (Material 3 버튼)

용도와 중요도에 따라 4가지 스타일의 버튼을 적절히 골라 씁니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const ButtonDemoApp());

class ButtonDemoApp extends StatelessWidget {
  const ButtonDemoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('Material 3 버튼 종류')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              // 1. 가장 강조하고 싶을 때 (주 행동 버튼)
              FilledButton(
                onPressed: () => print('FilledButton 클릭!'),
                child: const Text('1. 채워진 버튼 (FilledButton)'),
              ),
              const SizedBox(height: 12),

              // 2. 입체감 있는 그림자 버튼
              ElevatedButton(
                onPressed: () => print('ElevatedButton 클릭!'),
                child: const Text('2. 입체 버튼 (ElevatedButton)'),
              ),
              const SizedBox(height: 12),

              // 3. 테두리만 있는 보조 버튼
              OutlinedButton(
                onPressed: () => print('OutlinedButton 클릭!'),
                child: const Text('3. 테두리 버튼 (OutlinedButton)'),
              ),
              const SizedBox(height: 12),

              // 4. 배경 없는 텍스트 전용 버튼 (취소 등)
              TextButton(
                onPressed: () => print('TextButton 클릭!'),
                child: const Text('4. 텍스트 버튼 (TextButton)'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## 4. 레이아웃의 핵심: Row와 Column의 가로·세로 축 완전 정복!

초보자가 레이아웃에서 가장 많이 혼란스러워하는 것이 바로 **Row(가로)**와 **Column(세로)**의 정렬 축입니다:

- **Row (가로 방향으로 나란히 배치)**:
  - **주축 (Main Axis)**: **가로 방향** (왼쪽 ➔ 오른쪽)
  - **교차축 (Cross Axis)**: **세로 방향** (위 ➔ 아래)
- **Column (세로 방향으로 나란히 배치)**:
  - **주축 (Main Axis)**: **세로 방향** (위 ➔ 아래)
  - **교차축 (Cross Axis)**: **가로 방향** (왼쪽 ➔ 오른쪽)

- **`mainAxisAlignment` (주축 정렬 옵션)**:
  - `MainAxisAlignment.start`: 시작점에 모으기 (기본값)
  - `MainAxisAlignment.center`: 한가운데 모으기
  - `MainAxisAlignment.spaceEvenly`: 여백을 균등하게 나누어 배치
  - `MainAxisAlignment.spaceBetween`: 양 끝에 딱 붙이고 사이 간격 벌리기

---

## 5. 화면 넘어가기: 네비게이션 (접시 쌓기 원리)

Flutter에서 화면 전환은 식당의 **"접시 쌓기(Stack)"** 원리와 같습니다:

- **화면 열기 (`Navigator.push`)**: 기존 화면 위에 새 화면 접시를 한 장 얹어 화면을 덮습니다.
- **화면 닫기 (`Navigator.pop`)**: 맨 위에 얹혀 있던 화면 접시를 걷어내어 원래 보던 이전 화면으로 복귀합니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MaterialApp(home: ScreenOne()));

// 첫 번째 화면
class ScreenOne extends StatelessWidget {
  const ScreenOne({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('1번 화면')),
      body: Center(
        child: FilledButton(
          onPressed: () {
            // Navigator.push: 2번 화면 접시를 위에 얹어라!
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => const ScreenTwo()),
            );
          },
          child: const Text('2번 화면으로 가기 ➔'),
        ),
      ),
    );
  }
}

// 두 번째 화면
class ScreenTwo extends StatelessWidget {
  const ScreenTwo({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('2번 화면'), backgroundColor: Colors.teal),
      body: Center(
        child: OutlinedButton(
          onPressed: () {
            // Navigator.pop: 현재 화면 접시를 치우고 뒤로 돌아가라!
            Navigator.pop(context);
          },
          child: const Text('⬅️ 1번 화면으로 돌아가기'),
        ),
      ),
    );
  }
}
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: `Column` 위젯 안에 좋아하는 과일 이름 3개를 세로로 나란히 띄워보세요!
- 3개의 `Text` 위젯 사이에 `SizedBox(height: 10)`를 넣어 적절한 간격을 주세요.
- `mainAxisAlignment: MainAxisAlignment.center`를 주어 화면 세로 중앙에 모아보세요.

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyFruitApp());

class MyFruitApp extends StatelessWidget {
  const MyFruitApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('좋아하는 과일 목록')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: const [
              Text('🍎 사과', style: TextStyle(fontSize: 24)),
              SizedBox(height: 10),
              Text('🍌 바나나', style: TextStyle(fontSize: 24)),
              SizedBox(height: 10),
              Text('🍓 딸기', style: TextStyle(fontSize: 24)),
            ],
          ),
        ),
      ),
    );
  }
}
```
</details>
