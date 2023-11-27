# Bound Service
- 구성 요소를 서비스에 바인딩하여 요청을 보내고 응답을 수신할 수 있다.
	- 이 때 구성 요소가 클라이언트, 서비스가 서버 역할을 한다.
- 일반적으로 Bound Service는 다른 구성 요소와 바인딩될 때만 유지되고 백그라운드에서 무한히 실행되지 않는다.

## Started 서비스에 바인딩
- `startService()`를 호출하는 started 서비스도 `bindService()`를 호출하여 클라이언트가 서비스에 바인딩될 수 있다.
- 서비스가 시작된 후 바인딩되면, 서비스는 클라이언트가 바인딩을 모두 해제해도 소멸되지 않는다. 이 경우 `stopSelf()`, `stopService()`를 명시적으로 호출해야 한다.

## Bound Service 생성
- 서비스를 생성할 때 `IBinder`를 구현해야 한다.
- 이 `IBinder`는 클라이언트가 서비스와 상호작용할 수 있는 인터페이스를 제공한다.
- `IBinder` 정의 방법
	- 바인더 클래스 확장
		- 서비스가 애플리케이션 내에서만 사용되고, 클라이언트와 같은 프로세스에서 실행되는 경우 `Binder` 클래스를 확장한다. 그 인스턴스를 `onBind()`에서 반환한다.
	- 메신저 사용
		- 인터페이스가 여러 프로세스에서 작동해야 하는 경우 `Messenger`로 서비스 인터페이스를 생성한다. 서비스가 정의하는 `Handler`를 통해 클라이언트와 `IBinder`를 공유한다. 클라이언트는 `Message`를 사용해 서비스에 명령어를 보낼 수 있다.
		- IPC를 수행하는 가장 간단한 방법이다. `Messenger`가 모든 요청을 단일 스레드 큐에 저장하기 때문에 서비스의 Thread-safe를 고려하지 않아도 된다.
	- AIDL
		- Android 인터페이스 정의 언어(AIDL)를 사용하려면 프로그래밍 인터페이스를 정의하는 `.aidl` 파일을 생성한다. Android SDK는 이 파일으로 인터페이스를 구현하고 IPC를 처리하는 추상 클래스를 생성한다. 서비스 내에서 이 추상 클래스를 확장하면 된다.
### 바인더 클래스 확장
- 서비스가 애플리케이션 내에서만 사용되고, 클라이언트와 같은 프로세스에서 실행되는 경우에 적합하다.
- 클라이언트가 서비스 내의 public 함수에 직접 액세스할 수 있다.
- 예를 들어 음악 애플리케이션에서 백그라운드 음악 재생 서비스에 액티비티를 바인딩해야 하는 경우 사용할 수 있다.
<br></br>
- 서비스
	- `Binder` 인스턴스를 생성
	- `onBind()` 콜백 함수에서 `Binder`의 인스턴스를 반환
- 클라이언트
	- 서비스와 연결을 모니터링하는 `ServiceConnection`을 구현
	- `onServiceConnected()` 콜백 함수에서 `Binder`를 받는다.
	- `Binder`로 서비스의 함수를 호출한다.

```kotlin
class LocalService : Service() {
	private val binder = LocalBinder()
	private val mGenerator = Random()

// 클라이언트가 사용할 public 함수
val randomNumber: Int
	get() = mGenerator.nextInt(100)

	// 클라이언트가 사용할 클래스
	inner class LocalBinder : Binder() {
		// 클라이언트가 public 함수를 호출할 수 있도록 인스턴스 반환
		fun getService(): LocalService = this@LocalService
	}

	override fun onBind(intent: Intent): IBinder {
		return binder
	}  
}
```
- 서비스가 `Binder` 구현을 통해 서비스 함수 액세스 권한을 클라이언트에 제공하는 코드다.
- `LocalBinder`는 현재 인스턴스를 반환하는 `getService()`를 클라이언트에 제공한다.

