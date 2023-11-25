# Service 구현

## manifest에서 선언
```xml
<manifest ... >
	...
	<application ... >
		<service android:name=".ExampleService" />
		...
	</application>  
</manifest>
```
- `<application>` 요소의 하위로 `<service>` 요소를 추가한다.
- `android:name` 속성을 필수적으로 지정해야 한다.
- 서비스를 시작하는데 필요한 권한이나 서비스를 실행해야 하는 프로세스의 특성 등을 지정할 수 있다.
- `android:exported` 속성을 `false`로 설정하면 자신의 애플리케이션 내에서만 서비스를 사용할 수 있다.

- *애플리케이션 보안을 위해, 서비스를 시작할 때는 항상 명시적 인텐트만 사용한다. 서비스에 대한 인텐트 필터를 선언하지 않는다.*
- *사용자는 자신의 기기에서 서비스를 볼 수 있다. 사용자가 서비스를 중단하는 것을 막으려면 `android:description` 속성으로 서비스의 설명을 작성한다.*

## started 서비스 생성
- 다른 구성 요소가 `startService()`를 호출하여 서비스를 시작한다.
- 서비스의 `onStartCommand()`가 호출된다.

- 서비스를 시작한 구성 요소와 독립적인 수명 주기를 가진다.
	- 서비스는 백그라운드에서 무한히 실행될 수 있다.
	- 서비스를 시작한 구성 요소의 소멸 여부와 서비스의 중단은 무관하다.

- 다른 구성 요소가 서비스를 시작하려면, `startService()`를 호출할 때 `Intent`를 인자로 전달한다.
	- 인텐트에 서비스를 지정하고 서비스가 사용할 데이터를 담는다.
	- 서비스는 `onStartCommand()`의 매개변수로 이 인텐트를 받는다.

- start 서비스 생성을 위한 클래스
	- `Service
		- 모든 서비스의 상위 클래스
		- 기본적으로 메인 스레드를 사용하기 때문에, 서비스가 작업을 완료할 수 있는 새로운 스레드를 생성해야 한다.
	- `IntentService`
		- `Service`의 하위 클래스
		- worker 스레드를 사용하여 여러 요청을 하나씩 처리한다.
		- `onHandleIntent()`를 override하여 시작 요청에 대해 인텐트를 수신하고, 백그라운드 작업을 완료하도록 한다.

## `IntentService` 클래스
- 여러개의 요청을 동시에 처리하지 않아도 되는 서비스를 구현할 때 적합하다.
- 작동 방식
	- `onStartCommand()`에 전달된 모든 인텐트를 실행하는 기본 worker 스레드를 생성한다.
	- 인텐트를 한 번에 하나씩 `onHandleIntent()`에 전달하는 작업 큐를 생성한다.
	- `onStartCommand()`에서 인텐트를 작업 큐로 보내고 `onHandleIntent()`를 호출한다.
	- 요청이 모두 처리된 후 서비스를 중단하기 때문에 `stopSelf()`를 호출하지 않아도 된다.
	- `onBind()`이 `null`을 반환한다.
	
```kotlin 
// 슈퍼 클래스의 생성자로 worker 스레드의 이름을 전달
class HelloIntentService : IntentService("HelloIntentService") {
	override fun onHandleIntent(intent: Intent?) {
	// 여기서 백그라운드 작업을 수행한다.
		try {
			Thread.sleep(5000)
		} catch (e: InterruptedException) {
			Thread.currentThread().interrupt()
		}
	}  
}
```
- 생성자의 인자를 전달하고 `onHandleIntent()`만 필수적으로 구현하면 된다.


```kotlin
override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
	Toast.makeText(this, "service starting",Toast.LENGTH_SHORT).show()
	return super.onStartCommand(intent, flags, startId)  
}
```
- 다른 콜백 함수(`onCreate()`, `onStartCommand()` 등)을 override하려면 꼭 슈퍼 클래스의 함수를 호출해야 한다. 그래야 `IntentService`가 worker 스레드의 수명을 적절히 관리할 수 있다.
- `onBind()`는 예외적으로 슈퍼 클래스의 함수를 호출하지 않아도 된다. 

## Service 클래스
- 서비스가 작업 큐로 요청을 하나씩 처리하는 것이 아닌, 멀티스레딩을 수행해야 하는 경우 적합하다.
- 다른 구성 요소의 서비스 호출을 `onStartCommand()`로 직접 처리할 수 있기 때문에 여러 개의 요청을 동시에 수행할 수 있다.
	- 이전 요청이 끝날 때까지 기다리는 대신, 각 요청에 대해 새 스레드를 생성하여 실행하면 된다.

```kotlin
class HelloService : Service() {
	private var serviceLooper: Looper? = null
	private var serviceHandler: ServiceHandler? = null
	
	private inner class ServiceHandler(looper: Looper) : Handler(looper) {
		override fun handleMessage(msg: Message) {
			try {
				Thread.sleep(5000)
			} catch (e: InterruptedException) {
				Thread.currentThread().interrupt()
			}

			// 다른 작업을 처리하는 도중 서비스를 중지하지 않도록
			// startId로 서비스를 중지
			stopSelf(msg.arg1)
		}
	}

