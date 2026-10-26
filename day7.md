## Day 7 
# Flutter 프레임워크 (화면의 기억 '상태' 관리하기)

지금까지는 화면에 글자와 그림을 보여주기만 했습니다.  
하지만 진짜 스마트폰 앱은 사용자가 버튼을 누르면 숫자가 올라가고, 글자를 입력하면 실시간으로 반응해야 합니다.  
이렇게 **시간이나 사용자의 행동에 따라 변하는 화면의 기억**을 **상태(State)**라고 부릅니다.

---

## 💡 상태(State)란 무엇인가요? (사진 vs 디지털시계 비유)

화면을 구성할 때 상태의 유무에 따라 위젯의 종류를 결정합니다:

- **StatelessWidget (상태 없음)**: **액자 속 인쇄된 사진**과 같습니다. 한 번 인쇄되어 화면에 표시되고 나면, 스스로 내부 내용을 변경할 수 없는 정적인 위젯입니다 (예: 앱바 제목, 고정 아이콘, 안내문 등).
- **StatefulWidget (상태 있음)**: **전자식 디지털시계**나 **스톱워치**와 같습니다. 사용자의 터치나 시간의 흐름에 따라 내부 데이터가 바뀌고, 그에 맞춰 화면을 능동적으로 다시 그릴 수 있는 동적인 위젯입니다 (예: 카운터 숫자, 체크박스, 텍스트 입력창 등).

---

## 1. 카운터로 배우는 `setState()`의 원리

버튼을 누르면 내부의 `_counter` 숫자가 1씩 증가하고, Flutter가 화면을 즉시 다시 그려줍니다.

> [!IMPORTANT]
> **`setState()`는 Flutter에게 "저기요! 손들기 🙋"를 하는 신호입니다!**  
> 변수 값만 `_counter++`로 바꾼다고 해서 화면이 저절로 갱신되지 않습니다.  
> 반드시 `setState(() { _counter++; });` 형태로 감싸주어야만 Flutter가 **"아! 데이터가 바뀌었으니 화면을 다시 그려야겠구나(Rebuild)!"** 하고 인식합니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const CounterApp());

class CounterApp extends StatelessWidget {
  const CounterApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: const CounterScreen(),
    );
  }
}

// 1. StatefulWidget 정의
class CounterScreen extends StatefulWidget {
  const CounterScreen({super.key});

  @override
  State<CounterScreen> createState() => _CounterScreenState();
}

// 2. 실제 상태 데이터와 화면 UI를 담당하는 State 클래스
class _CounterScreenState extends State<CounterScreen> {
  // 화면이 기억해야 할 상태 변수
  int _counter = 0;

  void _increaseCount() {
    // setState로 감싸서 상태 변경을 Flutter에 알림!
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('숫자 올리기 카운터')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text('버튼을 누른 횟수:', style: TextStyle(fontSize: 18)),
            const SizedBox(height: 12),
            Text(
              '$_counter',
              style: const TextStyle(fontSize: 50, fontWeight: FontWeight.bold, color: Colors.blue),
            ),
          ],
        ),
      ),
      // 화면 오른쪽 하단의 둥근 플로팅 액션 버튼
      floatingActionButton: FloatingActionButton(
        onPressed: _increaseCount,
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

---

## 2. 생명주기(Lifecycle)와 리소스 반납 (`dispose`)

스마트폰 앱에서 텍스트 입력창(`TextField`)을 다룰 때는 **`TextEditingController`**라는 관리 도구를 사용합니다.

도서관에서 책을 빌렸으면 다 읽고 **반드시 반납**해야 하듯이, 컨트롤러도 화면이 닫힐 때 **`dispose()`** 메서드로 깨끗이 해제해 주어야 스마트폰 메모리가 낭비되는 **메모리 누수(Memory Leak)**를 막을 수 있습니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MaterialApp(home: LiveInputScreen()));

class LiveInputScreen extends StatefulWidget {
  const LiveInputScreen({super.key});

