### Liquid
* 将对象或属性暴露在window全局变量，获取全局变量下的属性
* Liquid是一门由Shopify使用Ruby实现的开源模板语言。
* Liquid的代码分为这三部分对象（object），标记（tag），过滤器（filter）。

#### 对象（object）
```Liquid 
    {{object.property}}
    // 上面的代码会获取到object对象的属性值
```

#### 标记（tag）
```Liquid
    {% if user %}
        {{ user.name }} Hello World
    {% endif %}
    // 上面代码如果存在全局变量user后输出 joan Hello World，否则不输出
```

##### 标记分为三类（控制流，迭代，变量赋值）
###### 控制流
- if和unless的用法
    - 控制流就是编程语言的基本控制结构了。控制流包括了if，这个前面已经说过。还包括了unless，这个unless是意思是除非！if是条件成立执行，而unless是条件不成立时执行，这俩一定是对头呐！下面看看unless的用法，和if的用法一模一样。

```Liquid
    {% unless user.name = "joan" %}
        Hello joan
    {% endunless %}
    // unless 跟if的用法基本一样，区别就是条件相反，unless是指条件不成立的时候时执行
```
* elsif/else的用法
```Liquid
    {% if user.name == "joan" %}
        Hello joan
    {% elsif user.name = "bb" %}
        Hello bb
    {% else %}
        who are you?
    {% endif %}
```

* case/when的用法
```Liquid
    {% assign name = "joan" %}
    {% case name %}
        {% when "joan" %}
            Hello joan
        {% when "bb" %}
            Hello bb
        {% else %}
        who are you?
    {% endcase %}    

    // 以上代码输出的结果是  Hello joan
    // 注意: assign 可以理解为用来定义变量如js的var、let
```

###### 迭代和循环
* for和break
``` Liquid
    {% for index in (1..5) %}
        {% if index == 4 %}
            {% break %}
        {% else %}
            {{ index }}
        {% endif %}
    {% endfor %}

    // 以上结果输出1，2，3
```

* continue
``` Liquid
    {% for index in (1..5) %}
        {% if index == 4 %}
            {% continue %}
        {% else %}
            {{ index }}
        {% endif %}
    {% endfor%}

    //以上输出结果为 1，2，3，5
```

* for 和 limit
``` Liquid
    {%for index in (1..5) limit:3 %}
        {{ index }}
    {% endfor %}

    // 以上输出结果为 1，2，3
```

* offset
```Liquid
    {%for index in (1..5) offset:2 %}
        {{ index }}
    {% endfor %}

    //以上输出结果为 3，4，5
```

* limit
    - 用法跟offset一致，区别就是limit输出的是1，2

* reversed反转执行顺序
``` Liquid
    {% assign array=[1,2,3,4,5] %}
    {% for index in array reversed %}
        {{ item }}
    {% endfor %}

    //以上输出结果为 5，4，3，2，1
```

* tablerow生成表格，cols定义表格几列
```Liquid
    <table>
        {% tablerow product in shop.products cols: 2 %}
            {{ product.title }}
        {% endtablerow %}
    </table>

    //以上输出结果为
    <table>
        <tr class="row1">
            <td class="col1">
                joan
            </td>
            <td class="col2">
                bb
            </td>
            ...(shop.products有多少元素就有多少td)
        </tr>
        <tr class="row2">
            <td class="col1">
                joan
            </td>
            <td class="col2">
                bb
            </td>
            ...(shop.products有多少元素就有多少td)
        </tr>
    </table>
```

###### 变量
*  assign
    - 创建新的变量，创建的变量没有啥要求
* capture
    - 创建的变量都是字符串，而且是把自己的所有都赋值给创建的变量。

```Liquid
    {% capture my_variable %}My name is joan{% endcapture %}
    {{ my_variable }}

    // 以上输出的结果为： My name is joan
```

* increment 
    - 它自带加1属性，它创建的是数值变量，其初始值是0，但之后每次调用这个变量的时候，其值自动加1，掉一次加一次，它的加1属性只对自己创建的变量有效

```Liquid
    {% increment my_counter %}
    {% increment my_counter %}
    {% increment my_counter %}

    // 以上输出的结果为 0 1 2
```

* decrement
    - 他的初始值是-1，每次调用减1，跟increment用法一毛一样




#### 过滤器（filter）
* abs
    - 返回一个数字的绝对值
