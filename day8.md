## Day 8 
# Dart & Flutter (시간이 걸리는 작업 '비동기 프로그래밍')

인터넷에서 날씨 정보를 가져오거나, 서버에서 회원 정보를 불러올 때는 약간의 시간(0.5초~2초)이 걸립니다.  
만약 그 시간 동안 앱이 아무것도 못 하고 얼어붙어 버린다면(Freezing) 사용자는 답답해서 앱을 꺼버릴 것입니다.  
이 문제를 해결하는 똑똑한 기술이 바로 **비동기 프로그래밍(Asynchronous Programming)**입니다.

---

## 💡 동기 vs 비동기: 카페 주문과 진동벨 비유

- **동기(Sync) 방식**: 커피를 주문하고 커피가 나올 때까지 카운터 앞에서 멍하니 서서 기다립니다. 뒤 손님은 주문조차 못 하고 앱 전체가 먹통(화면 멈춤)이 됩니다.
- **비동기(Async) 방식**: 주문 후 **진동벨(`Future`)**을 받고 자리에 앉아 자유롭게 핸드폰을 봅니다. 진동벨이 징~ 울리면(**`await`**) 카운터에 가서 커피를 수령합니다. 화면이 멈추지 않고 항상 매끄럽게 동작합니다.

- **`Future<T>`**: 미래에 특정 데이터(T)를 가져다주겠다고 약속하는 **'진동벨'**입니다.
- **`async`**: 이 함수 안에서 시간이 걸리는 비동기 작업이 일어난다는 선언문입니다.
- **`await`**: 진동벨이 울릴 때까지 다음 줄로 넘어가지 않고 차분히 기다리는 대기 명령어입니다.

---

## 1. Dart 콘솔로 이해하는 `async`와 `await`

```dart
// 2초 뒤에 서버에서 날씨 정보를 받아오는 가상 함수
Future<String> fetchWeather() async {
  print('📡 기상청 서버에 날씨 데이터 요청 중...');
  
  // Future.delayed: 2초 동안 기다리기 (실제 인터넷 다운로드 시뮬레이션)
  await Future.delayed(const Duration(seconds: 2));
  
  return '☀️ 맑음 (24°C)';
}

void main() async {
  print('1. 앱을 실행했습니다.');

  // await: fetchWeather 진동벨이 울려서 날씨 글자가 올 때까지 대기!
  String weather = await fetchWeather();
  print('2. 받아온 날씨 정보: $weather');

  print('3. 모든 작업이 안전하게 끝났습니다.');
}
```

**실행 결과 순서:**
1. `1. 앱을 실행했습니다.` 즉시 출력
2. `📡 기상청 서버에 날씨 데이터 요청 중...` 출력
3. 2초간 조용히 대기 (이 시간 동안에도 스마트폰 화면은 부드럽게 터치 가능!)
4. `2. 받아온 날씨 정보: ☀️ 맑음 (24°C)` 출력
5. `3. 모든 작업이 안전하게 끝났습니다.` 출력

---

## 2. Flutter의 자동 비동기 위젯: `FutureBuilder`

스마트폰 화면에서는 데이터를 가져오는 동안 **뱅글뱅글 도는 로딩 인디케이터**를 보여주어야 합니다.  
Flutter의 **`FutureBuilder`** 위젯은 비동기 작업의 3가지 상태에 맞춰 화면을 자동으로 척척 전환해 줍니다:

- **1단계: 로딩 중 (`ConnectionState.waiting`)** ➔ 화면 중앙에 원형 로딩 인디케이터(`CircularProgressIndicator`)를 표시합니다.
- **2단계: 에러 발생 (`snapshot.hasError`)** ➔ 통신 오류나 예외 발생 시 빨간색 에러 메시지를 표시합니다.
- **3단계: 성공 완료 (`snapshot.hasData`)** ➔ 서버로부터 무사히 전달받은 데이터를 화면에 멋지게 렌더링합니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const WeatherApp());

class WeatherApp extends StatelessWidget {
  const WeatherApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: const WeatherScreen(),
    );
  }
}

class WeatherScreen extends StatefulWidget {
  const WeatherScreen({super.key});

  @override
  State<WeatherScreen> createState() => _WeatherScreenState();
}

class _WeatherScreenState extends State<WeatherScreen> {
  late Future<String> _weatherFuture;

  @override
  void initState() {
    super.initState();
    _weatherFuture = _getWeatherData();
  }

  // 2초 걸리는 가짜 네트워크 함수
  Future<String> _getWeatherData() async {
    await Future.delayed(const Duration(seconds: 2));
    return '서울: 맑음, 기온 22도 🌤️';
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('실시간 날씨 알리미')),
      body: Center(
        child: FutureBuilder<String>(
          future: _weatherFuture, // 관찰할 진동벨 등록!
          builder: (context, snapshot) {
            // 1단계: 아직 데이터가 안 왔을 때 (로딩 화면)
            if (snapshot.connectionState == ConnectionState.waiting) {
              return Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: const [
                  CircularProgressIndicator(),
                  SizedBox(height: 16),
                  Text('날씨 데이터를 다운로드하는 중...'),
                ],
              );
            }

            // 2단계: 에러가 났을 때
            if (snapshot.hasError) {
              return Text('오류 발생: ${snapshot.error}', style: const TextStyle(color: Colors.red));
            }

            // 3단계: 성공적으로 데이터를 받았을 때
            if (snapshot.hasData) {
              return Text(
                snapshot.data!,
                style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
              );
            }

            return const Text('데이터가 없습니다.');
          },
        ),
      ),
    );
  }
}
```

---

## 3. 화면 전환 시 비동기로 결과 돌려받기 맛보기

내일(Day 10) 배울 메모 수정 기능에서는 이 비동기 기술이 그대로 쓰입니다!  
"수정 페이지로 이동하고, 사용자가 수정을 마칠 때까지 `await`로 기다렸다가 수정된 글자를 받아오는" 원리입니다.

```dart
// 다음 화면이 닫힐 때까지 기다렸다가 결과 받기
final String? result = await Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => const DetailScreen()),
);
```

---

## 🎯 직접 해보기 (Mini Challenge)

**문제**: 3초 뒤에 행운의 번호를 알려주는 비동기 함수 `getLuckyNumber()`를 만들어 호출해보세요!
- `await Future.delayed(const Duration(seconds: 3));` 사용
- 행운의 번호 77을 반환
- 콘솔에 "당신의 행운 번호는 77입니다!" 출력하기

<details>
<summary>👉 정답 예시 보기 (클릭하여 펼치기)</summary>

```dart
Future<int> getLuckyNumber() async {
  print('🍀 행운의 번호를 추첨하는 중...');
  await Future.delayed(const Duration(seconds: 3));
  return 77;
}

void main() async {
  int luckyNumber = await getLuckyNumber();
  print('당신의 행운 번호는 $luckyNumber입니다!');
}
```
</details>
