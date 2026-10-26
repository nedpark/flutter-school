## Day 11 
# 실전 미니 프로젝트 (구구단 맞추기 게임)

축하합니다! 이제 여러분은 단순한 화면 표시를 넘어, 사용자에게 문제를 내고 정답을 판별하는 **진짜 인터랙티브 게임 앱**을 만들 수 있는 실력을 갖추었습니다.

오늘은 `dart:math` 패키지의 랜덤 숫자 생성 기능과 `TextField`, `setState()`를 결합하여 재미있는 **'구구단 맞추기 게임'**을 만들어봅니다!

---

## 🎯 이번 프로젝트의 4단계 학습 목표

1. **1단계 (랜덤 문제 출제)**: `dart:math`의 `Random` 클래스를 사용해 2단~9단 사이의 무작위 곱셈 문제를 자동으로 만듭니다.
2. **2단계 (점수판 및 문제 카드 UI 구성)**: 현재 점수(`Score`)와 "7 × 8 = ?" 형태의 큼직하고 보기 좋은 문제 카드를 배치합니다.
3. **3단계 (입력창과 제출 버튼 연동)**: 숫자 키보드로 답을 입력받을 `TextField`와 [정답 확인] 버튼을 구성합니다.
4. **4단계 (정답 검증과 피드백 제공)**: 사용자가 입력한 답과 실제 정답(`_num1 * _num2`)을 비교하여 점수를 올리고, "정답입니다! 🎉" 또는 "오답입니다 😢" 메시지를 실시간으로 보여준 뒤 다음 문제로 넘어갑니다.

---

## 💡 핵심 원리 2가지 미리보기

### 1. 컴퓨터에게 주사위 굴리기 시키기 (`Random().nextInt`)
Dart 내장 라이브러리인 `dart:math`를 불러오면 랜덤 숫자를 쉽게 뽑을 수 있습니다:
```dart
import 'dart:math';

final random = Random();
// nextInt(8)은 0부터 7까지 중 하나를 무작위로 뽑습니다.
// 여기에 + 2를 더해주면 2부터 9까지의 숫자가 됩니다!
int num1 = random.nextInt(8) + 2; 
int num2 = random.nextInt(9) + 1; // 1부터 9까지
```

### 2. 문자를 안전하게 숫자로 바꾸기 (`int.tryParse`)
사용자가 입력창에 글자를 안 적고 제출하거나, 실수로 한글/특수문자를 적었을 때 앱이 튕기지 않도록 안전하게 변환합니다:
```dart
// 숫자로 바꿀 수 없으면 null을 돌려주는 안전한 변환기!
int? userAnswer = int.tryParse(_controller.text);
```

---

## 🚀 전체 완성 소스 코드 (DartPad 실행 가능)

