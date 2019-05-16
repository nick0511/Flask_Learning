# flask 1.0学习笔记





## 文件的认识



新建一个flask的项目的时候。文件结构是这样的。

```python
-static #存放静态文件css、js等
-template#存放html模板
-venv#虚拟环境
-app.py#主文件
```
```python
from flask import Flask

app = Flask(__name__)

#装饰器，功能是做url和视图函数的映射
@app.route('/')
def hello_world():
    return 'Hello World!'

#主函数
if __name__ == '__main__':
    #启用一个应用服务器
    # while true
    #   listen()   
     app.run()

```
----





## debug模式

#### debug模式的好处

- 出现错误的时候容易调试，它会出现在页面上
- 修改页面之后会自动重新执行，免去手动执行
在以前的版本debug模式开启就是一句话
```python
DEBUG=True

```
flask 1版本之后debug要在config里面设置了
勾选一个勾就可以

----



##  Flask默认Port与Host

flask的默认启动端口是5000,默认的Host是127.0.0.1

如果需要发布到服务器上要改为0.0.0.0

如果我们需要改变，就直接改我们的run函数就OK~



如下

```python
if __name__ == '__main__':
    app.run(
        host='127.0.0.1',
        port=8000,
        debug=True
    )
```

----



## 添加视图函数

添加视图函数很简单。就是一个函数，加上一个装饰器就ok。具体是这样

```python
@app.route('/test/')#url映射
def hello(name):
    return "视图函数例子"
```
----



## 反转URL



#### 定义：

​		反转url是从视图函数转成url

#### 作用：

 1、在重定向的时候用到
 2、在模板中也可能用到

```python
利用到url_for这个函数
需要加上：
from flask import url_for		
```
- - - - -



## URL中获取参数

#### 如何从url中获取参数呢？

加上`url后面加上<converter:variable_name>` 

```python
@app.route('/test/<string:name>')#意思是获取的类型是字符类型
def hello(name):
    return "你的字符是：{}".format(name)
```

比如输入一个127.0.0.1:5000/test/123123
输出的是

![1557306948252](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1557306948252.png)

#### 官方说明

官方上称为转换器

常见的转换器类型有

| string |    接受任何不包含斜杠的文本    |
| :----: | :----------------------------: |
|  int   |           接受正整数           |
| float  |          接受正浮点数          |
|  path  | 类似 `string` ，但可以包含斜杠 |
|  uuid  |        接受 UUID 字符串        |

官方例子

```python
@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return 'User %s' % username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return 'Post %d' % post_id

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # show the subpath after /path/
    return 'Subpath %s' % subpath
```

----



## html页面相关操作



#### 调转到某个html页面

我们总不能只输出的是一段文字。身为一个web框架，肯定要有web的出现。

```python
@app.route('/tem')
def tem():
    return render_template('base1_1.html')
```

例子中是跳转到base1_1.html页面中

条件：

​	1、创建有html文件

​	2、有相关的视图函数

​	3、从Flask导入render_template并使用render_template('filename.html')



#### 重定向

由一个页面重定向到另一个页面。是做网页很经常遇到的，比如说是查看个人信息这个例子。当你没有登陆，那肯定是让你先重定向到登陆页面



例子：

```python
@app.route('/<string:islogin>')
def hello_world(islogin):
    if islogin == '1': #模拟登陆成功
        return 'Hello World!'
    else:
        login_url = url_for('login')#利用反转url来获取url
        return  redirect('/login')#重定向到指定的地址

@app.route('/login')
def login():
    return 'login'
```



​	条件：

​		1、导入redirect的包

​		2、正确的url路径



## 模板



