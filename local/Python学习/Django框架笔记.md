---
title: Django框架笔记
date: 2020-05-04 15:11:26
categories: 
- 计算机基础
- Python
tags:
- Web框架
- Python
toc: true
---
# Django MTV与MVC的区别
MVC是Model-View-Controller的缩写，其中每个单词都有其不同的含义：
* Modle 代表数据存储层，是对数据表的定义和数据的增删改查；
* View 代表视图层，是系统前端显示部分，它负责显示什么和如何进行显示；
* Controller 代表控制层，负责根据从 View 层输入的指令来检索 Model 层的数据，并在该层编写代码产生结果并输出。
<div align=center><img src="/image/MVC.png" width="400"/></div>
 <center>图1 MVC设计模式</center>

MVC 设计模式的请求与响应过程描述如下：
* 用户通过浏览器向服务器发起request请求，Controller层接受请求后，同时向Model层和View发送指令；
* Mole 层根据指令与数据库交互并选择相应业务数据，然后将数据发送给 Controller 层；
* View 层接收到 Controller 的指令后，加载用户请求的页面，并将此页面发送给 Controller 层；
* Controller 层接收到 Model 层和 View 层的数据后，将它们组织成响应格式发送给浏览器，浏览器通过解析后把页面展示出来。

MVC的3层之间紧密相连，但又相互独立，每一层的修改都不会影响其它层，每一层都提供了各自独立的接口供其它层调用，MVC的设计模式降低了代码之间的耦合性（即关联性），增加了模块的可重用性，这就是MVC的设计模式。

Django借鉴了经典的MVC模式，它也将交互的过程分为了3个层次，也就是MTV设计模式；
* Model：数据存储层，处理所有数据相关的业务，和数据库进行交互，并提供数据的增删改查；
* Template：模板层（也叫表现层）具体来处理页面的显示；
* View：业务逻辑层，处理具体的业务逻辑，它的作用是连通Model 层和 Template 。
<div align=center><img src="/image/MTV.png" width="400"/></div>
<center>图2 MTV设计模式</center>

对MTV设计模式的请求与响应过程进行描述：
* 用户通过浏览器对服务器发起 request 请求，服务器接收请求后，通过 View 的业务逻辑层进行分析，同时向 Model 层和 Template 层发送指令；
* Mole层与数据库进行交互，将数据返回给 View 层；
* Template层接收到指令后，调用相应的模板，并返回给 View 层；
* View层接收到模板与数据后，首先对模板进行渲染（即将相应的数据赋值给模板），然后组织成响应格式返回给浏览器，浏览器进行解析后并最终呈现给用户。

通过以上两种设计模式的比较， 我们可以得出MTV是MVC的一种细化，将原来MVC中的 V层拿出来进行分离，视图的显示与如何显示交给Template层，而View层更专注于实现业务逻辑。
# ORM模块
ORM（Object Realtional Mapping）即对象关系映射，它是一种基于关系型数据库的程序技术。ORM允许你使用类和对象对数据库进行操作，这大大提高了对数据库的控制，避免了直接使用SQL语句对数据库进行操作。如图3是ORM与数据库的映射关系，ORM 把类映射成数据库中的表，把类的一个实例对象映射成数据库中的数据行，把类的属性映射成表中的字段，通过对象的操作对应到数据库表的操作，实现了对象到 SQL、SQL 到对象转换过程。
<div align=center><img src="/image/ORM与DB.png" width="400"/></div>
<center>图3 ORM与DB映射关系</center>

针对数据库中的字段类型，对应的"xxxField"表述如下表：
<table>
    <tr>
        <td>字段</td>
        <td>说明</td>
        <td>字段属性</td>
    </tr>
    <tr>
        <td>AutoFiled</td>
        <td>默然自增主键（Primary_key=Ture），Django 默认建立id字段为主键。</td>
        <td></td>
    </tr>
    <tr>
        <td>CharFiled</td>
        <td>字符类型</td>
        <td>Max_length=32，字符长度需要明确</td>
    </tr>
    <tr>
        <td>InterFiled</td>
        <td>整型 int</td>
        <td> </td>
    </tr>
    <tr>
        <td>DateFiled</td>
        <td>年月日时间类型</td>
        <td>auto_now=True，数据被更新就会更新时间 ；auto_now_add=True，数据第一次参数时产生。</td>
    </tr>
    <tr>
    <td>DateTimeFiled</td>
        <td>年月日小时分钟秒时间类型</td>
        <td>auto_now=True，数据被更新就会更新时间； auto_now_add=True，数据第一次参数时产生。</td>
    </tr>
    <tr>
        <td>DecimalFiled</td>
        <td>混合精度的小数类型</td>
        <td>max_digits=3，限定数字的最大位数(包含小数位)；decimal_places=2，限制小数的最大位数。</td>
    </tr>
    <td>BooleanFiled</td>
        <td>布尔字段，对应数据库 tinyint 类型数据长度只有1位。</td>
        <td>值为True或False</td>
    </tr>
    <tr>
        <td>TextFiled</td>
        <td>用于大文件</td>
        <td></td>
    </tr>
</table>

