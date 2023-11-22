# 액티비티 수명 주기
- 액티비티 상태 변화를 알아차릴 수 있도록 여러 콜백이 존재한다.
- 사용자가 액티비티를 벗어났다가 다시 돌아왔을 때 액티비티가 작동하는 방식을 콜백으로 선언한다.

![activity_lifecycle](https://github.com/kimhm0728/TIL/assets/70271235/3602b32e-7718-409a-a61c-c99925624f6a)

## `onCreate()`
```kotlin
// 클래스 프로퍼티
lateinit var textView: TextView  
var gameState: String? = null  
  
override fun onCreate(savedInstanceState: Bundle?) {
	super.onCreate(savedInstanceState)

	// 인스턴스 상태 복구
	gameState = savedInstanceState?.getString(GAME_STATE_KEY)

	// 액티비티에 대한 레이아웃 설정
	setContentView(R.layout.main_activity)

	// TextView 초기화
	textView = findViewById(R.id.text_view)  
}
```
- 시스템이 액티비티를 생성할 때 실행되며, 필수적으로 구현해야 한다.
- xml 레이아웃 파일의 ID를 `setContentView()`에 전달하여 지정해야 한다.
- 액티비티 전체 수명 주기 동안 한 번만 발생해야 하는 액티비티 로직을 실행한다.
	- ex) 데이터를 리스트에 바인딩
	- 액티비티를 ViewModel과 연결
	- 클래스 프로퍼티를 인스턴스화
- `savedInstanceState` 매개변수를 수신한다.
	- 액티비티의 이전 상태가 저장되어 있는 `Bunble` 객체다.
	- 처음 생성된 액티비티일 경우 `Bundle` 객체의 값은 `null`이 된다.
- `create` 상태인 액티비티가 `onCreate()` 실행을 완료하면, `start` 상태가 되고 `onStart()`가 호출된다.

## `onStart()`
- 사용자에게 액티비티가 표시된다.
- 애플리케이션은 액티비티를 포그라운드에 보내고, 상호작용할 수 있도록 준비한다.
- `start` 상태인 액티비티가 `onStart()` 실행을 완료하면, `resume` 상태가 되고 `onResume()`이 호출된다.

## `onResume()`
- 포그라운드에 액티비티가 표시된다.
- 애플리이션이 사용자와 상호작용한다.
- 포그라운드에서 사용자에게 보이는 동안 실행하는 모든 기능을 시작한다.
- 어떤 이벤트가 발생하여 액티비티에 포커스가 사라질 때까지 `resume` 상태로 머무른다.
	- ex) 전화 수신
	- 사용자가 다른 액티비티로 이동
	- 기기 화면 꺼짐
- 위와 같은 이벤트가 발생하면 액티비티는 `pause` 상태에 들어가고, `onPause()`가 호출된다.
- 액티비티가 `pause` 상태에서 `resume` 상태로 돌아오면 다시 `onResume()`이 호출된다.
	- 즉 `onPause()`에서 해제하는 구성요소를 `onResume()`에서 초기화해야 한다.

## `onPause()`
- 사용자가 액티비티를 떠날 때 가장 먼저 호출된다.
- 액티비티가 항상 소멸되는 경우는 아니며, 액티비티가 포그라운드에 있지 않음을 뜻한다.
- 액티비티가 포그라운드에 있지 않을 때 필요하지 않은 기능을 모두 정지한다.
- 액티비티가 다시 시작되거나 사용자에게 완전히 보이지 않을 때까지 `pause` 상태로 머무른다.
- `onPause()` -> `onResume()`
	- 액티비티가 다시 시작되는 경우
	- `Activity` 인스턴스를 메모리에 남겨두고, `onResume()`을 호출할 때 인스턴스를 다시 호출한다. 구성요소를 다시 초기화할 필요가 없다.
- `onPause()` -> `onStop()`
	- 액티비티가 사용자에게 완전히 보이지 않는 경우