<u>*在 Python 内部生成 HTML 不好玩，且相当笨拙。因为你必须自己负责 HTML 转义， 以确保应用的安全。因此， Flask 自动为你配置 [Jinja2](http://jinja.pocoo.org/) 模板引擎。</u>*

##### 官方说明：

在 Flask 中， Jinja2 默认配置如下：

- 当使用 `render_template()` 时，扩展名为 `.html` 、 `.htm` 、 `.xml` 和 `.xhtml` 的模板中开启自动转义。
- 当使用 `render_template_string()` 时，字符串开启 自动转义。
- 在模板中可以使用 `{% autoescape %}` 来手动设置是否转义。
- Flask 在 Jinja2 环境中加入一些全局函数和辅助对象，以增强模板的功能。

----

##### 参数传递

----

###### 普通参数

通过后端的参数传递到模板上。

一般是使用{{ }}来获取参数

例子：

比如后端的视图函数如下

```python
@app.route('/login')
def login():
    name = '123'
    return render_template('index.html',name=name)
```

那么我的index页面要是这样的

```html

<p>{{ name }}</p>

```



----

###### 列表类型参数

列表类型的参数只能用name=value形式传递参数

视图函数如下：

```python
@app.route('/login')
def login():
    name = ['nick','mike']
    return render_template('index.html',name=name)
```

index.html如下：

```html
<p>{{ name[0] }}</p>
```

访问第0个元素，输出nick

----

###### 字典类型参数

----

同样，后端的代码传递的函数也是name = value或者写成**字典名

```python
def index(username):
    #模板要放在templates
    #flask要导入render_template
    #
    #参数多可以写成字典
    content = {
        'username':username,
        'age':16,
        'website':{
            'baidu':'www.baidu.com',
            'google':'www.google.com'
        }
    }
    #用两个*来传字典，把字典转化成关键字参数
    return render_template('index.html',**content)
    
    #也可以直接传
    #return render_template('index.html',username=username)
```

模板中访问字典的两种方法如下：

```html

{#字典中访问字典元素两种方法#}

{#-------1--------#}
<p>website:{{ website['baidu'] }}</p>
<p>website:{{ website['google'] }}</p>

{#-------2--------#}
<p>website:{{ website.google }}</p>

```

##### 模板的判断语句

Flask 的模板判断语句和Django没什么区别，都是if endif 的形式

```html
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```



##### 模板的循环语句

延续上面列表参数传递的例子讲

for可以用来遍历我们的传递的列表、字典

```html
{% for item in name %}
    <p>{{ item }}</p>
{% endfor %}
```

注意的是，item想输出一定要带{{}}，因为它也是一个变量。



##### 模板的继承



很多模板语言都有继承的功能，我觉得最大的作用就是实现代码的复用。这应该是模板语言最核心的部分了。



语法：

母版的定义：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
    <body>
    {% block body %}{% endblock %}
    </body>
</html>

```

子板继承的时候：

用到的关键字是extend

```html
{% extend 'muban.html' %}
{% block body%}
	巴拉巴拉巴拉
{% endblock %}
```



注意：我们只可以在block里面些写东西,并不能在外面些东西，而我们子板也可以做别人的母版，可以支持多次继承，只需在子板里面在加一个自己定义的block，继承它的子板就可以重写这个block。

```
例子：
{% extend 'muban.html' %}
{% block body %}
	{% block left %}{% endblock %}
	{% block right %}{% endblock %}
{% endblock %}
```

​																																																																																																																																																																																																																																																																																															

----

##### 过滤器



过滤器有很多作用，比如：

length过滤器可以用于统计网站的评论数

default参数可以用于设置html某参数，后台没传值的时候做一个默认参数选项



###### Default过滤器：

作用：

​	设定变量的默认值。当后端不传参数的时候，前端默认加载默认值。而是不报错。

语法：

```
{{ avatar|default('xxx') }}
```



###### length过滤器：



作用：

​	获取列表、字符串、字典、元组等长度。例如用来显示文字评论的总数

语法：

```
	{{ avatar | length }}	
```

例子：

前端：

```html
 <body>
    <p>评论数：（{{ comments|length }}）</p>
    <ul>
        {% for comment in comments %}
            <li>
                <a href="#">{{ comment.user }}</a>
                <p href="#">{{ comment.content }}</p>
            </li>
        {% endfor %}
    </ul>
</body>
```

后端：

```python
@app.route('/')
def index():
    # 定义一个评论列表
    comments = [
        {
            'user' : '站长',
            'content' : '我觉得可以'
        },
        {
            'user' : '你猜',
            'content' : '我觉得不行'
        },
        {
            'user' : '杰克',
            'content' : '你有Freestyle吗？'
        }
    ]
    return render_template('index.html',comments=comments)
```

输出的是3



###### 官方上的常用过滤器：

- abs(value)：返回一个数值的绝对值。示例：-1|abs
- default(value,default_value,boolean=false)：如果当前变量没有值，则会使用参数中的值来代替。示例：name|default(‘xiaotuo’)——如果name不存在，则会使用xiaotuo来替代。boolean=False默认是在只有这个变量为undefined的时候才会使用default中的值，如果想使用python的形式判断是否为false，则可以传递boolean=true。也可以使用or来替换。
- escape(value)或e：转义字符，会将<、>等符号转义成HTML中的符号。显例：`content|escape`或`content|e`。
- first(value)：返回一个序列的第一个元素。示例：names|first
- format(value,*arags,**kwargs)：格式化字符串。比如：{{ "%s" - "%s"|format('Hello?',"Foo!") }}将输出：Helloo? - Foo!
- last(value)：返回一个序列的最后一个元素。示例：names|last。
- length(value)：返回一个序列或者字典的长度。示例：names|length。
- join(value,d=u”)：将一个序列用d这个参数的值拼接成字符串。
- safe(value)：如果开启了全局转义，那么safe过滤器会将变量关掉转义。示例：content_html|safe。
- int(value)：将值转换为int类型。
- float(value)：将值转换为float类型。
- lower(value)：将字符串转换为小写。
- upper(value)：将字符串转换为小写。
- replace(value,old,new)： 替换将old替换为new的字符串。
- truncate(value,length=255,killwords=False)：截取length长度的字符串。
- striptags(value)：删除字符串中所有的HTML标签，如果出现多个空格，将替换成一个空格。
- trim：截取字符串前面和后面的空白字符。
- string(value)：将变量转换成字符串。
- wordcount(s)：计算一个长字符串中单词的个数。



## 数据库

##### ORM 与 Flask sqlalchemy

Flask-sqlalchemy是一款ORM框架

ORM：object relationship mapping 模型关系映射

ORM的好处：可以让我们操作数据库和操作对象一样，非常方便，因为一个表抽象成一个类，一条数据抽象成一个对象。

ORM三大原则：

- 简单性：以最基本的形式建模数据。
- 传达性：数据库结构被任何人都能理解的语言文档化。
- 精确性：基于数据模型创建正确标准化了的结构。



##### Flask sqlalchemy连接数据库

​	需要环境：

​				Mysql的server

​				pymysql插件

​				flask-sqlalchemy包

​				

​	步骤：

​		1、创建sqlalchemy对象

​		2、初始化sqlalchemy连接语句,SQLALCHEMY_DATABASE_URI

​		3、app.config.from_object(config)

​		4、db = SQLAlchemy(app)

​		5、测试 db.create_all()

详细讲解：

1、创建对象

```
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy(app)
```

2、配置config.py配置文件

```python
config文件配置
DEBUG=True

#secure key

#sql
DIALECT = 'mysql'
DRIVER =  'pymysql'
USERNAME = 'root'
PASSWORD = '123123'
HOST = '132.232.110.111'
PORT = '3306'
DATABASE = 'flask_db'

SQLALCHEMY_DATABASE_URI = "{}+{}://{}:{}@{}:{}/{}?charset=utf8".format(DIALECT,DRIVER,USERNAME
                                                                       ,PASSWORD,HOST,PORT,DATABASE)


```

3、补全app.py文件

```flask
import config
app.config.from_object(config)
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
```

4、测试

```
db.create_all()
```



----

##### Sqlalchemy模型映射



1、模型需要继承db.Model，然后需要映射到表中的属性，必须写成‘db.Column’的类型

2、数据类型对应的属性

- 'db.Interger':代表整形
- 'db.String'：代表varchar
- 'db.TEXT'：代表text类型

3、其他参数对应

primary_key ：代表主键

autoincreatement：代表这个键是自增长的

nullable：代表是否允许为空

4、最后还需要调用db.createall()来创建数据表。

```python
#创建数据库
class User(db.Model):
    id  =db.Column(db.Integer,primary_key=True,unique=True,nullable=False,autoincrement=True)
    username = db.Column(db.String(20),nullable=False)
    pwd = db.Column(db.String(20),nullable=False)
    intro = db.Column(db.TEXT,nullable=False)    
db.create_all()	
```

![1557370694520](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1557370694520.png)



我们也可以自定义表名，这里没有定义，orm框架自动把类名转成小写做我们的表名

表名只需要在类里面加一句

```
__tablename__='user'
```



##### 关于表的操作

###### 	新增表

​		步骤：

​			1、新建类

​			2、db.create_all()

###### 	删除表

​		步骤：

​			1、db.drop_all()

##### 插入数据



首先要创建一个数据表类

然后创建一个类的对象，传递需要插入的参数

然后执行db.session.add（）把对象加里面

然后执行db.session.commit()提交操作



例子：

已有的数据表类

```
class User(db.Model):
    id = db.Column(db.Integer,primary_key=True,unique=True,nullable=False,autoincrement=True)
    username = db.Column(db.String(20),nullable=False)
    pwd = db.Column(db.String(20),nullable=False)
    intro = db.Column(db.TEXT,nullable=False)
```

插入操作：

```
user1 = User(username='mike',pwd='123456',intro='123123123sadfasdf')
db.session.add(user1)
db.session.commit()
```

##### 修改数据



思路：

1、首先查找出该条数据做一个对象。

2、修改该对象的对应属性

3、commit上去提交事务



下例是修改 名字为mike的记录 名字为macro

```python
id_2 = User.query.filter_by(username='mike').first()
id_2.username= 'macro'
db.session.commit()
```



##### 查询数据

SQLalchemy利用query对象来提供查询。我们可以使用过滤器来获得更加精确的信息。



例子：

环境：User表（字段：id，username，pwd，intro）

查询所有数据

使用的是User.query.all()

获得username的话，可以这样 

```
mes = User.query.all()#接受结果对象
for item in mes:
	print(item.username)
```



----

###### 查询执行函数all()

​	功能：以列表的形式返回查询的所有结果

###### 查询执行函数first()

​	功能：返回查询第一个结果，如果没有结果就返回none

###### 查询执行函数first_or_404()

​	功能：返回查询的第一个结果，如果没有结果就终止请求，返回404

###### 查询执行函数get()

​	功能：返回指定主键对应的行，如果没有对应的行，则返回none

###### 查询执行函数get_or_404()

​	功能：返回指定主键对应的行，如果没有就终止请求，则返回404

###### 查询执行函数count()

​		功能：返回查询结果的数量

###### 查询执行函数paginate()

​	功能：返回一个paginate对象

----

###### 过滤器 filter_by

​	功能：筛选出符合等值过滤条件的记录

​	假如要查询用户名为username的一条记录要用到filter_by(name=value)这个过滤器

​	



```
mes = User.query.filter_by(username='mike')

print(mes,mes.all())

```

如果没有改记录会输出一个空列表

```
SELECT user.id AS user_id, user.username AS user_username, user.pwd AS user_pwd, user.intro AS user_intro 
FROM user 
WHERE user.username = %(username_1)s []
```

如果有结果则

```python
mes = User.query.filter_by(username='macro')

print(mes,mes.all()[0].id)
```

结果：

```
SELECT user.id AS user_id, user.username AS user_username, user.pwd AS user_pwd, user.intro AS user_intro 
FROM user 
WHERE user.username = %(username_1)s 1
```

----

###### 过滤器 limit

​	语法：

​	表名对象.query.limit(条数).all()



​	功能：限定返回的记录条数

```python
mes = User.query.limit(3).all()
for item in mes:
    print(item.username,item.id)
```

​	

​	结果：

```
macro 1
asdf 2
asdf 3
```

----

###### 过滤器 order_by

语法：

​	表名对象.query.order_by(表名对象.列名.desc（）） 默认是asc正序



功能：根据某个字段进行排序，当然也可以是多个字段。只需要加个， 再加相应字段及排序规则。



例子：

```python
mes = User.query.order_by(User.username.desc()).all()
for item in mes:
    print(item.username,item.id)
```

结果：

```
sdf 4
macro 1
dfeer 5
asdf 2
asdf 3
```

----

###### 过滤器 group_by



​	功能：根据指定的条件进行分组

​	

​	语法： 表名对象.query.group_by(表名对象.字段名)

​		

​	例子：

```python
mes  = User.query.group_by(User.username)
for item in mes.all():
    print(item.username,item.id)
```

​	结果：

```
asdf 2
dfeer 5
macro 1
sdf 4
```



----

###### 过滤器 offset



​	功能：把原来的查询，进行偏移相应的位置，再输出	



​	语法：表名对象.query.offset(偏移量).执行查询函数

​		注意：偏移量必须是正整数



​	例子：

```python
    mes = User.query.offset(1).all()
    for item in mes:
        print(item.username,item.id)
```

​	结果（带有offset）：

```
asdf 2
asdf 3
sdf 4
dfeer 5
```

（不带offset）：

```
macro 1
asdf 2
asdf 3
sdf 4
dfeer 5
```

#### 删除数据

​	貌似只能删除查找到的一条数据，一条条去删

做法：

​	先找出相关的数据对象

​	执行删除语句

​	commit掉

语法：

```
mes = User.query.filter_by(username='asdf').first()
db.session.delete(mes)
db.session.commit()
```



#### 批量插入数据：

不推荐使用ORM操作，ORM效率较慢，推荐使用非ORM





```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from random import randint

class userinfo1(db.Model):
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    username = db.Column(db.String(80))
    
 
DB_CONN = config.SQLALCHEMY_DATABASE_URI
engine = create_engine(DB_CONN,echo=True)
DB_Session = sessionmaker(bind=engine)
session = DB_Session()

session.execute(userinfo1.__table__.insert(),[{'username': str(randint(1,10000))}for i in range(100000)])
session.commit()

```



而且需要优化mysql插入参数

```shell
innodb_buffer_pool_size=800M#内存缓冲区大小，纯mysql环境要设置为服务器内存60%~80.
```

没做优化：耗时11.439026117324829s

设为512M：耗时10.588851928710938s

设为800M：耗时8.975513935089111

```shell
innodb_thread_concurrency=4 #设置OS可以进入innodb内核的线程数，推荐2*（CPU核心数+磁盘数）
```

没有buffer优化时，设为4的时候：11.329192399978638s

没有buffer优化时，设为8的时候：7.384426593780518s

800M的buffer_pool_size时设为4的时候8.601622343063354s

800M的buffer_pool_size时设为8的时候8.104305505752563s







#### 外键



外键的定义：

```
 Gid = db.Column(db.Integer,db.ForeignKey('role.id'))
```



表的创建

```python
class Role(db.Model):
    id = db.Column(db.Integer,primary_key=True,nullable=False,autoincrement=True)
    role = db.Column(db.String(20),nullable=False)

class UserList(db.Model):
    id = db.Column(db.Integer,primary_key=True,nullable=False,autoincrement=True)
    Username = db.Column(db.String(20),primary_key=True,nullable=False)
    Gid = db.Column(db.Integer,db.ForeignKey('role.id'))

    role = db.relationship('Role',backref=db.backref('roles'))

db.create_all()
```



反向映射：

```python
role = db.relationship('Role',backref=db.backref('roles'))
```

​		是给Userlist模型加一个’role‘属性，可以访问这个角色拥有的用户数据，像访问普通模型一样。

​		

​		·backref·是定义反向引用，可以通过‘UserList.role’来访问这个模型所有的用户

```
#查找哪些用户的角色是admin
user1 = Role.query.filter(Role.role == 'admin').first()
result = user1.roles
for roles in result:
    print('-'*10)
    print(roles.Username)
```

​	表里面的数据

```
role：
 
 admin 1
 
UserList：
	1 mike  1
    2  bike  1
```

结果：

```
----------
mike
----------
bike
```

#### 多对多关系



注意：

1、多对多关系需要一个中间表来关联

2、中间表不能通过class的方式来做，只能通过table来做

3、设置关联

4、访问或数据添加可以通过以下方式进行操作