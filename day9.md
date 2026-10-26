## Day 9 
# 실전 앱 프로젝트 1 (나만의 메모장 앱 만들기)

지금까지 배운 모든 지식(변수, 리스트, 함수, 위젯, 상태 관리)을 하나로 합쳐서, 실제로 작동하는 **'나만의 메모장 앱'**을 만들어봅니다!

초보자분들이 코드를 한 번에 보고 겁먹지 않도록, **4단계에 걸쳐 점진적으로 완성해 나가는 과정**으로 구성했습니다.

---

## 🎯 4단계 제작 목표

초보자분들이 긴 코드를 한 번에 보고 당황하지 않도록, 아래 4단계 목표에 맞춰 차근차근 완성해 나갑니다:

1. **1단계 (기본 뼈대 구성)**: `MaterialApp`과 `Scaffold`로 앱의 전체 틀을 잡고 상단 앱바(`AppBar`)를 만듭니다.
2. **2단계 (메모 목록 렌더링)**: `ListView.builder`를 사용해 메모 데이터를 카드(`Card`) 형태로 화면에 나열합니다.
3. **3단계 (하단 입력창 배치)**: 화면 아래쪽에 새 메모를 입력받을 `TextField`와 저장 버튼을 가로로 배치합니다.
4. **4단계 (추가 및 삭제 연동)**: `setState()`를 사용해 실제 목록에 메모가 추가되고 삭제되는 동작을 완성합니다.

---

## 1단계: 뼈대와 앱바(AppBar) 만들기
화면의 기본 골격인 `MaterialApp`과 `Scaffold`, 그리고 상단 제목줄을 만듭니다.

```dart
// 1단계 핵심 코드
Scaffold(
  appBar: AppBar(
    title: const Text('나의 메모장'),
    centerTitle: true, // 제목을 가운데 정렬
  ),
  body: const Center(child: Text('여기에 메모 목록이 들어갑니다.')),
);
```

---

## 2단계: 카드 형태의 메모 목록 띄우기 (`ListView.builder`)
메모들을 리스트 사물함에 담아두고, **`ListView.builder`**를 이용해 카드(`Card`)와 타일(`ListTile`) 형태로 붕어빵처럼 화면에 찍어냅니다.

```dart
// 2단계 핵심 코드: 리스트의 개수만큼 카드를 반복 생성
ListView.builder(
  itemCount: _notes.length, // 메모의 총 개수
  itemBuilder: (context, index) {
    return Card(
      child: ListTile(
        title: Text(_notes[index]), // index번째 메모 내용
      ),
    );
  },
);
```

---

## 3단계: 하단 텍스트 입력창 달기 (`Row` + `Expanded` + `TextField`)
화면 아래쪽에 글자를 칠 수 있는 입력창과 저장 아이콘 버튼을 배치합니다.

> [!TIP]
> **왜 `TextField`를 `Expanded`로 감싸야 하나요?**  
> `Row` 안에서 `TextField`를 그냥 두면 가로 길이를 무한정 늘리려다 화면 밖으로 넘치는 에러(오버플로우)가 납니다.  
> `Expanded`로 감싸주면 **"남아있는 가로 여백을 딱 맞게 채워라!"** 하고 안전하게 제한해 줍니다.

---

## 4단계: 전체 완성 소스 코드 (DartPad 실행 가능)

복사하여 [DartPad](https://dartpad.dev/) (Flutter 모드)에 붙여넣고 상단 **[ ▶ Run ]** 버튼을 눌러보세요!

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MemoApp());

class MemoApp extends StatelessWidget {
  const MemoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: '나의 메모장',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
        useMaterial3: true,
      ),
      home: const MemoHomePage(),
    );
  }
}

class MemoHomePage extends StatefulWidget {
  const MemoHomePage({super.key});

  @override
  State<MemoHomePage> createState() => _MemoHomePageState();
}

class _MemoHomePageState extends State<MemoHomePage> {
  // 1. 메모들을 보관하는 상태 리스트 (사물함)
  final List<String> _notes = [
    'Flutter 기초 공부하기',
    'DartPad에서 메모장 만들기',
    'Material 3 디자인 적용해보기'
  ];

  // 2. 텍스트 입력창을 감시하고 조작하는 컨트롤러
  final TextEditingController _controller = TextEditingController();

  // 3. 메모리 누수를 방지하는 자원 반납 메서드 (Day 7 복습!)
  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  // 4. 새로운 메모를 추가하는 함수
  void _addNote() {
    final String text = _controller.text.trim(); // 앞뒤 쓸데없는 공백 제거
    if (text.isNotEmpty) {
      setState(() {
        _notes.add(text);    // 리스트에 추가
        _controller.clear(); // 입력창을 깨끗이 비우기
      });
    }
  }

  // 5. 특정 번호(index)의 메모를 삭제하는 함수
  void _deleteNote(int index) {
    setState(() {
      _notes.removeAt(index); // 리스트에서 해당 칸 삭제
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('나의 메모장 📝'),
        centerTitle: true,
      ),
      body: Column(
        children: <Widget>[
          // A. 메모 목록 영역 (남은 세로 공간을 꽉 채우도록 Expanded)
          Expanded(
            child: _notes.isEmpty
                ? const Center(
                    child: Text(
                      '작성된 메모가 없습니다.\n아래 입력창에서 첫 메모를 남겨보세요!',
                      textAlign: TextAlign.center,
                      style: TextStyle(color: Colors.grey, fontSize: 16),
                    ),
                  )
                : ListView.builder(
                    padding: const EdgeInsets.symmetric(horizontal: 12.0, vertical: 8.0),
                    itemCount: _notes.length,
                    itemBuilder: (context, index) {
                      return Card(
                        elevation: 1,
                        margin: const EdgeInsets.symmetric(vertical: 4.0),
                        child: ListTile(
                          title: Text(
                            _notes[index],
                            style: const TextStyle(fontSize: 16),
                          ),
                          // 오른쪽 끝에 휴지통 삭제 버튼 달기
                          trailing: IconButton(
                            icon: const Icon(Icons.delete_outline, color: Colors.redAccent),
                            tooltip: '메모 삭제',
                            onPressed: () => _deleteNote(index),
                          ),
                        ),
                      );
                    },
                  ),
          ),

          // B. 하단 입력 바 영역 (스마트폰 하단 노치와 키보드를 배려하는 SafeArea)
          SafeArea(
            child: Padding(
              padding: const EdgeInsets.all(12.0),
              child: Row(
                children: <Widget>[
                  Expanded(
                    child: TextField(
                      controller: _controller,
                      decoration: const InputDecoration(
                        hintText: '새로운 메모를 입력하세요...',
                        border: OutlineInputBorder(),
                        contentPadding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 12.0),
                      ),
                      // 키보드에서 엔터(완료)를 쳤을 때도 자동 등록!
                      onSubmitted: (_) => _addNote(),
                    ),
                  ),
                  const SizedBox(width: 8.0),
                  // 예쁜 보라색 원형 추가 버튼
                  IconButton.filled(
                    onPressed: _addNote,
                    icon: const Icon(Icons.send),
                    tooltip: '메모 추가',
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 메모 카드의 왼쪽(leading)에 예쁜 메모 아이콘(`Icons.note_alt_outlined`)을 추가해보세요!
- `ListTile` 안에 `leading: const Icon(Icons.note_alt_outlined, color: Colors.indigo),` 한 줄을 넣어보세요.
- 카드가 한층 더 그럴듯한 메모장 앱처럼 바뀝니다!
