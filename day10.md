## Day 10 
# 실전 앱 프로젝트 2 (메모 수정과 비동기 데이터 전달)

대망의 마지막 날입니다!  
어제 만든 메모장에 **'메모 수정 기능'**을 추가합니다.  
이 과정에서 **"A 화면에서 B 화면으로 데이터를 건네주고, B 화면에서 수정한 결과를 다시 A 화면으로 안전하게 돌려받는"** 실무 앱 개발의 핵심 비동기 패턴을 마스터하게 됩니다.

---

## 🎯 이번 프로젝트의 핵심 목표: 화면 간 데이터 전달

메모 수정 기능은 두 화면 사이에서 데이터를 안전하게 주고받으며 완성됩니다:

1. **메인 화면에서 수정 화면으로 (`Navigator.push`)**: 수정 버튼(`Icons.edit_outlined`)을 누르면, 기존 메모 내용을 생성자에 실어 새 화면(`EditNotePage`)으로 전달합니다.
2. **수정 화면에서 내용 변경 후 반환 (`Navigator.pop`)**: 사용자가 텍스트를 고친 후 [수정 완료] 버튼을 누르면 `Navigator.pop(context, 수정된글자)`를 통해 결과를 되돌려 보냅니다.
3. **메인 화면에서 결과 수신 및 갱신 (`await` & `setState`)**: `await`로 기다리던 메인 화면이 전달받은 글자를 받아 `setState()`로 목록을 즉시 갱신합니다.

---

## 1. 핵심 원리 3단계

### 1단계: 수정 화면으로 기존 내용 보내주기
메모를 수정하려면 당연히 기존에 써둔 글자가 입력창에 먼저 적혀 있어야 합니다.  
생성자를 통해 기존 메모 글자를 넘겨줍니다:
```dart
MaterialPageRoute(
  builder: (context) => EditNotePage(currentNote: _notes[index]),
)
```

### 2단계: 수정 완료 후 결과 들려보내기 (`Navigator.pop`)
사용자가 수정을 마치고 [수정 완료] 버튼을 누르면, 현재 화면을 닫으면서 두 번째 인자에 **수정된 글자**를 쥐어 보냅니다:
```dart
Navigator.pop(context, _editController.text);
```

### 3단계: 메인 화면에서 비동기(`await`)로 기다렸다 갱신하기 (Day 8 복습!)
수정 화면이 닫힐 때까지 차분히 기다렸다가, 반환된 값이 `null`이 아니면 `setState()`로 화면을 갱신합니다:
```dart
// Day 8에서 배운 await: 수정 화면이 닫혀서 결과가 도착할 때까지 대기!
final String? editedNote = await Navigator.push<String>(
  context,
  MaterialPageRoute(builder: (context) => EditNotePage(currentNote: currentNote)),
);

if (editedNote != null && editedNote.trim().isNotEmpty) {
  setState(() {
    _notes[index] = editedNote.trim(); // 목록 갱신!
  });
}
```

---

## 2. 전체 완성 소스 코드 (DartPad 실행 가능)

복사하여 [DartPad](https://dartpad.dev/)에 붙여넣고 실행해 보세요!  
메모 옆의 연필 모양 아이콘(`Icons.edit_outlined`)을 누르면 수정 화면으로 이동합니다.

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
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.teal),
        useMaterial3: true,
      ),
      home: const MemoHomePage(),
    );
  }
}

// ==========================================
// 1. 메인 화면 (메모 목록 + 추가 + 삭제)
// ==========================================
class MemoHomePage extends StatefulWidget {
  const MemoHomePage({super.key});

  @override
  State<MemoHomePage> createState() => _MemoHomePageState();
}

class _MemoHomePageState extends State<MemoHomePage> {
  final List<String> _notes = [
    'Flutter 기초 공부하기',
    'DartPad에서 비동기 화면 전환 실습하기'
  ];

  final TextEditingController _controller = TextEditingController();

  @override
  void dispose() {
    _controller.dispose(); // 메모리 해제
    super.dispose();
  }

  void _addNote() {
    final String text = _controller.text.trim();
    if (text.isNotEmpty) {
      setState(() {
        _notes.add(text);
        _controller.clear();
      });
    }
  }

  void _deleteNote(int index) {
    setState(() {
      _notes.removeAt(index);
    });
  }

