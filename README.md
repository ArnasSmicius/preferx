[![](https://jitpack.io/v/com.vinted/preferx.svg)](https://jitpack.io/#com.vinted/preferx)

# PrefeRx

This is a reactive SharedPreferences library for Kotlin

 - allows to integrate & manage your preferences with RxJava
 - easily stores primitives, enums and other serializable objects

Download
--------

```groovy
dependencies {
  implementation 'com.vinted:preferx:1.1.0'
}
```

Supported types
--------
Library supports default `SharedPreferences` types with default fallback values when preference was not yet set or was cleared.


```kotlin

  sharedPreferences.intPreference("counter", 0)

  sharedPreferences.stringPreference("string", "")

  sharedPreferences.booleanPreference("seen", false)

  sharedPreferences.longPreference("timestamp", 0L)

```

It also allows easy storing of enums and objects.

```kotlin

  sharedPreferences.enumPreference("notifications", Frequency.ALL)

  sharedPreferences.objectPreference(
    name = "current_user"
    defaultValue = User.ANONYMOUS,
    serializer = someSerializer,
    clazz = User::class
  )
```

Basic example
--------

```kotlin
class ExampleActivity : Activity {

  private val sharedPreferences by lazy {
    getSharedPreferences("app-pref", Context.MODE_PRIVATE)
  }

  private val sessionCounter : IntPreference by lazy {
    sharedPreferences.intPreference("counter", 0)
  }

  @Override
  public fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple_activity)

    val incrementedCount = sessionCounter.get() + 1
    sessionCounter.set(incrementedCount)

    counter_view.text = "Application was started $incrementedCount times"
  }
}
```

RxJava example
--------

```kotlin
class ExampleActivity : Activity {

  ...

  private var disposable: Disposable? = null

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    Observable.just(stringPreference.get())
      .concatWith(stringPreference.onChangeObservable)
      .subscribe {
          text_view.text = it
      }
    }.apply { disposable = this }
  }

  override fun onDestroy() {
    disposable?.dispose()
    super.onDestroy()
  }

  ...
}
```

For more usage examples see:
 - [preferences with dagger](app/src/main/kotlin/com/vinted/preferx/examples/DaggerActivityExample.kt)
 - [preferences in Rx stream](app/src/main/kotlin/com/vinted/preferx/examples/RxActivityExample.kt)


Caveats
--------

 - When `EnumSerializer` fails to resolve enum value for any reason it will fallback to default value
 - When using `EntitySerializer` one must ensure that it returns value. In case of `null` it will fallback to default value.
