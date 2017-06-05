# 一、angular介绍
1、angular是Google公司提供的一套基于MVC结构的js开发工具，其核心功能就是对现有html编码以指令方式进行扩展，并使其通过使用元素声明的方式来构建动态内容。
2、为实现上述目的，angular利用 了两项技术点：双向绑定和依赖注入

# 二、适用范围
angular是构建一个MVC类结构的js库，建议在构建CRUD应用时使用它。对于图形编辑、游戏开发不建议使用。

# 三、控制器
1、不要在控制器中写业务逻辑，业务逻辑交给模型层的服务。
// 比如这是把业务逻辑放在控制器
```
.controller('ElectronicMapCtrl', function ($scope,pageBase,API) {
 // 获取未处理报警列表的总数
            vm.getAlarmNum = function () {
                var url =  API + "WarningInfoServlet";
                var params = {
                    methodName  : 12
                };
                pageBase.postFunc(url,params).then(
                    function(res) {
                        if (res.data.success) {
                            vm.alarmListNum               = res.data.data.total;
                        }
                    }
                );
            };
            vm.getAlarmNum();
});
```
当你把业务逻辑交给模型层的服务，控制器是这样：
```
.controller('ElectronicMapCtrl', function (ElecMap) {
  ElecMap.getAlarmNum().then(function(res){
    vm.alarmListNum               = res.total;
})
});
```
然后ElecMap是你自定义的service或factory，里面的代码就像这样：
```
angular.module('myApp')
    .factory('ElecMap', ['API','pageBase','$q',function(API,pageBase,$q) {
        return new elecMapService(API,pageBase,$q);
    }])

function elecMapService(API,pageBase,q) {
    var deferred = q.defer();
    this.getAlarmNum= function () {
        var url = API + "WarningInfoServlet";
        var params = {
            methodName: 12
        };
        pageBase.postFunc(url, params).then(
            function (res) {
                if (res.data.success) {
                    deferred.resolve(res.data.data);
                }
            })
        return deferred.promise;
    }
}
```
为什么控制器不应该包含业务逻辑和应用状态？
①控制器会在每个视图中被实例化，在视图被销毁时也要同时销毁
②控制器是不可重用的——它与视图有耦合
③Controllers are not meant to be injected

