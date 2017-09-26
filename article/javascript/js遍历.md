# js中的遍历

标签（空格分隔）： js

---
js有如下两种数据需要经常遍历
数组(Array)
```
var demoArr = ['Javascript', 'Gulp', 'CSS3', 'Grunt', 'jQuery', 'angular'];
```
对象(Object)
```
var demoObj = {
    aaa: 'Javascript',
    bbb: 'Gulp',
    ccc: 'CSS3',
    ddd: 'Grunt',
    eee: 'jQuery',
    fff: 'angular'
};
```

 1. for循环
 只能遍历数组，不能遍历对象
 2. do/while
 只支持数组遍历，用于对循环退出条件不是很明确的场景
 3. forEach
 只能遍历数组，不能遍历对象
不能退出循环
 break continue会报错，return同for循环中的continue
 4. for in
 可以循环数组，也可遍历对象，遍历常用对象时使用
其余同for
 5. for of
 常见循环数组或类数组，map对象，es6语法
 6. 尾部调用
 处理比较复杂的数据
 7. map
 支持数组和形似数组的对象
 8. $.each
 可以循环数组，也可遍历对象
 $.each和$().each循环只能通过return false 来退出循环, 使用return 或 return true 将跳过一个元素, 继续执行后面的循环.

## 1，for循环
 **for循环只能遍历数组, 不能遍历对象.**
```
(function() {
　for(var i=0, len=demoArr.length; i<len; i++) {
　　if (i == 2) {
　　　// return;   // 函数执行被终止
　　　// break;    // 循环被终止
　　　continue; // 循环被跳过
　　};
　　console.log('demo1Arr['+ i +']:' + demo1Arr[i]);
　}
})();
```
## 2，do/while循环
**do/while 循环与for循环大体差不多,只支持数组遍历, 多用于对循环退出条件不是很明确的场景. 一般来说不建议使用这种方式遍历数组.**
```
(function() {
　 var i = 0,
 　len = array.length;
 　do {
 　　if (i == 2) {
 　　　break; // 循环被终止, 此处如果是continue就会造成循环无法退出
 　　};
 　　console.log('array['+ i +']:' + array[i]);
 　　i++;//此句建议放置循环while头部
 　} while(i<len);
})();
```
## 3，forEach
使用forEach循环有几点需要特别注意:</br>
　forEach无法遍历对象</br>
　forEach无法在IE中使用，只是在firefox和chrome中实现了该方法</br>
　forEach无法使用break，continue跳出循环，使用return时，效果和在for循环中使用continue一致</br>
```
demoArr.forEach(function(val, index) {
    if (e == 'CSS3') {
        return;  // 循环被跳过
        // break;   // 报错
        // continue;// 报错
    };
    console.log(val, index);
}
```
很多第三方库会扩展他的方法，使其能够应用在很多地方，比如angular的工具方法中，也有forEach方法，其使用与原生的基本没有差别，只是没有了局限性，可以在IE下使用，也可以遍历对象

## 4，for in
```for(var item in arr|obj){}``` 可以用于遍历数组和对象</br>
　遍历数组时，item表示索引值，索引为字符串，arr表示当前索引值对应的元素 arr[item]</br>
　遍历对象时，item表示key值，arr表示key值对应的value值 obj[item]</br>
```
(function() {
　for(var i in demoArr) {
　　if (i == 2) {
　　　return; // 函数执行被终止
　　　// break;  // 循环被终止
　　　// continue;  // 循环被跳过
　　};
　　console.log('demoArr['+ i +']:' + demoArr[i]);
　}
　console.log('-------------');
})();
```
　上面代码中的 index 变量将会是 "0"、"1"、"3"等这样的字符串，而并不是数值类型。如果你使用字符串的 index 去参与某些运算（"2" + 1 == "21"），运算结果可能会不符合预期。</br>
　不仅数组本身的元素将被遍历到，那些由用户添加的附加（expando）元素也将被遍历到，例如某数组有这样一个属性 myArray.name，那么在某次循环中将会出现 index="name" 的情况。而且，甚至连数组原型链上的属性也可能被遍历到。</br>
　最不可思议的是，在某些情况下，上面代码将会以任意顺序去遍历数组元素。</br>
优点：它可以支持所有对象类型的数据，包括数组，甚至是函数等。而且语法简单，在其他语言中都很少看到。</br>
缺点：不仅遍历普通键，连原型链上的键都去遍历了。</br>
建议：在遍历常用对象时使用，这里的常用对象，就是我们上面举例的键值对（不是Map）。
## 5，for of
在遍历中直接取值。感觉上和for...in形成互补，一个在遍历中取键名，另一个取值。</br>
另一个优点是，它可以遍历任何部署了Iterator接口的数据结构，甚至是非JavaScript的数据类型，即自己定义的数据结构。ES6新增语法。</br>
循环数组：
```
let iterable = [10, 20, 30];
for (let value of iterable) {
 　value += 1;
 　console.log(value);
}
// 11
// 21
// 31
```
循环字符串
```
let iterable = 'boo';
for (let value of iterable) {
　console.log(value);
}
// "b"
// "o"
// "o"
```
除了Map结构外，不能取到键名。不能用来遍历普通对象。</br>
在遍历时想要值的时候，可以使用，但不能用在普通对象上面。Map类型推荐使用。</br>

## 6，递归（尾调用）
ES6中对函数尾调用进行了优化，而如果尾调用结构还是一个递归函数，那么更加节省性能，简直升天。
```
function forEach(object,factory,_keyIndex = 0,_keys = object.keys()) {
　var key = _keys[_keyIndex];
　var value = object[key];
　if(typeof factory === 'function') factory(key,value);
　if (_keyIndex < _keys.length) {
　 　_keyIndex++
　　return this.forEach(object, factory, _keyIndex, _keys)
 　} else {
　　return
 　}
　return forEach(object,factory,_keyIndex,_keys);
}
var obj = {
 　a : 1,
 　b : 2,
 　c : 3
};
forEach(obj,function(key,value){
　console.log(key + ' => ' + value);
});
```
优点：可以不涉及循环，递归的性能高于循环，特别是尾调递归。</br>
缺点：代码结构复杂，千变万化，没有统一的规律可以掌握。</br>
使用场景：当处理比较复杂的数据，可能用循环会消耗掉性能，而又比较在意性能的时候使用。</br>
## 7 map filter every
map即 Array.prototype.map,该方法只支持数组后形似数组的对象 例如 NodeList.</br>
map方法使用其提供函数的每次返回结果生成一个新的数组.</br>
例如 将字符串数组转换为数字数组, 只需一条语句, 如下:
```console.log(['1', '2', '3'].map(Number));//[1,2,3] ```

## 8 第三方each、map方法：理想方法
包括jQuery和underscore都提供了each。</br>
当对象中存在length属性时,`$.each`内部使用for循环去遍历对象, 否则它将使用for in循环去遍历, 因此`$.each`遍历对象遵循如下规律:</br>
　如果对象中存在 length 属性, 遍历深度以length属性为准, 即length多大, 遍历多少个元素.</br>
　如果对象中不存在 length 属性, 遍历深度以实际内部属性个数为准. </br>
不仅如此, `$.each`的具体使用过程中还有以下几点需要注意:</br>
　使用 return 或者 return true 为跳过一个元素，继续执行后面的循环;</br>
　使用 return false 为终止循环的执行, 这是因为在 jquery.each 中,若返回值指定为false, 才跳出循环, 如果感兴趣请翻看 jquery.each 源码;</br>
　无法使用 break 与 continue 来跳过循环.</br>



