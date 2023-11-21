# 안드로이드에서의 액티비티
- 애플리케이션의 기본 구성 요소
- `main()`에서 실행되는 프로그램들과 달리, 안드로이드에서는 시스템의 수명 주기 단계에 따라 호출된다.


## 액티비티란?
- 사용자가 애플리케이션과 상호작용을 위한 진입점
- 애플리케이션이 UI를 그리는 하나의 화면
- 일반적으로 하나의 액티비티가 화면을 채우지만, 화면보다 작거나 다른 화면 위에 떠있을 수 있다.
- 하나의 애플리케이션에 여러 액티비티가 포함되어 있다.
- 사용자가 애플리케이션을 실행할 때 가장 처음으로 표시되는 화면을 *메인 액티비티*라고 한다.


## 액티비티 구현
- `Activity` 클래스의 서브클래스를 구현한다.
- 애플리케이션의 `manifest`에 액티비티 관련 정보를 등록한다.
- 애플리케이션의 액티비티 간에는 느슨한 결합을 유지해야 한다.


## 액티비티 사용을 위한 manifest 구성
#### 액티비티 선언
```xml
<manifest ... >
	<application ... >
		<activity android:name=".MainActivity">
		</activity>
	</application>
</manifest>
```
액티비티 선언을 위해 `<application>`의 하위 요소에 `<activity>` 요소를 추가해야 한다.
`android:name` 속성으로 액티비티 클래스명을 명시해야 한다.

#### 인텐트 필터 선언
```xml
<activity android:name=".ExampleActivity">
	<intent-filter>
		<action android:name="android.intent.action.SEND" />
		<category android:name="android.intent.category.DEFAULT" />
		<data android:mimeType="text/plain" />
	</intent-filter>
</activity>
```
액티비티가 `startActivity()`를 통해 다른 액티비티를 호출하고자 할 때, 이 인텐트 필터를 기반으로 액티비티를 실행한다.
`<activity>` 안에 `<intent-filter>` 속성을 선언한다.
`<intent-filter>`안에는 `<action>`, `<category>`, `<data>` 요소를 포함시킬 수 있다.

인텐트 필터를 사용하면 다른 앱이 해당 액티비티를 실행시킬 수 있다. 이를 막기 위해서는 액티비티 안에 `android:exported="true"` 속성을 설정한다.

#### 권한 선언
```xml
<manifest>
	<application>
		<activity android:name=".MainActivity"
		android:permission="com.google.socialapp.permission.SHARE_POST">
		</activity>
	</application>
</manifest>
```
위 코드처럼 액티비티에 권한을 설정하려면 애플리케이션 전체에도 동일한 권한을 가지고 있어야 한다.
```xml
<manifest>
	<uses-permission android:name="com.google.socialapp.permission.SHARE_POST" />
</manifest>

```
`<manifest>` 요소 하위에 `<uses-permission>` 요소를 추가해야 한다.


## 액티비티 수명 주기
#### `onCreate()`
- 액티비티를 생성할 때 이 콜백을 필수적으로 구현해야 한다.
- 액티비티 구현 시 필수 구서요소를 초기화하는 작업을 수행한다. (ex: 뷰 생성, 데이터를 리스트에 결합)
- `setContentView()`를 호출해 UI 레이아웃을 정의해야 한다.
- 다음 콜백은 항상 `onStart()`다.
#### `onStart()`
- 액티비티는 `start` 상태로 전환되고 사용자에게 표시된다.
#### `onResume()`
- 액티비티가 사용자와 상호작용 하기 직전에 호출된다.
- `onResume()`이 호출되는 액티비티는 항상 백스택의 맨 위에 있다.
- 모든 사용자 입력을 감지하며, 액티비티의 핵심 기능을 구현한다.
- 다음 콜백은 항상 `onPause()`다.
#### `onPause()`
- 액티비티가 애플리케이션에 부분적으로 표시될 때, 사용자가 액티비티에 포커스를 두고 있지 않아 `stop`, `restart` 상태로 전환되기 직전에 호출된다.
- `onPause()` 완료 후 상황에 따라 `onStop()` 또는 `onResume()`이 호출된다.`
#### `onStop()`
- 액티비티가 애플리케이션에 더 이상 표시되지 않을 때 호출된다.
- 액티비티가 제거되고 있거나, 새 액티비티가 시작도고 있거나, 기존 액티비티가 `restart` 상태일 때 호출된다.
- 다음 콜백은 액티비티가 다시 시작되면 `onRestart()`, 완전히 종료되면 `onDestroy()`이다.
#### `onRestart()`
- `stop` 상태의 액티비티가 다시 시작되려고 할 때 호출된다.
- 액티비티가 중지된 이후부터의 상태를 복원한다.
- 다음 콜백은 항상 `onStart()`다.
#### `onDestroy()`
- 액티비티가 제거되기 전에 호출된다.
- 액티비티의 마지막 콜백이므로 이 콜백에서 액티비티의 모든 리소스를 해제해야 한다.