	override fun onCreate() {
		// 서비스를 실행하는 스레드를 시작
		HandlerThread("ServiceStartArguments", Process.THREAD_PRIORITY_BACKGROUND).apply {
			start()
			serviceLooper = looper  
            serviceHandler = ServiceHandler(looper)
		}
	}
	
	override fun onStartCommand(intent: Intent, flags: Int, startId: Int): Int {
		Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show()
		// 각 요청에 대해 작업을 시작하고 startId를 전달
		serviceHandler?.obtainMessage()?.also { msg ->
			msg.arg1 = startId  
            serviceHandler?.sendMessage(msg)
		}

		// 시스템이 서비스를 중단하는 경우 다시 시작
		return START_STICKY
	}

	override fun onBind(intent: Intent): IBinder? {
		// 바인딩을 허용하지 않는 경우 null 반환
		return null
	}

	override fun onDestroy() {
		Toast.makeText(this, "service done", Toast.LENGTH_SHORT).show()
	}  
}
```
- `IntentService`와 동일하게 한번에 하나의 요청만 수행하는 코드를 `Service`로 구현한 것이다. 
- `IntentService`와 다르게 구현해야 할 코드의 양이 많아진다.

- `onStartCommand()` 반환값
	- 시스템이 서비스를 중단할 경우 서비스를 유지할지에 대한 값
	- `START_NOT_STICKY`
		- 시스템이 서비스의 `onStartCommand()` 반환 후 중단했다면, 서비스를 재생성하지 않는다.
		- 다만 전달할 `PendingIntent`가 있는 경우는 재생성한다.
		- 서비스가 불필요하게 실행되지 않도록 하는 안전한 옵션이다.
		- 애플리케이션이 완료되지 않은 작업을 다시 시작할 때 유용하다.
	- `START_STICKY`
		- 시스템이 서비스의 `onStartCommand()` 반환 후 중단했다면, 서비스를 다시 생성한다.
		- 생성 후 `onStartCommand()`를 호출하되, 마지막 인텐트는 전달하지 않고 `null` 인텐트가 된다.
		- 다만 전달할 `PendingIntent`가 있는 경우는 해당 인텐트가 전달된다.
		- 무한히 실행 중이고 작업을 기다리는 미디어 플레이어에 적합하다.
	- `START_REDELIVER_INTENT`
		- 시스템이 서비스의 `onStartCommand()` 반환 후 중단했다면, 서비스를 다시 생성한다.
		- `onStartCommand()`에 마지막 인텐트를 전달하여 호출한다.
		- 또한 모든 `PendingIntent`가 차례로 전달된다.
		- 즉시 재개되어야 하는 작업을 수행할 때 적합하다.
## 서비스 시작
- 구성 요소에서 서비스를 시작하려면 `Intent`에 시작할 서비스를 지정하여 `startService()`나 `startForegroundService()`에 전달한다.
- *API 26 이상인 경우 앱이 포그라운드에 있지 않을 때 백그라운드 서비스를 사용/생성하는 것을 제한한다. `startForegroundCommand()`를 호출하면, 백그라운드 서비스를 생성하지만 서비스가 자체적으로 포그라운드로 승격된다. 서비스 생성 후 5초 이내에 `startForeground()`를 호출해야 한다.*

```kotlin
Intent(this, HelloService::class.java).also { intent ->
	startService(intent)
}
```
- 서비스를 시작하기 위해서는 위 코드처럼 명시적 인텐트를 사용한다.

- 서비스가 실행되고 있는 경우 `onStartCommmand()`를 즉시 호출한다.
- 서비스가 실행되고 있지 않는 경우 `onCreate()`를 호출 후 `onStartCommand()`를 호출한다.

- 서비스를 시작한 구성 요소에게 결과를 반환해야 할 경우, `PendingIntent`를 사용한다.
	- 서비스를 시작한 클라이언트가 브로드캐스트를 받기 위해 `getBroadcast()`를 사용해 `PendingIntent`를 만든다.
	- 이를 서비스에 전달하고, 서비스는 이 브로드캐스트를 사용하여 결과를 전달한다.

- 서비스를 여러번 시작하는 경우 `onStartCommand()`도 여러번 호출된다.
- 하지만 서비스를 중단하기 위해서는 한 번만 중단하면 된다.

## 서비스 중단
- 서비스는 자신의 수명 주기를 직접 관리한다.
- `stopSelf()`를 통해 스스로 중단하거나 다른 구성 요소가 `stopService()`를 호출하여 중단시킬 수 있다.
- 서비스가 여러 요청을 동시에 처리하는 경우, 요청을 끝낸 후에도 서비스를 중단하지 않아야 한다. 서비스 중단 후 새로운 요청을 받을 수 있기 때문이다.
	- 이를 해결하기 위해 `stopSelf(int)`를 사용한다. 인자로 `onStartCommand()`의 `startId`를 전달한다. `startId`에 맞는 요청만을 중단하기 때문에, 서비스가 새 요청을 수신하면 `startId`가 일치하지 않아 중단되지 않는다.
- _리소스 낭비, 배터리 소모를 줄이기 위해 서비스가 시작되면 항상 명시적으로 서비스를 중단해야 한다._


## bound 서비스 생성
- `bindService()`를 호출하여 다른 구성 요소를 서비스에 바인딩한다.
- 다른 구성 요소가 서비스와 상호작용이 필요한 경우 적합하다.
- 클라이언트가 서비스와 통신할 방법을 나타내는 `IBinder` 인터페이스를 구현해야 한다. 이를 `onBind()`에서 반환한다.
-  그 후 다른 구성 요소가 `bindService()`를 호출하면, 구성 요소는 `IBinger`에 접근하여 서비스의 함수를 호출할 수 있다.

- 시스템은 서비스와 바인딩된 구성 요소가 없으면 소멸시킨다. `stopSelf()`, `stopService()`를 통해 명시적으로 중단하지 않아도 된다.
- 여러 클라이언트가 하나의 서비스에 바인딩될 수 있다.
- 클라이언트가 서비스와의 상호작용을 완료하면 `unbindService()`를 호출하여 바인딩을 해제한다.
- https://developer.android.com/guide/components/bound-services?hl=ko

## 포그라운드 서비스
- 사용자가 포그라운드 서비스를 인식하고 있기 때문에 메모리가 부족하더라도 시스템은 중단하지 않는다.
- `Notification`로 서비스에 대한 알림을 제공해야 하며, 서비스를 중단하거나 포그라운드에서 제거하지 않는 이상 제거할 수 없다.
- ex) 음악을 재생하는 음악 플레이어

- `Notification`의 우선 순위를 `PRIORITY_LOW` 이상이어야 한다. 사용자가 애플리케이션의 서비스를 확실히 인식해야 하기 때문이다.
- _API 28 이상에서는 포그라운드 서비스를 사용할 때 `FOREGROUND_SERVICE` 권한을 요청해야 한다.

```kotlin
val pendingIntent: PendingIntent =
	Intent(this, ExampleActivity::class.java).let { notificationIntent ->
	PendingIntent.getActivity(this, 0, notificationIntent, 0)
	}  
  
