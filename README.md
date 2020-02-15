> XPath是什么？
> * XPath 的全称为 XML Path Language
> * XPath使用路径形式的格式来标识XML格式文档中的节点
> * XPath是XSLT标准中主要的一部分
> * XPath 是W3C推荐的一个标准

> 通俗的说: XPath就是一种在HTML中寻找**节点**的语法.   

例如一个简单的例子：
```html
<div class="cooking">
  <span lang="en">Everyday Italian</span>
</div>
```
获取其中的span元素，可以通过Xpath **/div/span** 来完成

> XPath在爬虫框架CasperJS和Selenium中大量的使用，但在Google出的最新爬虫框架Puppeteer中使用的是JQuery的Selector, 后续会单独写一篇文章介绍。

###### XPath 不仅仅是这么简单，它有200多种内置的路径表达方式，下面我们来看几个主要的概念

### 1.节点 (Nodes)

每个节点就是一个独立完整的Tag标签或者是独立的值， 例如：
``` html
  <author>J K. Rowling</author> (节点 - 独立的标签)
  J K. Rowling   （节点 - 独立的值）
```

节点有以下属性：

* Parent (父节点,  0到1个) 
  比如在上面简单的例子中，Span节点的父节点是div节点.

* Children (子节点, 0到多个)
  比如在上面简单的例子中，div节点的子点是span节点.

* Siblings (邻居节点，0到多个)
  比如在以下的html中，input的邻居节点有span节点和table节点
```html
  <div>
      <span>input your name</span>
      <input>  </input>
      <table>  </table>
  </div>
```
* Ancestors (祖先节点, 0到多个)
  比如在以下的html中，span节点的祖先节点有 div节点和body节点
```html
  <body>
      <div>
          <span>
              text
          </span>
      </div>
  </body>
```
* Descendants（后辈节点, 0到多个）
比如在上一个html中， body节点的后辈节点有div节点和span节点.


### 2. 语法 (Syntax)
比较常用的语法就7种，熟练使用这些基本上就足够用来寻找大部分的节点了。

* 节点名字 - 选择所有该名字的节点
比如 div 就表示选择所有div节点.  

* /  - 根节点，表示目标文档中的最顶部节点。
例如 /div, 就表示选择在目标文档顶部节点下的所有div节点。 注意只是在root节点下，跨层的孙辈节点就不包含在内了， 例如 /body/div 就不属于/div的选择了

* // 当前节点下寻找，这里的寻找是不限制层级的，即当前节点下的所有后辈节点都会被搜索。

例如使用 Selenium 可以有以下的写法：
```python
inputDiv = driver.find_element_by_xpath("//div[@class='login']") 
inputElementUsername = inputDiv.find_element_by_xpath("//input[@name='email']")
``` 
其中第一句是需拿在文档中寻找登陆div (这里driver指代当前页面), 第二句就是在找到的登陆div中 寻找email的输入框。 这里输入框并不一定在登陆div的第一层下。

* .  -  当前节点
  ..  - 父节点
  这两个语法的含义和命令行中的.与..是一致的， . 代表当前节点 ..代表父节点 ：
```
../button   在当前节点的父节点中寻找button节点
./button  在当前节点中寻找button节点
```

* @ 属性选择， 在上面的例子中有出现过，当选择节点的时候，不仅仅可以通过节点名字来区别，还可以额外增加节点的属性。 例如下面的html
```html
  <body>
        <div class="a"> a </div>
        <div class="b"> b </div>
  </body>
```
当要获取body下的a div时， 可以通过指定class属性来寻找节点
```
    //div[@class="a"]
```

* 声明 (Predicates) 
利用之前的几个语法已经可以完成节点定位的， 而这里的声明 (Predicates) 提供了一些高效率的节点定位函数：

```
/bookstore/book[1]  -  选择第一个符合条件的book节点
/bookstore/book[last()]  - 选择最后一个符合条件的book节点
/bookstore/book[last()-1] -  选择倒数第二个符合条件的book节点
/bookstore/book[position()<3] - 选择前二个符合条件的book节点，位置小于3，即1，2
/bookstore/book[price>35.00] - 选择价格属性大于35的book节点， 这个price属于属性，可以根据需要调整
```

* 通配符 
这个只需要了解 * 代表任意节点， 其他的没什么用

* 多重路径选择
通过  | 可以选择若干个路径， 例如：
```
//book/title | //book/price - 选择所有在book节点下的title和price节点
```


### 3. 例子

这里针对一些复杂情况做说明并举例，如有需要请留言

* 如何定位有多个class属性的元素？

方法1 可以通过空格间隔写入多个class
```js
  "//button[@class='btn btn-netease btn-sm js-btn-ok']"
```
方法2 可以使用 and 连接多个class属性
```
"//a[contains(@class,'btn') and contains(@class,'page_next')]"
```