## Filed的通用字段选项
Model 中添加的字段都是 Field 类型的实例，不同的 Field 类型可能会支持不同的字段选项，但是也有很多字段选项是通用的，即可以用在任何一种 Field 类型中。
* blank：默认值是False，设置为True时，字段可以为空。设置为 False时，字段是必须填写的。如果是字符型字段CharField和TextField，它们是用空字符串来存储空值的
* unique：默认值是 False，它是一个数据库级别的选项，规定该字段在表中必须是唯一的。
>数据库层面对待对待唯一性约束会创建唯一性索引，所以，如果一个字段设置了 unique=True，就不需要对这个字段加上索引选项了。
* null
默认为False，如果此选项为 False建议加入default选项来设置默认值。如果设置为True，表示该列值允许为空。日期型、时间型以及数字型字段不接受空字符串。所以当设置IntegerField，DateTimeField型字段可以为空时，需要将blank与null均设为True。
>对于 CharFiled 和 TextFiled 这样的字符串类型，null 字段应该设置为 False，如果为 Ture，对于空数据就会有两种概念。
* db_index：默认值是 False，如果设置为 True，Django 则会为该字段创建数据库索引，如果该字段经常作为查询的条件，那么就需要设置 db_index 选项，从而加快数据的检索速度。
* db_column：这个选项用于设置数据库表字段的名称。如果没有指定，Django 默认使用 Model 中字段的名字。
* default：用于给字段设置默认值，该选项可以设置为一个值或者是可以调用对象，但不能是可变对象，不同字段类型默认值也不同，比如 BooleanFiled 布尔类型 default 值为Ture 或者 False。主要的使用场景是当一个字段的值被用户省略时，后台服务器自动为该字段的设置默认值。
* primary_key：默认值是 False，如果设置为 True，表示该字段为主键，在 Django 中 默认 id 为主键，也就是说即使你的数据表中没有创建 id 字段，Django 也会自动为你创建 id 字段并将其设置为主键。如果你在表中设置了其他字段为主键的时，那么 Django 将取消为 id 字段设置主键。
* choices：这个选项用于给字段设置可以选择的值。它是一个可迭代对象，即列表或者元组，其中每一个元素都是一个二元组（a，b）的形式，a是用来选择的对象，b是对a的描述信息。
* verbose_name：设置此字段在 admin后台管理系统界面上的显示名称，如果没有设置这个字段，Django将会直接展示字段名并且将字段中的下划线转变为空格。

# 视图函数
 视图函数是一个 Python 函数或者类，开发者主要通过编写视图函数来实现业务逻辑。视图函数首先接受来自浏览器或者客户端的请求，并最终返回响应，视图函数返回的响应可以是HTML文件，也可以是HTTP协议中的303重定向，如下：
 ```python
from django.http import HttpResponse
def Hello_my_django(request):
    return HttpResponse('<html><body>Hello my Django</body></html>')
 ```

**1.HttpResponse视图响应类型**

django.http 模块中导入 HttpResponse，从它简单的名字我们可以得知，它是一种视图的响应类型。

**2.视图函数参数request**

视图函数至少有一个参数，第一个参数必须是 request，request是HttpRequest请求类型的对象，它携带了浏览器的请求信息，所以视图函数的第一个参数必须为request。

**3.return视图响应**

视图函数要返回响应内容，这里的响应内容是我们用HTML标签编写的，把它作为HttpResponse的对象返回给浏览器。

# 模板系统及应用
“模板”称之为Template，它的存在使得HTML和 View视图层实现了解耦，在 templates 文件中新建一个 HTML 文件，并且将此文件命名为 hello.html，然后在此文件中书写我们的 HTML 代码，如下所示：

写HTM代码：
```html
<html><body>{{vaule}}</body></html>
```
写视图函数：
```python
from django.shortcuts import render      
def hello_my_django(request):
    return render(request,"hello.html",{"vaule":"hello my Django"})
```
**1.模板传参**

hello.html 文件中的 {{vaule}} 是一个模板的变量，视图函数必须把数据形成字典的形式才可以传递给模板，这就是“模板传参”。

**2.render方法**

render 是 View 层加载模板的一种方式，它封装在 django.shortcuts 模块中，render 方法使用起来非常方便，它首先加载模板，然后将加载完成的模板响应给浏览器。

# 路由系统
Django 中利用 ROOT_URLCONF 构建了 URL 与视图函数的映射关系。在 django.conf.urls 中封装了路由模块，新建的Django项目中提供了urls.py路由配置文件，urls.py文件中定义了一个 urlpatterns的列表，它是由url( )实例对象组成的列表，Django中url的定义就是在这个列表完成的。后台Admin管理系统的路由就定义在了列表第一个位置，下面我们对路由的语法格进行简单说明：
>url(regex,view,name=None)

参数解析如下：
* regex，匹配请求路径，用正则表达式表示；
* view，指定 regex 匹配路径所对应的视图函数的名称；
* name，是给 url 地址起个别名，在模板反向解析的时候使用

## 配置URL实现页面访问
在 urls.py 的同级目录下，新建 views.py 文件，把它作为编写视图函数的 View 层，然后在 views.py 中编写如下代码：
```python
from django.http import HttpResponse
def page_view(request):
    html='<h1>欢迎来到，C语言中文网，网址是http://c.biancheng.net</h>'
    return HttpResponse(html)
```
目的是把 URL 与视图层进行绑定，然后在urls.py的 urlpatterns 中编写如下代码：
```python
from django.conf.urls import url
from django.contrib import admin
from myject import views
urlpatterns = [
    url(r'admin/', admin.site.urls),
    url(r'^page$/',views.page_view),]
```
