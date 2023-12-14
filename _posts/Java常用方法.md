---
title: Java常用方法整理
categories:
  - Java
  - LeetCode
date: 2023-05-20 14:30:00
---
# Java常用方法整理

## Object 类

| 方法               | 描述                   |
| ------------------ | ---------------------- |
| equals(Object obj) | 判断对象是否相等。     |
| hashCode()         | 获取对象的哈希码。     |
| toString()         | 获取对象的字符串表示。 |
| getClass()         | 获取对象的类。         |
| wait()             | 等待其他线程通知。     |
| notify()           | 唤醒等待中的线程。     |
| notifyAll()        | 唤醒所有等待中的线程。 |

## Math 类

| 方法      | 描述                           |
| --------- | ------------------------------ |
| max(a, b) | 返回a和b中较大的值。           |
| min(a, b) | 返回a和b中较小的值。           |
| abs(x)    | 返回x的绝对值。                |
| pow(x, y) | 返回x的y次幂。                 |
| sqrt(x)   | 返回x的平方根。                |
| random()  | 返回一个0到1之间的随机浮点数。 |
| round(x)  | 对浮点数进行四舍五入。         |
| floor(x)  | 返回不大于x的最大整数。        |
| ceil(x)   | 返回不小于x的最小整数。        |

## Random 类

| 方法              | 描述                             |
| ----------------- | -------------------------------- |
| nextInt(bound)    | 返回一个0到bound之间的随机整数。 |
| nextDouble()      | 返回一个0到1之间的随机浮点数。   |
| nextBoolean()     | 返回一个随机的布尔值。           |
| nextBytes(byte[]) | 将随机字节填充到字节数组。       |

## String 类

| 方法                  | 描述                           |
| --------------------- | ------------------------------ |
| length()              | 返回字符串的长度。             |
| charAt(index)         | 返回指定索引处的字符。         |
| substring(begin, end) | 截取子字符串。                 |
| indexOf(str)          | 查找子字符串第一次出现的位置。 |
| equals(str)           | 判断字符串是否相等。           |
| compareTo(str)        | 比较字符串的大小关系。         |
| startsWith(prefix)    | 判断字符串是否以指定前缀开头。 |
| endsWith(suffix)      | 判断字符串是否以指定后缀结尾。 |
| toUpperCase()         | 将字符串转换为大写。           |
| toLowerCase()         | 将字符串转换为小写。           |
| trim()                | 去除字符串两端的空格。         |

## StringBuilder/StringBuffer 类

| 方法                | 描述                                          |
| ------------------- | --------------------------------------------- |
| append(str)         | 追加字符串。                                  |
| insert(offset, str) | 插入字符串。                                  |
| delete(start, end)  | 删除指定范围的字符。                          |
| reverse()           | 反转字符串。                                  |
| toString()          | 将 StringBuilder/StringBuffer 转换为 String。 |
| capacity()          | 获取容量。                                    |
| ensureCapacity(min) | 确保容量至少为指定值。                        |
| length()            | 返回长度。                                    |
| setLength(length)   | 设置长度。                                    |

## Arrays 类

| 方法                   | 描述                             |
| ---------------------- | -------------------------------- |
| sort(arr)              | 对数组进行排序。                 |
| binarySearch(arr, key) | 在排序数组中查找指定元素的索引。 |
| copyOf(arr, length)    | 复制数组的一部分。               |
| fill(arr, value)       | 将数组中的元素都设置为指定值。   |
| equals(arr1, arr2)     | 比较两个数组是否相等。           |

## Collections 类

| 方法                          | 描述                                 |
| ----------------------------- | ------------------------------------ |
| sort(list)                    | 对列表进行排序。                     |
| binarySearch(list, key)       | 在排序列表中查找指定元素的索引。     |
| reverse(list)                 | 反转列表。                           |
| shuffle(list)                 | 随机打乱列表中的元素。               |
| max(collection)               | 返回集合中的最大元素。               |
| min(collection)               | 返回集合中的最小元素。               |
| frequency(collection, obj)    | 返回集合中指定元素的出现次数。       |
| indexOfSubList(list, subList) | 返回子列表第一次出现在列表中的位置。 |

## List 接口

| 方法                | 描述                               |
| ------------------- | ---------------------------------- |
| add(element)        | 向列表末尾添加元素。               |
| add(index, element) | 在指定位置插入元素。               |
| remove(index)       | 移除指定位置的元素。               |
| get(index)          | 获取指定位置的元素。               |
| set(index, element) | 设置指定位置的元素。               |
| size()              | 返回列表的大小。                   |
| indexOf(element)    | 返回元素第一次出现在列表中的位置。 |
| subList(from, to)   | 返回子列表。                       |

## Set 接口

| 方法              | 描述                               |
| ----------------- | ---------------------------------- |
| add(element)      | 向集合中添加元素。                 |
| remove(element)   | 从集合中移除元素。                 |
| contains(element) | 判断集合是否包含指定元素。         |
| size()            | 返回集合的大小。                   |
| isEmpty()         | 判断集合是否为空。                 |
| iterator()        | 返回迭代器，用于遍历集合中的元素。 |

## Map 接口

| 方法             | 描述                       |
| ---------------- | -------------------------- |
| put(key, value)  | 将键值对添加到映射中。     |
| get(key)         | 根据键获取值。             |
| remove(key)      | 移除指定键的键值对。       |
| containsKey(key) | 判断映射中是否包含指定键。 |
| keySet()         | 返回所有键组成的集合。     |
| values()         | 返回所有值组成的集合。     |
| entrySet()       | 返回所有键值对组成的集合。 |

