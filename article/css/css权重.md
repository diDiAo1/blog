# css权重
css

---

# 1、css的特殊性
选择器的特殊性分为4个等级，a b c d，从左到右，越左边的越优先, 如果一个选择器规则有多个相同类型选择器，则+1。
 1. 如果是HTML内样式，那么特殊性最优先，a=1
 2. id选择器的特殊性是b，
 3. 类选择器、伪类选择器、属性选择器为c
 4. 标签选择器、伪元素选择器为d
 
# 2、选择器类型
## 1、id选择器
`#myid { ... }`
## 2、类选择器
`.myclass { ... }`
## 3、标签选择器
`p{ ... }  h1{...}  a{...}`
## 4、属性选择器
 5. 简单属性选择器
`h1[class] {color:silver}`  选择有class属性(值不限)所有的h1元素，文本为银色
`a[href][title]` 同时有href和title属性的超链接
 6. 具体属性值选择
 `a[title='超链接']` 选择title为超链接的a标签
 7. 部分属性值选择
 `span[class~="barren"]` 选中class属性中由barren的span元素，等同`span.barren`
`img[title~="title1"]` 选中title文本中包含title1的所有图片
`[foo^="bar"]`选中foo属性值以bar开头的所有元素
`[foo$="bar"]`选中foo属性值以bar结尾的所有元素
`[foo*="bar"]`选中foo属性值中包含子串bar的所有元素
 8. 特定属性选择类型(常见用途匹配语言值)
`*[lang|="en"]`选择lang属性等于en或者以en-开头的所有元素
#### 后代选择器 `h1 span {}` 作为h1元素后代的任何span元素
#### 选择子元素 `h1 > strong {}` 只作为h1元素子元素(不是后代元素)的strong元素
#### 相邻兄弟元素 `h1 + p {}` 选择紧接着h1元素后出现的所有p元素，h1和p有共同的父元素
## 5、伪类选择器
 1. 链接伪类
 `:link :visited`
 2. 动态伪类
 `:focus :hover :active`
 3. 选中第一个元素
`：first-child`
## 6、伪元素选择器
`::after ::befor :first-letter :first-line`
# 3、css特殊性图片说明
![css-specificityimg](http://www.standardista.com/wp-content/uploads/2012/01/specificityimg.png)

# 4、权重计算测试
1. *{}                         ====》0
2. li{}                        ====》1(一个元素)
3. li:first-line{}             ====》2(一个元素，一个伪元素)
4. ul li {}                    ====》2（两个元素）
5. ul ol+li{}                  ====》3（三个元素）
6. h1+ *[rel=up] {}            ====》11（一个属性选择器，一个元素）
7. ul ol li.red{}              ====》13（一个类，三个元素）
8. li.red.level{}              ====》21（两个类，一个元素）
9. style=""                    ====》1000(一个行内样式)
10. p {}                        ====》1（一个元素）
11. div p {}                    ====》2（两个元素）
12. .sith {}                    ====》10（一个类）
13. div p.sith{}                ====》12（一个类，两个元素）
14. #sith{}                     ====》100（一个ID选择器）
15. body #darkside .sith p {}   ====》112(1+100+10+1,一个Id选择器，一个类，两个元素)
