---
layout: post
title:  "JavaScript ArrayBuffers And Typed Arrays"
date:   2018-11-02 20:21:34 +0800
categories: JavaScript
---

这两天看一篇前端将数据保存在图片上的文章，发现里面有用到`Uint8Array`，之前也没了解过这个类型是干嘛用的，就谷歌了一下，发现一篇很简单易懂的文章，就翻译下来，存个地。[原文链接](http://qnimate.com/javascript-arraybuffers-and-typed-arrays/#prettyPhoto)


在这篇文章中，我将解释`ArrayBuffer`和`Typed Arrays`分别是什么以及它们是如何使用的。所有的 API 都来自 HTML5 规格

### JavaScript Arrays vs ArrayBuffers
JavaScript中`Array`的元素可以是任何类型(strings, numbers, arrays, objects等)。JavaScript中`Array`没有数组长度的大小限制，可以动态的增长。`Array`的问题是执行效率低，占用更多的内存。

当遇到需要处理大数据和大计算量的程序时就会出现问题，这时就需要`ArrayBuffers`。

一个`ArrayBuffer`是一个8比特(bit)块的集合(一个字节(byte)显示为一个`ArrayBuffer`的元素)。`ArrayBuffer`有固定的数组长度，所以不能动态增长。`ArrayBuffer`只能存储数字。创建一个`ArrayBuffer`的时候，所有字节被初始化为数字0。


### ArrayBuffer
`ArrayBuffer`可以通过使用`ArrayBuffer`类来创建
```
var buffer = new ArrayBuffer(100); //100 bytes, 原文中描述有误
```
可以使用`DataView`从`ArrayBuffer`中读写数据。在`ArrayBuffer`中，并没有固定使用8个比特位(bit)表示一个数字，可以使用8、16、32、64比特位来表示数组
```
var buffer = new ArrayBuffer(100);
var view = new DataView(buffer);

//first arguement -> offset, represent from which byte we want to write the value
//second arguement -> value to be stored
//third arguement -> boolean -> Endianness -> false represents big-endian
//setInt32 represents write the value to 32bits. Value is signed integer. 
view.setInt32(8,22,false);//here we are writing 22 in big-endian after first byte.

//lets retrieve and print the value
var number = view.getInt32(8,false);
document.getElementById("number").innerHTML = number;
```
`DataView`提供了许多将数据写入`ArrayBuffer`的方法：
1. setInt8: 使用8个比特位来存储一个数值，它接受一个带符号的整型数值(正数或者负数)
2. setUint8: 使用8个比特位来存储一个数值，它接受一个无符号的整型数值(正数)
3. setInt16: 使用16个比特位来存储一个数值，它接受一个带符号的整型数值
4. setUint16: 使用16个比特位来存储一个数值，它接受一个无符号的整型数值
5. setInt32: 使用32个比特位来存储一个数值，它接受一个带符号的整型数值
6. setUint32: 使用32个比特位来存储一个数值，它接受一个无符号的整型数值
7. setFloat32: 使用32个比特位来存储一个数值，它接受一个带符号的浮点型数值
8. setFloat64: 使用64个比特位来存储一个数值，它接受一个带符号的浮点型数值

同样`DataView`提供了许多从`ArrayBuffer`中读取数据的方法
1. getInt8: 读8个比特位。返回一个带符号的整型数值
2. getUint8: 读8个比特位。返回一个不带符号的整型数值
3. getInt16: 读16个比特位。返回一个带符号的整型数值
4. getUint16: 读16个比特位。返回一个不带符号的整型数值
5. getInt32: 读32个比特位。返回一个带符号的整型数值
6. getUint32: 读32个比特位。返回一个不带符号的整型数值
7. getFloat32: 读32个比特位，返回一个带符号的浮点型数值
8. getFloat64: 读64个比特位，返回一个带符号的浮点型数值

更多信息请参考[字节顺序](https://zh.wikipedia.org/wiki/%E5%AD%97%E8%8A%82%E5%BA%8F)

### Typed Arrays
在上面的描述中，我们已经知道如何在`ArrayBuffer`中读写数字，但是上面的方法并不好，因为我们必须每次都调用方法才可以。如果我们想像使用JavaScript中的数组一样读写`ArrayBuffer`要怎么办呢，这就是`Typed Array`的功能

Typed Arrays可以使用不同大小块来显示`ArrayBuffer`中的数据。它提供了JavaScript中读写数组的方法来读写`ArrayBuffer`中的数据

![assets/img/array.jpg]

上图显示了8比特位与16比特位在`ArrayBuffer`中的表示方式

```
var buffer = new ArrayBuffer(80);
//first arguement-> reference to representing buffer
//second arguement-> offset
//third arguement-> size of typed array
var typed_array = new Int16Array(buffer, 0, 40);//created a typed array representing the complete ArrayBuffer

typed_array[0] = 12;
typed_array[39] = 2;
```
与上面类似，我们也可以通过创建不同块大小的typed array来存储数据
1. Int8Array: 创建一个单位为8个比特位的 typed array，存储值为带符号的数值
2. Uint8Array: 创建一个单位为8个比特位的 typed array，存储值为不带符号的数值
3. Int16Array: 创建一个单位为16个比特位的 typed array，存储值为带符号的数值
4. Uint16Array: 创建一个单位为16个比特位的 typed array，存储值为不带符号的数值
5. Int32Array: 创建一个单位为32个比特位的 typed array，存储值为带符号的数值
6. Uint32Array: 创建一个单位为32个比特位的 typed array，存储值为不带符号的数值
7. Float32Array: 创建一个单位为32个比特位的 typed array，存储值为带符号的浮点型数值
8. Float64Array: 创建一个单位为64个比特位的 typed array，存储值为带符号的浮点型数值

我们也可以不用`ArrayBuffer`来创建Typed Array。在这种情况下，浏览器会自动创建一个与Typed Array大小相同的`ArrayBuffer`
```
var typed_array = new Uint8Array(1024);//a typed array of size 1024 bytes
var copy = new Uint8Array(typed_array);//a typed array from another types array. A copy is produced
var typed_array_2 = new Int32Array([0,1,2,3]);//a typed array from javascript arrays.
```

Typed Array也定义了数组区域查询和赋值的方法
```
//The set() method copies the elements of javascript arrays or typed arrays into a typed array
var typed_array_1 = new Uint8Array(1024);
var typed_array_2 = new Uint8Array([0,1,2,3]);
typed_array_1.set(typed_array_2); // Copy them to the start of another byte array
typed_array_1.set(typed_array_2, 4);// Copy them again at a different offset 
typed_array_1.set([0,1,2,3], 8); //Or just copy values direct from a regular array
```

### Conclusion

`Blob`给我们提供了获取它内部的`ArrayBuffer`的数据的方法。二进制文件由数字和字符串组成，所以我们可以通过Typed Array和DataView来读取`Blob`的数据
`ArrayBuffer`被用在**canvas**和**WebGL**api中。它还用在其他很多地方，这篇文章只给出了一个概览，给大家一个入门的介绍。
