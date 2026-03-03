## kotlin 作用域函数 run with apply aslo let
```kotlin
public inline fun <T, R> T.run(block: T.() -> R): R {
    return block()
}

public inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}

public inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

public inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}

public inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}
```

![](https://cdn.jsdelivr.net/gh/flycool/pic@main/data/kotlin_api.png)