[DartPad](https://dartpad.dev/)를 열고 (Flutter 모드), 아래 코드를 붙여넣은 뒤 실행해 보세요!

```dart
import 'dart:math';
import 'package:flutter/material.dart';

void main() => runApp(const GugudanGameApp());

class GugudanGameApp extends StatelessWidget {
  const GugudanGameApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: '구구단 맞추기 게임',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.orange),
        useMaterial3: true,
      ),
      home: const GugudanScreen(),
    );
  }
}

class GugudanScreen extends StatefulWidget {
  const GugudanScreen({super.key});

  @override
  State<GugudanScreen> createState() => _GugudanScreenState();
}

class _GugudanScreenState extends State<GugudanScreen> {
  final Random _random = Random();
  final TextEditingController _controller = TextEditingController();

  // 게임 상태 변수들
  int _num1 = 2;
  int _num2 = 2;
  int _score = 0;
  int _streak = 0; // 연속 정답 횟수
  String _message = '정답을 맞혀 점수를 올려보세요!';
  Color _messageColor = Colors.grey;

  @override
  void initState() {
    super.initState();
    _nextQuestion(); // 게임 시작 시 첫 번째 문제 출제
  }

  @override
  void dispose() {
    _controller.dispose(); // 컨트롤러 메모리 해제
    super.dispose();
  }

  // 1. 새로운 구구단 문제 생성 (2단 ~ 9단)
  void _nextQuestion() {
    setState(() {
      _num1 = _random.nextInt(8) + 2; // 2 ~ 9
      _num2 = _random.nextInt(9) + 1; // 1 ~ 9
      _controller.clear(); // 입력창 비우기
    });
  }

  // 2. 정답 확인 함수
  void _checkAnswer() {
    final String text = _controller.text.trim();
    if (text.isEmpty) return;

    final int? userAnswer = int.tryParse(text);
    final int correctAnswer = _num1 * _num2;

    setState(() {
      if (userAnswer == correctAnswer) {
        _score += 10;
        _streak += 1;
        _message = '정답입니다! 🎉 ($_streak연속 정답!)';
        _messageColor = Colors.green;
        _nextQuestion();
      } else {
        _streak = 0;
        _message = '아쉬워요! $_num1 × $_num2 = $correctAnswer 였습니다 😢';
        _messageColor = Colors.redAccent;
        _nextQuestion();
      }
    });
  }

  // 3. 게임 다시 시작 (점수 초기화)
  void _resetGame() {
    setState(() {
      _score = 0;
      _streak = 0;
      _message = '새로운 게임이 시작되었습니다!';
      _messageColor = Colors.grey;
      _nextQuestion();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('구구단 퀴즈 게임 🎮'),
        centerTitle: true,
        actions: [
          IconButton(
            icon: const Icon(Icons.refresh),
            tooltip: '점수 초기화',
            onPressed: _resetGame,
          ),
        ],
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          children: [
            // A. 점수판 카드
            Card(
              elevation: 0,
              color: Theme.of(context).colorScheme.primaryContainer,
              child: Padding(
                padding: const EdgeInsets.symmetric(horizontal: 24.0, vertical: 16.0),
                child: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceAround,
                  children: [
                    Column(
                      children: [
                        const Text('내 점수', style: TextStyle(fontSize: 14)),
                        const SizedBox(height: 4),
                        Text(
                          '$_score점',
                          style: const TextStyle(fontSize: 28, fontWeight: FontWeight.bold),
                        ),
                      ],
                    ),
                    Column(
                      children: [
                        const Text('연속 정답', style: TextStyle(fontSize: 14)),
                        const SizedBox(height: 4),
                        Text(
                          '$_streak회',
                          style: TextStyle(
                            fontSize: 28,
                            fontWeight: FontWeight.bold,
                            color: _streak >= 3 ? Colors.orange : null,
                          ),
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ),
            const SizedBox(height: 32),

            // B. 구구단 문제 표시 카드
            Card(
              elevation: 2,
              child: Container(
                width: double.infinity,
                padding: const EdgeInsets.symmetric(vertical: 40.0),
                child: Column(
                  children: [
                    const Text(
                      '다음을 계산해 보세요',
                      style: TextStyle(color: Colors.grey, fontSize: 16),
                    ),
                    const SizedBox(height: 12),
                    Text(
                      '$_num1  ×  $_num2  =  ?',
                      style: const TextStyle(
                        fontSize: 48,
                        fontWeight: FontWeight.bold,
                        letterSpacing: 2.0,
                      ),
                    ),
                  ],
                ),
              ),
            ),
            const SizedBox(height: 24),

            // C. 피드백 메시지 (정답 / 오답 알림)
            Text(
              _message,
              textAlign: TextAlign.center,
              style: TextStyle(
                fontSize: 18,
                fontWeight: FontWeight.bold,
                color: _messageColor,
              ),
            ),
            const SizedBox(height: 24),

            // D. 정답 입력창
            TextField(
              controller: _controller,
              keyboardType: TextInputType.number, // 숫자 전용 키보드 띄우기
              textAlign: TextAlign.center,
              style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
              decoration: const InputDecoration(
                hintText: '정답 입력',
                border: OutlineInputBorder(),
              ),
              onSubmitted: (_) => _checkAnswer(), // 키보드 엔터 키로도 정답 제출 가능!
            ),
            const SizedBox(height: 16),

            // E. 정답 확인 버튼
            SizedBox(
              width: double.infinity,
              height: 52,
              child: FilledButton.icon(
                onPressed: _checkAnswer,
                icon: const Icon(Icons.check),
                label: const Text('정답 확인', style: TextStyle(fontSize: 18)),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 3연속으로 정답을 맞혔을 때 보너스 점수를 주는 기능을 추가해보세요!
- `_checkAnswer()` 함수에서 `_streak`가 3의 배수(`_streak % 3 == 0`)일 때 보너스 20점을 추가로 더해주고(`_score += 20;`), 메시지에 "🔥 3연속 정답 보너스 +20점!"을 띄워보세요!

<details>
<summary>👉 정답 예시 코드 보기 (클릭하여 펼치기)</summary>

```dart
if (userAnswer == correctAnswer) {
  _score += 10;
  _streak += 1;

  if (_streak % 3 == 0) {
    _score += 20; // 보너스 점수
    _feedbackMessage = '대단해요! 🔥 $_streak연속 정답 보너스 +20점!';
  } else {
    _feedbackMessage = '정답입니다! 🎉 ($_streak연속 정답!)';
  }
  _feedbackColor = Colors.green;
  _nextQuestion();
}
```
</details>