```kotlin
class BindingActivity : Activity() {
    private lateinit var mService: LocalService
    private var mBound: Boolean = false

    // bindService()에 인자로 전달할 콜백
    private val connection = object : ServiceConnection {

        override fun onServiceConnected(className:
        ComponentName, service: IBinder) {
            // IBinder를 cast 하여 LocalService 인스턴스를 가져옴
            val binder = service as LocalService.LocalBinder
            mService = binder.getService()
            mBound = true
        }
  
        override fun onServiceDisconnected(arg0: ComponentName) {
            mBound = false
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main)
    }
  
    override fun onStart() {
        super.onStart()
        // LocalService에 바인딩
        Intent(this, LocalService::class.java).also { intent ->  
            bindService(intent, connection, Context.BIND_AUTO_CREATE)
        }
    }

    override fun onStop() {
        super.onStop()
        unbindService(connection)
        mBound = false
    }

	// 버튼 클릭 시 호출
	fun onButtonClick(v: View) {
        if (mBound) {
            Toast.makeText(this, "number: $num", Toast.LENGTH_SHORT).show()
        }
    }
}
```
- 클라이언트에서 버튼을 클릭했을 때 `LocalService`에 바인딩되어 `Service`의 `getRandomNumber()`를 호출하는 코드다.

### 메신저 사용
- 서비스가 원격 프로세스와 통신해야 하는 경우 적합하다.
- AIDL을 사용하지 않고도 IPC를 수행할 수 있다.
- `Messenger`가 모든 서비스 호출을 큐에 올리기 때문에 한 번에 하나씩 호출을 처리할 수 있다.
<br></br>
- 사용 방법
	- 서비스가 각 클라이언트 호출의 콜백을 받는 `Handler` 구현
	- 서비스가 `Handler`를 사용하여 `Messenger` 객체를 생성
	- `Messenger`가 `IBinder`를 생성하고, 서비스는 `onBind()`에서 이를 반환
	- 클라이언트는 `IBinder`를 사용하여 `Messenger`를 인스턴스화하고, 이를 이용하여 `Messege` 객체를 서비스에 전송
- 클라이언트는 서비스의 함수를 호출하지 않는다. 
	- 대신 클라이언트가 메시지(`Message`  객체)를 전달한다. 
	- 서비스의 `Handler`에서 이 메시지를 받는다.

```kotlin
// 메시지를 표시하는 명령어
private const val MSG_SAY_HELLO = 1

class MessengerService : Service() {
	// 클라이언트가 IncomingHandler에게 메시지를 보내는 대상
	private lateinit var mMessenger: Messenger

	// 클라이언트에서 수신한 메시지의 Handler
	internal class IncomingHandler(
        context: Context,
        private val applicationContext: Context = context.applicationContext
    ) : Handler() {
        override fun handleMessage(msg: Message) {
            when (msg.what) {
                MSG_SAY_HELLO ->
                    Toast.makeText(applicationContext, "hello!", Toast.LENGTH_SHORT).show()
                else -> super.handleMessage(msg)
            }
        }
    }

	// 서비스에 바인딩할 때, 메신저로 메시지를 보낼 수 있는 인터페이스를 반환
    override fun onBind(intent: Intent): IBinder? {
        Toast.makeText(applicationContext, "binding", Toast.LENGTH_SHORT).show()
        mMessenger = Messenger(IncomingHandler(this))
        return mMessenger.binder
    }
```
- `Handler`의 `handleMessage()`에서 클라이언트에서 수신한 `Message`를 받고, `what`을 통해 무엇을 수행할지 결정한다.

```kotlin
class ActivityMessenger : Activity() {
	// 서비스와 통신할 Messenger
    private var mService: Messenger? = null

	// 서비스와 바인딩되었는지 여부를 나타내는 플래그
    private var bound: Boolean = false

	private val mConnection = object : ServiceConnection {
        override fun onServiceConnected(className: ComponentName, service: IBinder) {
	        // IBinder를 통해 Messenger 인스턴스 생성
			mService = Messenger(service)
            bound = true
        }

        override fun onServiceDisconnected(className: ComponentName) {
	        mService = null
            bound = false
        }
    }

    fun sayHello(v: View) {
        if (!bound) return
		// what 값을 사용하여 메시지를 생성하고 서비스에 전송
        val msg: Message = Message.obtain(null, MSG_SAY_HELLO, 0, 0)
        try {
            mService?.send(msg)
        } catch (e: RemoteException) {
            e.printStackTrace()
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main)
    }

    override fun onStart() {
        super.onStart()
		// 서비스에 바인딩
        Intent(this, MessengerService::class.java).also { intent ->
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE)
        }
    }

    override fun onStop() {
        super.onStop()
        // 서비스와 바인딩 해제
        if (bound) {
            unbindService(mConnection)
            bound = false
        }
    }
}
```
- 클라이언트는 서비스가 반환한 `IBinder`를 통해 `Messenger`를 생성하고 `send()`로 메시지를 전송한다.