``` Liquid
    {{ -18 | abs }}
    {{ "-18" | abs }}

    //18
```

* append
    - 将两个字符串拼接起来并返回凭借后的值
    - 可以作用于变量(可预先创建变量，并且调用拼接)
``` Liquid
    {{ "./my/fancy/url" | append: ".html" }}

    // ./my/fancy/url.html
```

* at_least
    - 将数字限制在最小值
```Liquid
    {{ 4 | at_least: 5 }}

    // 5
```

* at_most
    - 将数字限制在最大值

* capitalize
    - 将字符串首字母转为大写

* upcase
    - 将字符串中的每个字符都转换为大写形式。对于已经全是大写的字符串不做任何操作。

* ceil
    - 将一个浮点数向上取整并返回一个最接近的整数，（可以是字符串）

* floor
    - 将一个浮点数通过舍弃小数部分得到最近的整数。在 floor 过滤器执行之前 Liquid 会先尝试将输入转换为数字格式。

* compact
    - 删除数组中所有的nil值
    - [参考文档](https://liquid.bootcss.com/filters/compact/)

* concat
    - 就是将数组合并再一起嘛
    - 您可以将concat过滤器串联在一起以连接两个以上的数组
``` Liquid
    {% assign fruits = "apples, oranges, peaches" | split: ", " %}
    {% assign vegetables = "carrots, turnips, potatoes" | split: ", " %}
    {% assign everything = fruits | concat: vegetables %}
    {% for item in everything %}
        - {{ item }}
    {% endfor %}

    /*
    - apples
    - oranges
    - peaches
    - carrots
    - turnips
    - potatoes
    */
```

``` Liquid
    {% assign furniture = "chairs, tables, shelves" | split: ", " %}
    {% assign everything = fruits | concat: vegetables | concat: furniture %}
    {% for item in everything %}
        - {{ item }}
    {% endfor %}
```

* date
    - 将时间戳（timestamp）转换为另一种日期格式
    - 格式化语法与 [strftime](http://strftime.net/) 一致
    - date 能够作用于包含良好格式化的日期字符串
    - 将 "now" (或 "today") 单词传入 date 过滤器可以获取当前时间
    - 输出的日期是最后一次生成当前页面的时间，并不是页面呈现给用户的时间

``` Liquid
    {{ article.published_at | date: "%a, %b %d, %y" }}
    // Fri, Jul 17, 15

    {{ "March 14, 2016" | date: "%b %d, %y" }}
    // Mar 14, 16

    {{ "now" | date: "%Y-%m-%d %H:%M" }}.
    // 2018-08-14 03:36
```

* default
    - 指定一个默认值，以防预期的值不存在。如果左侧的值为 nil、false 或空，default 将输出此默认值。
``` Liquid
    //product_price未定义
    {{ product_price | default: 2.99 }} // 2.99

    {% assign product_price = "" %}
    {{ product_price | default: 2.99 }} // 2.99

    {% assign product_price = 4.99 %}
    {{ product_price | default: 2.99 }} // 4.99
```

* divided_by
    - 将两数相除，如果除数（divisor）为整数，则将相除之后得到的结果向下取整得到最接近的整数（也就是对应 floor 的功能）。
    - divided_by 返回的结果于除数是同一数据类型的,也就是说，如果除数是整数，返回的结果也是整数；如果除数是浮点数（带有小数），返回的结果也是浮点数。

* times
    - 将一个数乘以另一个数

* downcase
    - 用于将字符串中的各个字符转换为小写形式。对于已经是小写形式的字符串没有任何影响。

* escape
    - 对字符串转义操作就是将字符串中的某些字符替换为转义序列（escape sequence），这样整个字符串就能够用于 URL 了。如果字符串不需要转义则不会对字符串做任何操作。

``` Liquid
    {{ "Have you read 'James & the Giant Peach'?" | escape }}

    // Have you read &#39;James &amp; the Giant Peach&#39;?    
```

* escape_once
    - 转义一个字符串并且不修改已经转义过的实体（entities)。对于无须转义的字符串不做任何修改。

* first
    - 返回数组的第一项
``` Liquid
    {% assign my_array = "apples, oranges, peaches, plums" | split: ", " %}

    {{ my_array.first }}

    // apples
```

* last
    - 返回数组中最后一项，用法跟first一样

* join
    - 将数组中的各个字符串合并为一个字符串
``` Liquid
    {% assign beatles = "John, Paul, George, Ringo" | split: ", " %}

    {{ beatles | join: " and " }}

    // John and Paul and George and Ringo
```

* lstrip
    - 删除字符串左侧所有的空白符（制表符、空格和换行符）。字符串中间留白不受影响
``` Liquid
    {{ "          So much room for activities!          " | lstrip }}
    // So much room for activities!(我的右侧还是有留白的)
```

* rstrip
    - 将字符串右侧的所有空白字符（制表符 - tab、空格符 - space 和 回车符 - newline）删除。

* strip
    - 删除字符串左右两侧的所有空白符号（包括制表符、空格、换行符）。对于字符串中间的空白符不做任何处理。

* strip_html
    - 从字符串中删除所有 HTML 标签。

* strip_newlines
    - 从字符串中删除所有换行字符

* map
    - 从对象（object）中提取指定名称的属性的值，并用这些值构建一个数组。

* minus
    - 从一个数中减去另一个数
``` Liquid
    {{ 183.357 | minus: 12 }} // 171.375
```

* plus
    - 两个数相加

* modulo
    - 返回除法运算的余数。

* newline_to_br
    - 将所有换行符(\n)替换成html中的(<br>)标签
``` Liquid
    {% capture string_with_newlines %}
        Hello
        there
    {% endcapture %}

    {{ string_with_newlines | newline_to_br }}

    /*
    <br />
    Hello<br />
    there<br />
    */
```

* prepend
    - 在一个字符串前面附加一个字符串,可以作用于变量

* remove
    - 从一个字符串中删除所有出现的另一个子字符串。

* remove_first
    - 从一个字符串中仅仅删除第一次出现的另一个子字符串。

* replace
    - 将参数中给出的第一个参数全部替换为第二个参数。

* replace_first
    - 将字符串中出现的第一个参数替换为第二个参数。

* reverse
    - 将数组中的所有项的顺序反转。reverse 不能操作字符串。

* round
    - 将浮点数舍入到最近的整数，或者，如果传入的参数是一个数值的话，将浮点数舍入到参数指定的小数位。
``` Liquid
    {{ 2.7 | round }} // 3

    {{ 183.357 | round: 2 }} // 183.36
```

* size
    - 返回字符串中所包含的字符数或者数组中所包含的条目数量。size 还支持“点标记”（例如 {{ my_string.size }}）。这种用法便于你在标签（tag）中使用 size 过滤器，例如条件判断标签（tag）。
``` Liquid
    {{ "Ground control to Major Tom." | size }} // 28

    {% assign my_array = "apples, oranges, peaches, plums" | split: ", " %}
    {{ my_array | size }} // 4

    {% if site.pages.size > 10 %}
        This is a big website!
    {% endif %}
```

* slice
    - 只传入一个参数时将返回此参数作为下标所对应的单个字符。第二个参数是可选的，用于指定返回的子字符串的长度。
``` Liquid
    {{ "Liquid" | slice: 0 }} // L

    {{ "Liquid" | slice: 2, 5 }} // quid

    {{ "Liquid" | slice: -3, 2 }} // ui
```

* sort
    - 对数组中的所有进行排序。排序后的数组是按照区分大小写的顺序排列的。

* sort_natural
    - 对数组进行排序，并且大小写无关。

* split
    - 根据参数传入的分隔符将字符串分解为数组。split 通常被用于将以逗号为分隔符的字符串转换为数组。

* truncate
    - 将字符串截短为指定的字符个数。如果指定的字符数量小于字符串的长度，则会在字符串末尾添加一个省略号(…) 并将此省略号计入字符个数中。
    - 可自定义省略号（默认：...）,当然，省略号的字符包括再截取字符的长度中
    - 若不需要省略号，给个空字符串就可以了

* truncatewords
    - 将字符串截短为指定的单词个数。如果指定的单词数量小于字符串中包含的单词个数，则会在字符串末尾添加一个省略号(…)。
    - 等同以上的自定义省略号

* uniq
    - 删除数组中重复的东东。

* url_decode
    - 对于作为 URL 进行编码或通过 url_encode 编码的字符串进行解码。

* url_encode
    - 将字符串中非 URL 安全的字符转换为百分号编码（percent-encoded）的字符。