val notification: Notification = Notification.Builder(this, CHANNEL_DEFAULT_IMPORTANCE)
	.setContentTitle(getText(R.string.notification_title))
	.setContentText(getText(R.string.notification_message))
	.setSmallIcon(R.drawable.icon)
	.setContentIntent(pendingIntent)
	.setTicker(getText(R.string.ticker_text))
	.build()  
  
startForeground(ONGOING_NOTIFICATION_ID, notification)
```
- `startForegound()`
	- 서비스가 포그라운드에서 실행되도록 한다.
	- 인자로 알림을 고유하게 식별할 정수와 상태 표시줄에 띄울 `Notification`을 전달한다.

- `stopForeground()`
	- 서비스를 포그라운드에서 제거한다.
	- 인자로 `Notification`의 알림 제거 여부를 나타내는 `Boolean` 값을 전달한다.

## 서비스 수명 주기
- started 서비스
	- 시작: 다른 구성 요소가 `startService()` 호출
	- 중단: 서비스가 `stopSelf()` 호출, 다른 구성 요소가 `stopService()` 호출
- bound 서비스
	- 시작: 다른 구성 요소(클라이언트)가 `bindService()`를 호출
	- 연결 종료: 클라이언트가 `unbindService()`를 호출
	- 소멸: 모든 클라이언트가 바인딩을 해제
- started 서비스에도 다른 구성 요소를 바인딩할 수 있다. 이 경우 모든 클라이언트가 바인딩을 해제해야만 소멸되며, `stopService()`, `stopSelf()`를 호출해도 소멸되지 않는다.

```kotlin
class ExampleService : Service() {
	private var startMode: Int = 0             // 서비스가 중단되었을 때 행동
	private var binder: IBinder? = null        // 바인드하는 클라이언트와 상호작용할 인터페이스
	private var allowRebind: Boolean = false   // onRebind() 사용 여부
	
	override fun onCreate() {
		// 서비스 생성
	}

	override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
		// 서비스 시작
		return _mStartMode_
	}
	
	override fun onBind(intent: Intent): IBinder? {
		// 클라이언트와 바인딩  
		return _mBinder_
	}

	override fun onUnbind(intent: Intent): Boolean {
		// 모든 클라이언트가 언바인드
		return _mAllowRebind_
	}

	override fun onRebind(intent: Intent) {
		// 언바인드 후 다시 바인딩
	}
	
	override fun onDestroy() {
		// 서비스가 더 이상 사용되지 않아 소멸
	}  
}
```
- 액티비티의 수명 주기 콜백과는 다르게, 슈퍼 클래스의 함수를 호출하지 않아도 된다.