  // [핵심] 수정 화면으로 가고 결과를 돌려받는 비동기 함수
  Future<void> _navigateToEditNote(int index) async {
    final String currentNote = _notes[index];

    // Day 8에서 배운 비동기 작업: 사용자가 수정을 마칠 때까지 기다립니다.
    final String? editedNote = await Navigator.push<String>(
      context,
      MaterialPageRoute(
        builder: (context) => EditNotePage(currentNote: currentNote),
      ),
    );

    // 수정을 취소하지 않고 저장하고 돌아왔을 때만 목록 업데이트!
    if (editedNote != null && editedNote.trim().isNotEmpty) {
      setState(() {
        _notes[index] = editedNote.trim();
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('나의 메모장 (수정 가능) 📋'),
        centerTitle: true,
      ),
      body: Column(
        children: <Widget>[
          // 메모 카드 목록
          Expanded(
            child: _notes.isEmpty
                ? const Center(
                    child: Text(
                      '메모가 없습니다. 아래에서 새로 추가해보세요!',
                      style: TextStyle(color: Colors.grey),
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
                          // 오른쪽 끝에 [수정] 버튼과 [삭제] 버튼 나란히 배치
                          trailing: Row(
                            mainAxisSize: MainAxisSize.min, // 필요한 만큼만 가로폭 차지
                            children: [
                              IconButton(
                                icon: const Icon(Icons.edit_outlined, color: Colors.teal),
                                tooltip: '수정하기',
                                onPressed: () => _navigateToEditNote(index),
                              ),
                              IconButton(
                                icon: const Icon(Icons.delete_outline, color: Colors.redAccent),
                                tooltip: '삭제하기',
                                onPressed: () => _deleteNote(index),
                              ),
                            ],
                          ),
                        ),
                      );
                    },
                  ),
          ),

          // 하단 새 메모 입력 바
          SafeArea(
            child: Padding(
              padding: const EdgeInsets.all(12.0),
              child: Row(
                children: <Widget>[
                  Expanded(
                    child: TextField(
                      controller: _controller,
                      decoration: const InputDecoration(
                        hintText: '새로운 메모 입력...',
                        border: OutlineInputBorder(),
                        contentPadding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 12.0),
                      ),
                      onSubmitted: (_) => _addNote(),
                    ),
                  ),
                  const SizedBox(width: 8.0),
                  IconButton.filled(
                    onPressed: _addNote,
                    icon: const Icon(Icons.add),
                    tooltip: '추가',
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

// ==========================================
// 2. 수정 전용 화면 (EditNotePage)
// ==========================================
class EditNotePage extends StatefulWidget {
  final String currentNote; // 부모 화면에서 넘겨받은 기존 메모 내용

  const EditNotePage({super.key, required this.currentNote});

  @override
  State<EditNotePage> createState() => _EditNotePageState();
}

class _EditNotePageState extends State<EditNotePage> {
  late final TextEditingController _editController;

  @override
  void initState() {
    super.initState();
    // 기존 메모 글자를 입력창의 기본값으로 세팅!
    _editController = TextEditingController(text: widget.currentNote);
  }

  @override
  void dispose() {
    _editController.dispose(); // 컨트롤러 해제
    super.dispose();
  }

  void _saveAndReturn() {
    // Navigator.pop에 결과 문자열을 실어 메인 화면으로 복귀!
    Navigator.pop(context, _editController.text);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('메모 수정하기 ✏️'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _editController,
              decoration: const InputDecoration(
                labelText: '메모 내용 수정',
                border: OutlineInputBorder(),
              ),
              maxLines: 5, // 여러 줄 입력 가능하도록 확장
              autofocus: true, // 화면이 열리자마자 바로 타이핑 가능하게 커서 위치
            ),
            const SizedBox(height: 20),
            Row(
              mainAxisAlignment: MainAxisAlignment.end,
              children: [
                // 취소 버튼 (그냥 뒤로 가기)
                OutlinedButton(
                  onPressed: () => Navigator.pop(context),
                  child: const Text('취소'),
                ),
                const SizedBox(width: 12),
                // 저장 버튼 (수정된 글자 반환하기)
                FilledButton.icon(
                  onPressed: _saveAndReturn,
                  icon: const Icon(Icons.check),
                  label: const Text('수정 완료'),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 🎓 플루터 스쿨을 수료하신 여러분, 축하합니다!

여러분은 지난 10일간 코딩이 완전 처음인 상태에서 시작하여:
1. **Dart 언어**의 기본 문법(변수, 타입, Null Safety, 함수, 제어문)
2. **객체지향(OOP)**과 컬렉션, 함수형 프로그래밍
3. **Flutter**의 위젯 트리, 레이아웃(Row/Column), 화면 이동(Navigator)
4. **상태 관리(`setState`, `ListenableBuilder`)**와 비동기 프로그래밍(`Future`, `async/await`)
5. 그리고 두 화면 간에 데이터를 주고받는 **실전 메모장 앱**까지 스스로 완성해 냈습니다!

### 🚀 앞으로의 추천 공부 방향
- **내 컴퓨터에 개발 환경 설치해보기**: 웹 DartPad를 넘어 [공식 설치 가이드](https://docs.flutter.dev/get-started/install)를 참고하여 VS Code와 Flutter SDK를 설치해보세요.
- **실제 스마트폰에 앱 올려보기**: USB 케이블로 내 스마트폰을 컴퓨터에 연결해 방금 만든 메모장 앱을 설치해보세요!
- **추천 상태 관리 라이브러리**: 앱 규모가 커지면 `Riverpod`이나 `Bloc`, `Provider` 같은 전문 상태 관리 도구를 탐색해보세요.

여러분의 멋진 앱 개발 여정을 응원합니다! 🌟