- `onPause()`는 아주 잠깐 실행되므로 데이터를 저장하는 작업을 수행하기에 시간이 부족할 수 있다.
	- 그러므로 아래 작업을 `onPause()`에서 수행해서는 안된다.
	- 작업을 마지기 전에 `onPause()`의 실행이 끝날 수 있기 때문이다.
	- 이러한 작업은 `onStop()`에서 수행해야 한다.
		- 애플리케이션, 사용자 데이터를 저장
		- 네트워크 호출
		- 데이터베이스 트랜잭션 실행

#### 멀티 윈도우 모드
- 멀티 윈도우 모드에서 사용자가 다른 애플리케이션의 창을 탭하면, 액티비티는 `pause` 상태로 전환된다.
- 만약 카메라를 활성화하고 해제하는 코드를 `onResume()`과 `onPause()`에 작성했다면, 멀티 윈도우 모드에서 액티비티가 보임에도 카메라가 해제될 수 있다.
- 이를 해결하기 위해 `onStart()`와 `onStop()`에 카메라를 활성화하고 해제하는 코드를 작성해야 한다. -> 이 경우 전반적인 사용자 환경이 저하될 수 있다.
- *UI 관련 리소스나 작업을 해제할 때에는 `onPause()`가 아닌 `onStop()`에서 해제하는 것이 좋다.*

## `onStop()`
- 액티비티가 사용자에게 더이상 표시되지 않을 때 호출된다.
	- ex) 새로 시작된 액티비티가 화면 전체를 차지
	- 액티비티의 실행 완료
- 액티비티가 화면에 보이지 않을 때 필요하지 않은 기능을 모두 정지한다.
	- ex) 애니메이션 일시중지
	- 위치 업데이트 간략화
- CPU를 비교적 많이 소모하는 종료 작업을 수행한다.
	- ex) 데이터를 데이터베이스에 저장
- `onStop()` -> `onRestart()`
	- 액티비티가 다시 시작되는 경우
	- 메모리에 저장된 `Activity` 객체의 정보를 다시 호출한다. 
	- 또한 레이아웃에 있는 `View` 객체의 현재 상태도 기록한다.
	- ex) 사용자가 `EditText` 위젯에 입력한 내용을 따로 저장하거나 복원할 필요가 없다.
- `onStop()` -> `onDestroy()`
	- 액티비티가 실행을 종료하는 경우

## `onDestroy()`
- 액티비티가 소멸되기 전에 호출된다.
	- 사용자가 액티비티를 닫아 종료되는 경우
	- `finish()`가 호출되어 종료되는 경우
	- 구성 변경으로 인해 일시적으로 소멸시키는 경우
		- ex) 화면 회전, 멀티 윈도우 모드
- 이전 콜백(`onStop()`)에서 아직 해제되지 않은 모든 리소스를 해제해야 한다.
- 액티비티에 소멸될 때 필요한 로직을 입력하는 대신 `ViewModel` 객체를 사용한다.
	- 액티비티의 뷰 데이터를 포함해야 한다.
	- 액티비티가 구성 변경으로 다시 생성되는 경우, `ViewModel`의 뷰 데이터는 그대로 보존되어 다음 액티비티 인스턴스에 전달된다. 추가 작업이 필요하지 않다.
	- 액티비티가 다시 생성되지 않을 경우 `ViewModel`의 `onCleared()`를 호출하여 액티비티가 소멸되기 전에 데이터를 해제한다.
- 액티비티가 종료되어 `onDestroy()`가 호출되는 경우, `onDestroy()`가 액티비티의 마지막 콜백이 된다.
- 구성 변경으로 인해 `onDestroy()`가 호출되는 경우, 즉시 새로운 액티비티 인스턴스를 생성하여 `onCreate()`를 호출한다.
- 위 두 경우를 `isFinishing()`으로 구분할 수 있다.
	- `true`: `finish()`에 의해 액티비티가 소멸된 경우
	- `false`: 시스템에 의해 액티비티가 소멸된 경우

## 액티비티 상태

