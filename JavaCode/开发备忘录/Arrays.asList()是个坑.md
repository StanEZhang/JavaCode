 该方法是将**数组转化成List集合**的方法。
```shell
String[] strings = {"aa", "bb", "cc"};
List<String> stringList = Arrays.asList(strings);
```
注意：

1. 该方法适用于对象型数据的数组（`String`、`Integer`）
2. 该方法不建议使用于基本数据类型的数组（`byte`, `short`, `int`, `long`, `float`, `double`, `boolean`）
3. 该方法将数组与`List`列表链接起来：当更新其一个时，另一个自动更新
4. 不支持`add()`、`remove()`、`clear()`等方法

用此方法得到的List的**长度是不可改变**的。
当你向这个List添加或删除一个元素时，程序就会抛出异常（`java.lang.UnsupportedOperationException`）。
**总结：如果你的`List`只是用来遍历，就用`Arrays.asList()`。**
**如果你的List还要添加或删除元素，还是乖乖地`new`一个`java.util.ArrayList`，然后一个一个的添加元素。**