  @override
  State<LiveInputScreen> createState() => _LiveInputScreenState();
}

class _LiveInputScreenState extends State<LiveInputScreen> {
  final TextEditingController _controller = TextEditingController();
  String _inputText = '';

  @override
  void initState() {
    super.initState();
    // 위젯이 처음 태어날 때 실행되는 초기화 작업
    _controller.addListener(() {
      setState(() {
        _inputText = _controller.text;
      });
    });
  }

  @override
  void dispose() {
    // [매우 중요] 위젯이 사라질 때 컨트롤러를 안전하게 메모리에서 해제!
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('실시간 글자 따라하기')),
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          children: [
            TextField(
              controller: _controller,
              decoration: const InputDecoration(
                labelText: '여기에 글자를 입력해보세요',
                border: OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 24),
            Text(
              '내가 타이핑한 내용:\n$_inputText',
              textAlign: TextAlign.center,
              style: const TextStyle(fontSize: 22, color: Colors.teal, fontWeight: FontWeight.bold),
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 3. 세련된 상태 관리: `ListenableBuilder` (Flutter 3.10+)

`setState()`는 편리하지만, 버튼 하나를 눌렀을 때 페이지 전체를 통째로 다시 그린다는 단점이 있습니다.  
거실 벽지 하나를 바꾸자고 집 전체를 부수고 다시 짓는 격이죠!

최신 Flutter에서는 **`ChangeNotifier`**와 **`ListenableBuilder`**를 사용하면, **값이 바뀐 그 자리만 쏙 골라서 번개처럼 리빌드**할 수 있습니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const ModernApp());

// 1. 상태와 비즈니스 로직을 따로 보관하는 똑똑한 모델
class CounterModel extends ChangeNotifier {
  int _count = 0;
  int get count => _count;

  void increment() {
    _count++;
    notifyListeners(); // "나를 구독하고 있는 위젯들아, 화면 갱신해!" 알림 방송
  }
}

class ModernApp extends StatelessWidget {
  const ModernApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: const ModernScreen(),
    );
  }
}

class ModernScreen extends StatefulWidget {
  const ModernScreen({super.key});

  @override
  State<ModernScreen> createState() => _ModernScreenState();
}

class _ModernScreenState extends State<ModernScreen> {
  final CounterModel _model = CounterModel();

  @override
  void dispose() {
    _model.dispose(); // 모델도 사용이 끝나면 메모리 해제!
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('ListenableBuilder 예제')),
      body: Center(
        // [핵심] 오직 이 숫자 Text 위젯만 핀포인트로 다시 그려집니다!
        child: ListenableBuilder(
          listenable: _model,
          builder: (context, _) {
            return Text(
              '${_model.count}',
              style: const TextStyle(fontSize: 60, fontWeight: FontWeight.bold),
            );
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _model.increment,
        child: const Icon(Icons.plus_one),
      ),
    );
  }
}
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 위의 첫 번째 카운터 예제에 **"1씩 빼기"** 버튼을 추가해보세요!
- `_decreaseCount()` 함수를 만들어 `setState(() { _counter--; });`를 작성해보세요.
- 화면에 마이너스 아이콘 버튼(`Icons.remove`)을 배치해보세요.

<details>
<summary>👉 힌트 및 정답 코드 보기 (클릭하여 펼치기)</summary>

```dart
// floatingActionButton을 Row로 묶어 두 개의 버튼을 배치하거나 본문에 ElevatedButton 추가하기
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children: [
    ElevatedButton.icon(
      onPressed: () {
        setState(() {
          _counter--;
        });
      },
      icon: const Icon(Icons.remove),
      label: const Text('감소'),
    ),
    const SizedBox(width: 16),
    ElevatedButton.icon(
      onPressed: () {
        setState(() {
          _counter++;
        });
      },
      icon: const Icon(Icons.add),
      label: const Text('증가'),
    ),
  ],
)
```
</details>