## Queue/LinkedList 类

| 方法           | 描述                           |
| -------------- | ------------------------------ |
| offer(element) | 将元素添加到队列中。           |
| poll()         | 移除并返回队列头部的元素。     |
| peek()         | 返回队列头部的元素，但不移除。 |
| size()         | 返回队列中的元素个数。         |
| isEmpty()      | 判断队列是否为空。             |

## Scanner 类

| 方法         | 描述                               |
| ------------ | ---------------------------------- |
| next()       | 读取下一个输入项（字符串）。       |
| nextInt()    | 读取下一个输入项作为整数。         |
| nextDouble() | 读取下一个输入项作为双精度浮点数。 |
| nextLine()   | 读取当前行剩余部分并返回。         |
| hasNext()    | 判断是否还有输入项。               |
| close()      | 关闭 Scanner 对象。                |

## File 类

| 方法            | 描述                       |
| --------------- | -------------------------- |
| exists()        | 判断文件是否存在。         |
| isDirectory()   | 判断是否是目录。           |
| isFile()        | 判断是否是文件。           |
| getName()       | 返回文件名。               |
| getPath()       | 返回文件路径。             |
| createNewFile() | 创建新文件。               |
| delete()        | 删除文件。                 |
| list()          | 返回目录中的文件和子目录。 |

## Thread 类

| 方法        | 描述                     |
| ----------- | ------------------------ |
| start()     | 启动线程。               |
| run()       | 线程执行的主体。         |
| join()      | 等待线程执行完成。       |
| sleep()     | 使当前线程睡眠指定时间。 |
| isAlive()   | 判断线程是否存活。       |
| interrupt() | 中断线程。               |

## Runnable 接口

| 方法  | 描述             |
| ----- | ---------------- |
| run() | 线程执行的主体。 |

## InputStream/OutputStream 类

| 方法                 | 描述                 |
| -------------------- | -------------------- |
| read()               | 读取一个字节的数据。 |
| read(byte[] buffer)  | 读取多个字节的数据。 |
| close()              | 关闭流。             |
| write()              | 写入一个字节的数据。 |
| write(byte[] buffer) | 写入多个字节的数据。 |

## Reader/Writer 类

| 方法                 | 描述                 |
| -------------------- | -------------------- |
| read()               | 读取一个字符的数据。 |
| read(char[] buffer)  | 读取多个字符的数据。 |
| close()              | 关闭流。             |
| write()              | 写入一个字符的数据。 |
| write(char[] buffer) | 写入多个字符的数据。 |

## 格式转换的常用方法

| 类型转换         | 方法                                      | 描述                         |
| ---------------- | ----------------------------------------- | ---------------------------- |
| 字符串到整数     | Integer.parseInt(String str)              | 将字符串转换为整数。         |
| 字符串到浮点数   | Double.parseDouble(String str)            | 将字符串转换为双精度浮点数。 |
| 整数到字符串     | String.valueOf(int value)                 | 将整数转换为字符串。         |
| 浮点数到字符串   | String.valueOf(double value)              | 将双精度浮点数转换为字符串。 |
| 字符串到布尔值   | Boolean.parseBoolean(String str)          | 将字符串转换为布尔值。       |
| 布尔值到字符串   | String.valueOf(boolean value)             | 将布尔值转换为字符串。       |
| 字符串到日期     | SimpleDateFormat.parse(String str)        | 将字符串转换为日期。         |
| 日期到字符串     | SimpleDateFormat.format(Date date)        | 将日期转换为字符串。         |
| 字符串到字节数组 | str.getBytes()                            | 将字符串转换为字节数组。     |
| 字节数组到字符串 | new String(byte[] bytes)                  | 将字节数组转换为字符串。     |
| 字符串到枚举     | Enum.valueOf(Class enumType, String name) | 将字符串转换为枚举类型。     |
| 枚举到字符串     | enumValue.name()                          | 将枚举类型转换为字符串。     |

## 数组和集合类型的转换

| 类型转换        | 方法                                                            | 描述                                             |
| --------------- | --------------------------------------------------------------- | ------------------------------------------------ |
| Array 转 List   | Arrays.asList(T... array)                                       | 将数组转换为 List。                              |
| List 转 Array   | list.toArray(new T[list.size()])                                | 将 List 转换为数组。                             |
| Set 转 List     | new ArrayList<>(set)                                            | 将 Set 转换为 List。                             |
| List 转 Set     | new HashSet<>(list)                                             | 将 List 转换为 Set。                             |
| Map 转 List     | new ArrayList<>(map.entrySet())                                 | 将 Map 转换为 List，每个元素为 Map 的 Entry。    |
| List 转 Map     | list.stream().collect(Collectors.toMap(keyMapper, valueMapper)) | 将 List 转换为 Map，指定 key 和 value 转换方法。 |
| Array 转 String | Arrays.toString(array)                                          | 将数组转换为字符串。                             |
| String 转 Array | str.split(regex)                                                | 将字符串分割为数组。                             |
| List 转 String  | String.join(delimiter, list)                                    | 将 List 中的元素用指定分隔符连接为字符串。       |
| String 转 List  | Arrays.asList(str.split(delimiter))                             | 将字符串分割后转换为 List。                      |
