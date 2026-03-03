## kotlin inline noinline crossline
`const val` 内联变量
`inline` 内联函数 , 可以有非局部返回，即，在lambda内`return` (可以return@label )
`noline` 不内联，在需要用函数对象时使用
`crossline` 增强内联， 在函数被间接使用时用，这时， 不允许在lambda内`return`