## 서비스에 바인딩
1. 클라이언트가 `ServiceConnection`을 구현한다.
	- `onServiceConnected()`
		- 시스템이 서비스와의 바인딩을 완료했을 때 호출된다.
		- 서비스의 `onBind()`에서 반환한 `IBinder`를 매개변수로 전달받는다.
	- `onServiceDisconnected()`
		- 서비스의 연결이 예기치 못하게 끊어진 경우 호출된다.
		- 클라이언트가 바인딩을 해제할 때는 호출되지 않는다.
2. 클라이언트가 `ServiceConnection` 인스턴스를 인자로 `bindService()`를 호출한다.
	- 바인딩은 비동기식으로 처리되기 때문에, `bindService()`에서 `IBinder`를 반환하지 않는다.
	- `bindService()`의 반환 값은 서비스가 존재하는지, 클라이언트에게 서비스 액세스 권한이 있는지 여부를 나타낸다.
		- true: 서비스 사용 가능
		- false: 서비스 사용 불가능
	- false를 반환한 경우에도 `unbindService()`를 호출하여 유휴 상태인 서비스를 종료한다.
3. 서비스의 `onBind()`가 호출되고, 서비스와 상호작용하기 위한 `IBinder`를 반환한다.
4. 시스템이 `onServiceConnected()`를 호출한다. 서비스와 통신을 시작할 수 있다.
5. 클라이언트가 서비스로부터 바인딩을 해제하기 위해 `unbindService()`를 호출한다.
	- 클라이언트가 소멸될 때 아직 서비스에 바인딩되어 있는 경우, 자동으로 바인딩이 해제된다.
	- 클라이언트가 서비스와의 상호작용을 마치면 바로 바인딩을 해제해야 한다.

```kotlin
Intent(this, LocalService::class.java).also { intent ->
	bindService(intent, connection, Context.BIND_AUTO_CREATE)
}
```

- `bindService()`의 매개변수
	- intent
		- 바인딩할 서비스의 이름을 명시적으로 지정한 `Intent`
	- connection
		- `ServiceConnection` 객체
	- flags
		- 바인딩 옵션을 나타내는 플래그
		- `BIND_AUTO_CREATE`: 서비스가 아직 활성화되지 않은 경우 서비스 생성 (Default)
		- `BIND_DEBUG_UNBIND`
		- `BIND_NOT_FOREGROUND`
		- 0 - 플래그를 사용하지 않는 경우

#### 참고 사항
- `DeadObjectException` 예외를 처리해야 한다. 연결이 끊어질 때 발생한다.
- 클라이언트의 수명 주기에 맞게 바인딩하고 바인딩 해제해야 한다.
	- 액티비티가 표시될 때만 서비스와 상호작용
		- `onStart()`에서 바인딩
		- `onStop()`에서 바인딩 해제
	- 백그라운드에서 액티비티가 중단되었을 때도 서비스와 상호작용
		- `onCreate()`에서 바인딩
		- `onDestroy()`에서 바인딩 해제
	- 일반적으로 수명 주기 전환의 처리를 최소화하기 위해 `onResume()`과 `onPause()`에서는 바인딩하고 바인딩 해제하지 않는다.

## Bound Service 수명 주기
- 서비스가 다른 구성 요소와 바인딩만 되는 경우, 수명 주기를 따로 관리하지 않아도 된다.
- 클라이언트에 바인딩되었는지 여부에 따라 시스템이 대신 관리해주기 때문이다.

![service_binding_tree_lifecycle](https://github.com/kimhm0728/TIL/assets/70271235/f5e95a0a-55a0-48ff-b463-a2476d292692)

- 그러나 `onStartCommand()` 콜백 함수를 구현했다면 (started 서비스와 함께 사용) 서비스를 명시적으로 중지해야 한다.
- 서비스가 클라이언트에 바인딩되었는지 여부에 관계없이, `stopSelf()`, `stopService()`를 호출할 때까지 서비스가 실행되기 때문이다.
- `onRebind()`
	- 다음에 클라이언트가 서비스에 다시 바인딩될 때 호출된다.
	- `onUnbind()`에서 `true`를 반환할 때만 호출된다.
	- 반환값을 가지고 있지 않지만, 다시 바인딩될 때도 `onServiceConnected()`가 호출된다.