|종료 가능성|프로세스 상태|액티비티 상태|
|------|---|---|
|낮음|포그라운드(포커스 있거나 가져올 예정)|create/start/resume|
|중간|백그라운드(포커스 잃음)|pause|
|높음|백그라운드(보이지 않음)|stop|
|높음|    |destroy|


## UI 상태 저장 및 복원
- 화면 회전 또는 멀티 윈도우 모드로 전환되어도 UI 상태가 동일하게 유지되어야 한다.
- 그러나 시스템은 이러한 구성 변경이 발생하면 액티비티를 소멸시키고 UI 상태를 제거한다.
- `ViewModel`, `onSaveInstanceState()`, 로컬 저장소를 사용하여 사용자의 UI 상태를 보존해야 한다.
- `onSaveInstanceState()`는 직렬화/역직렬화 비용을 발생시킨다.

#### 인스턴스 상태
- 시스템이 이전 상태를 복원하기 위해 저장한 데이터를 뜻한다.
- `Bundle` 객체에 키-값 쌍으로 저장되어 있다.
- 시스템은 `Bundle` 인스턴스 상태를 통해 액티비티 레이아웃의 `View` 객체 관련 정보를 저장한다. 따라서 액티비티가 소멸되고 재생성되어도, UI 상태는 별도의 작업 없이 이전 상태로 복원된다.
- 하지만 복원할 상태 정보가 많은 경우   `ViewModel`, `onSaveInstanceState()`, 로컬 저장소를 사용하여 사용자의 UI 상태를 보존해야 한다. `Bundle` 객체는 메인 스레드에서 직렬화되고 시스템 프로세스 메모리를 사용하기 때문에 소량의 데이터를 보존하는 데만 적합하기 때문이다.

#### `onSaveInstanceState()`으로 간단한 UI 상태 저장
- 액티비티가 정지되기 시작하면, 시스템은 인스턴스 상태 번들에 상태 정보를 저장하기 위해 `onSaveInstanceState()`를 호출한다. 기본적으로 뷰에 대한 임시 정보를 저장한다.
- 액티비티의 추가적인 인스턴스 상태 정보를 저장하려면 `onSaveInstanceState()`를 override한다.
```kotlin
override fun onSaveInstanceState(outState: Bundle?) {
	outState?.run {
		putInt(STATE_SCORE, currentScore)
		putInt(STATE_LEVEL, currentLevel)
	}
	
	super.onSaveInstanceState(outState)  
}  
```
- 이 함수는 사용자가 활동을 명시적으로 닫거나 `finish()`가 호출된 경우는 호출되지 않는다.
- 구성 변경이 발생한 경우에만 호출된다.
- 임시적인 상태가 아닌 영구 데이터를 저장하려면, 액티비티가 포그라운드에 있을 때나 `onStop()`에서 저장한다.

#### `onSaveInstanceState()`으로 UI 상태 복원
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
	super.onCreate(savedInstanceState)
	if (savedInstanceState != null) {
		// 상태 복원
		with(savedInstanceState) {
			currentScore = getInt(STATE_SCORE)
			currentLevel = getInt(STATE_LEVEL)
		}
	} else {
		// 새 인스턴스인 경우 기본값으로 초기화
	}
}
```
- `onCreate()`는 액티비티를 새로 생성할 때도 호출되기 때문에 `Bundle` 인스턴스가 `null`인지 반드시 확인해야 한다.

```kotlin
override fun onRestoreInstanceState(savedInstanceState: Bundle?) {
	super.onRestoreInstanceState(savedInstanceState)
	savedInstanceState?.run {
		currentScore = getInt(STATE_SCORE)
		currentLevel = getInt(STATE_LEVEL)
	}
}
```
- `onRestoreInstanceState()`를 구현해도 상태를 복원할 수 있다.
- 이 함수는 `onStart()` 콜백 이후에 호출된다.
- 복원할 상태가 있는 경우에만 호출되기 때문에, `Bundle` 인스턴스가 `null`인지 확인하지 않아도 된다.
