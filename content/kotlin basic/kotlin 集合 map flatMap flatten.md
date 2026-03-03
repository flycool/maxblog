## kotlin 集合 map flatMap flatten
## map
map就是将`transform`方法产生的结果添加到一个新的集合里面去，然后返回这个新的集合
```kotlin
/**
 * Returns a list containing the results of applying the given [transform] function
 * to each element in the original collection.
 * 
 * @sample samples.collections.Collections.Transformations.map
 */
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}

public inline fun <T, R, C : MutableCollection<in R>> Iterable<T>.mapTo(destination: C, transform: (T) -> R): C {
    for (item in this)
        destination.add(transform(item))
    return destination
}
```
## flatten
flatten 处理嵌套集合，遍历这个嵌套集合，将每一个子集合中的元素通过`addAll`方法添加到新的集合中，最终得到一个扁平化的集合
```kotlin
/**
 * Returns a single list of all elements from all collections in the given collection.
 * @sample samples.collections.Iterables.Operations.flattenIterable
 */
public fun <T> Iterable<Iterable<T>>.flatten(): List<T> {
    val result = ArrayList<T>()
    for (element in this) {
        result.addAll(element)
    }
    return result
}
```
## flatMap
flatMap 遍历集合中的元素，然后将每个元素传入`transform`中处理后得到一个列表，将这个列表的所有元素添加到`destination`中，最终得到一个扁平化的列表
```kotlin
/**
 * Returns a single list of all elements yielded from results of [transform] function being invoked on each element of original collection.
 */
public inline fun <T, R> Iterable<T>.flatMap(transform: (T) -> Iterable<R>): List<R> {
    return flatMapTo(ArrayList<R>(), transform)
}

public inline fun <T, R, C : MutableCollection<in R>> Iterable<T>.flatMapTo(destination: C, transform: (T) -> Iterable<R>): C {
    for (element in this) {
        val list = transform(element)
        destination.addAll(list)
    }
    return destination
}
```

可见，flatMap其实可以看作由flatten和map进行组合之后的方法，组合方式根据具体情况来定。 仅仅是对一个集合进行扁平化操作时，使用flatten就可以了。 如果需要对其中的元素进行一些“加工”，可以考虑使用flatMap