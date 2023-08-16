# ArrayList的扩容机制

`ArrayList`本身代码失效较简单，相信大家都能自己看懂，这篇文章仅分析`ArrayList`的扩容机制。

<img src="file:///C:/chen/project-demo/blog/img/type.ArrayList.excalidraw.png" title="" alt="" width="500">



## 一、何时扩容

从`add()`方法看起，代码十分简单，如下：

```java
public boolean add(E e) {
    // 1.确保当前数组是否能够容纳新元素
    ensureCapacityInternal(size + 1);
    // 6.将当前元素加入数组
    elementData[size++] = e;
    return true;
}

/**
 * 2.该方法相当于一个代理，
 * 第一步确认添加元素需要的最小容量，第二部判断是否需要扩容
 */
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

/**
 * 3.计算添加一个元素时需要的最低容量
 */
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    // 如果当前数组是一个空数组，则使用默认的初始化容量或最低容量(两者中大较大者)
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

/**
 * 4.确保数组有足够的空间容纳新的元素的加入
 */
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    // 如果放入当前元素需要的最小容量大于数组长度则扩容
    if (minCapacity - elementData.length > 0) {
        // 5.扩容
        grow(minCapacity);
    }
}
```

**总结：**

如果数组的容量在添加元素时，无法容纳新元素，则进行扩容。



## 二、怎么扩容

根据上文可知，扩容即调用`grow()`方法，其代码如下：

```java
private void grow(int minCapacity) {
    // 老容量
    int oldCapacity = elementData.length;
    // 新容量是老容量的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    // 如果新容量小于需要的最低容量，则使用最低容量作为新容量
    // 这里基本不会出现这种情况，除非调用ensureCapacity方法进行手动扩容
    if (newCapacity - minCapacity < 0) {
        newCapacity = minCapacity;
    }
    // 如果新容量大于允许的最大容量
    if (newCapacity - MAX_ARRAY_SIZE > 0) {
        newCapacity = hugeCapacity(minCapacity);
    }
    // 使用新容量创建数组并将老数组的数据拷贝到新数组中
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    // 这里表示最低容量已经超过int的最大值
    if (minCapacity < 0) {
        throw new OutOfMemoryError();
    }
    // 如果最低容量大于允许的最大容量，则最大只能扩容到int的最大值
    return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```

总结：

扩容的机制就是将数组增大1.5倍，然后将老数组的数据复制到新数组中。其中夹杂了对怎样确定新容量的判断。