# 四 模板
1、避免在模板中使用复杂的表达式。
2、当需要动态设置 的 `src` 时使用` ng-src `而非 `src `中嵌套 {{}} 的模板。`href`同理。
3、通过 `ng-style` 指令配合对象式参数和` scope `变量来动态设置元素样式，而不是将 `scope `变量作为字符串通过 {{ }} 用于 `style` 属性。
```
<script>
...
$scope.divStyle = {
  width: 200,
  position: 'relative'
};
...
</script>
<div ng-style="divStyle">hello world</div>;
```
4、添加元素样式
```
$scope.a=true;
$scope.b=false;
<div ng-class="{'red':a,'blue':b}"></div>
$scope.blnfocus=true;
<div ng-class="{true:'red',false:'blue'}[blnfocus]"></div>
```
列表中还有`ng-class-odd` 和 `ng-class-even`
简单例子：
```
<li 	ng-repeat="stu in data"
	ng-click="li_click($index)"
	ng-class-odd="odd"
	ng-class-even="even"
	ng-class="{focus:$index===focus}"
>
	<span>{{stu.name}}</span>
	<span>{{$first?'是':'否'}}</span>
	<span>{{$last?'是':'否'}}</span>
</li>
$scope.li_click = function (i) {
	$scope.focus = i;
}
```
css中需要加入`.odd .even .focus`
5、元素显示隐藏`ng-show ng-hide ng-switch(ng-switch-when ng-switch-default)`
# 五 过滤器
1、orderby
```
<li  ng-repeat="stu in data|orderBy:'-score'|limitTo:3">
```
按分数降序排列，只显示3条
2、匹配方式过滤`filter`
{{数据|`filter`:匹配项}}
匹配项可以是字符串、对象和函数
```
<li  ng-repeat="stu in data|filter:findscore">
$scope.findscore = function (ｅ)｛
　　return e.score>85 && e.score<90;
｝
```
3、在需要格式化数据时将格式化逻辑封装成 过滤器 并将其声明为依赖
自定义过滤器
```
<li  ng-repeat="stu in data|young:0">
module.filter('young',function(){
    return function (e,type) {
        var _out = [];
        var _sex = type ? "男":"女";
        for (var i = 0;i<e.length;i++) {
            if (e[i].age > 22 && e[i].age<28 && e[i].sex == _sex)
	_out.push(e[i]); 	
        }
        return _out;
    }
})
```
在`controller`中注入自定义的`filter`
```
function MyCtrl($scope, young){
}
module.controller('MyCtrl', MyCtrl);
```
4、重要应用
①表头排序
```
<li >
	<span ng-click="title='name';desc=!=desc">姓名</span>
	<span ng-click="title='score';desc=!=desc">分数</span>
</li>
<li 	ng-repeat="stu in data">
	<span>{{stu.name}}</span>
	<span>{{stu.score}}</span>
</li>
$score.title = 'name'
$score.desc = 0
```
②字符查找
```
<input  type="text" ng-model="key">
<li 	ng-repeat="stu in data | filter:{name:key}">
	<span>{{stu.name}}</span>
	<span>{{stu.score}}</span>
</li>
```
# 六 作用域
作用域是控制器与视图的桥梁，也是指令与视图的桥梁
1、提供`$watch`方面监听数据模型变化(`ng-model`双向绑定)
```
<input  type="text" ng-model="key">
$scope.$watch('key',function(){
	//...
})
```
2、子级可继承父级作用域全部属性和方法
同级不可以相互访问属性和方法
3、两种方式实现作用域通信
①在作用域间创建一个单例的服务
②事件:只能父子相传，同级接受不到
```
$broadcasted(eventname,data) 父到子
$emitted(eventname,data) 子到父
$on(eventname,function(event,data)){
//
}
```
在作用域监听传播来的事件并获取相应的数据
# 七  依赖注入
1、当在代码中声明了依赖关系后，Angular通过`injector`注入器将所依赖的对象进行"注入"操作
2、`config`函数为定义的模板对象注入依赖的各种服务，除了用于注册控制器的`controllerProvider`服务外，还有`provide`服务，它包含了几个重要方法`provider,factory,service,value`
3、依赖注入标记
①标记式注入
调用`$injector`属性来完成，字符型数组，一定要按注入方式的顺序来
```
var MyCtrl = function($scope,$window,$show);
MyCtrl.$inject=[$scope,$window,$show]
```
②行内式注入
将一个字符型数组作为对象参数，最后一个是函数体，其余是注入对象中的服务名，它们顺序与函数体参数一一对应
```
module.controller('MyCtrl',[$scope,$window,function($scope,$window){
    // ...
}])
```
4、依赖注入应用场景
①构建控制器:上述标记式注入
②工厂方法：类似`'config','factory','directive','filter'`等构造性质的方法
# 八 angular服务
1、内置服务：`$scope,$http,$window,$location`
2、创建angular服务，除了调用`$provide`服务，还可以直接调用模块中的`factory、service、constant`和`value`等方法来创建。
3、把业务逻辑封装到服务中，把业务逻辑抽象为服务作为你的` model`。例如：
在 三、控制器 提到的内容
如果需要例子展现如何在控制器中使用服务，请参考 'Avoid writing business logic inside controllers'。

# 九 使用promise而非回调
1、处理异步编程的模式，可以有效解决回调的繁琐，以一种同步的方式去处理业务逻辑。
2、想要在angular中创建`promise`对象，必须在模板中注入`$q`服务，并先调用`defer`方法创建一个延期对象。
```
var defer = $q.defer()
```
defer是一个延期对象，它包含3个方法notify、resolve、reject和promise对象
promise对象有一个then方法
```
promise.then(successCallback,errorCallback,notifyCallback)
```
3、实例
```
module.factory("async",function($q,$http){
    var defer = $q.defer();
    $http.get('data/asycn')
    .success(function(data){
        defer.resolve(data);
    })
    .error(function(reason){
        defer.reject(reason) ; 
   })
    return defer.promise;
})
.controller("MyCtrl",function($scope,async){
    var promise = async;
    promise.then(function(resp){
        $scope.result = "成功"+resp;
    },function(n){
        $scope.result = "失败"+n;
   })
})
```
# 十 注意事项
1、调用`element`方法控制`dom`元素
```
angular.element(document.getElementById("control")).append(newhtml)
```
2、`setTimeout`无效，调用`$timeout`服务
在angular中，大部分操作之后的效果都是由`$apply`方法自动在页面完成，若调用非angular中方法，系统不会调用`$apply`方法在页面中同步操作结果
3、{{}}闪烁问题
使用`ng-bind ng-cloak`，适用`span div option...`
`ng-bind` 没办法绑定输入框的
```
<div ng-cloak>{{message}}</div>
```
4、使用`track by`排序`ng-repeat`中的数据
5、释放多余的`$watch`检测函数，再次调用`$watch`就可释放它的检测功能
```
$scope.stopWatch = function() {
    contentWatch();
}
var contentWatch = $scope.$watch('content',function(newVal,oldVal){
    if(newVal===oldVal){return;}
   $scope.count++;
})
```
6、`ng-if`中`ng-model`值无效
```
<div ng-if="!a">
    <input type="checkbox" ng-model="$parent.b">
</div>
```
7、`angularjs`有时报错莫名其妙且提示不全，耐心检查代码

参考：
angularjs实战
