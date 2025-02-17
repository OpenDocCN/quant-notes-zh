# 强推！这可能是B站最全的【Python金融量化+业务数据分析】系列课程了，保姆级教程，手把手教你学 - P28：2.数据的预处理~1 - python数字游侠 - BV1FFDDYCE2g

在我们的第一部分对数据的类型进行处理。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_1.png)

对我们数据进行相关的预处理的操作啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_3.png)

那首先第一步一定是加载数据，是不是先把我们的数据加载出来啊，好那在这的话我们对应的模块已经导入了。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_5.png)

导完之后呢，我们就将这个文本文件当中的。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_7.png)

这7万条用户消费记录，所对应的数据先读取出来吧，对吧好，pd d r read杠CSV，好，在当前文件夹有一个data data，下边有一个这样的文本文件。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_9.png)

把它里的内容读出来吧，走看一下，那我这儿读出来的数据是不是有问题啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_11.png)

对不对，你看这是几行几列的对吧，在这儿你很难能够观测出它的行列的一个顺序，对不对啊，包括每一列表示的点的含义呢，你也不知道吧，首先我们逐步的去处理啊，那在这首先看那它的列索引，不该是我们的原本数据吧。

对不对，那怎么办，在这加一个header等于啥呢，header等于header等于一个什么呢，none是不是就可以了对吧，那现在的话我会发现我们整个的数据，它是不是一共有零列N行啊对吧。

我们想要的是不是四列啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_13.png)

四列对不对，那你会发现原始数据它的分割啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_15.png)

分割是用不同个数的空格来分割的吧，对不对，所以说在这的话啊，我们需要给它加一个什么呢，SEP是不是SEP等于什么呢。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_17.png)

等于一个空格行不行，一个空格是不行啊，因为你会发现这个什么呢。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_19.png)

这个分割所对应空格的个数是不一样的吧对吧，那这咱们用什么呢，用一个杠S加R就可以了。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_21.png)

杠S加是不是表示N个不同的空格啊对吧，那在这的话我们就给它分成了什么呀，分成了四列吧是吧，四列啊，0123是它隐式的列索引吧对吧，那每一列所表示的含义是什么呢，在这我们还可以给它添加一个什么呢。

叫做name，那么四是用来指定他的列索引的啊，那在这的话我们来看一下。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_23.png)

那我读取数据的每一列起个名字呗，我们说第一列是不是用户id啊，我们叫做user杠id，可以吧，那这块的话第二列我们叫做购买的日期，是不是叫做all，order杠什么的，DTOK吧好，那么第三列的话。

我们叫做购买产品的数量吧是吧，我们叫order杠，对吧，那第四列啊，第四列的话是我们的消费金额吧，我们叫做order杠啊，mount可以吧，走你看一下，现在的话，我们这四列它所对应的列索引是不是就有了。

对吧，好在这我们去保存一下，用DF对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_25.png)

现在我们的数据是不是就加载出来了对吧好，那这四个字段啊，所对应四列，它的含义在这我们都已经表示出来了吧对吧，表示出来了啊，好这块是我们的第一部分。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_27.png)

叫做啊这个数据的加载是吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_29.png)

这块我们的数据就已经成功的加载出来了啊，好再看一下我们的第二个需求是什么啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_31.png)

第二个需求，那在这的话我们需要去观察我们数据吧对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_33.png)

首先我们说查看数据的数据类型，查看数据中是否存在缺失值，是不是，那这块怎么去做呢，这块直接是不是DEF点，我们通过调一个什么，调一个info是不是就可以了。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_35.png)

首先我们看一下，那么每一列数据的数据类型分别是什么呢。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_37.png)

看一下user id，包括我们的时间，包括我们购买产品的数量都是int64，是不是整形啊对吧，我们这的消费金额是不是我们的浮点型啊。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_39.png)

对不对，好，这个是我们观察到的每列的数据类型吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_41.png)

那每列是否存在缺失数据呢，我们来看一下整个的数据，它一共是6万9659条数据吧对吧，6965969659，是不是每一列都是69659个非功能数据啊。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_43.png)

对吧，那么意味着我们每一列并没有空值吧对吧，并没有空值啊，那在这的话，这两步咱们是不是就检测出来了对吧，检测出来啊，并没有空值，然后的话我们每一列的数据类型，咱们也知道了吧对吧好。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_45.png)

第三个，我们说将我们的时间转，将我们的这个什么order刚data这一列转成时间类型。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_47.png)

看一下，在这啊，在这我们会发现order杠DT啊，order杠TT它是不是整形啊，我需要把它转成实验类型吧对吧，那这块怎么去转呢，这块我们是不是学过，怎么将我们的一个这个字符串。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_49.png)

或者是将我们整数的一个类型，给它转成时间类型啊，对不对好，那这块就是说我们首先pd点儿啊，pd点我们叫做to杠什么呢，date time是不是，然后在这的话，是不是叫我们DF当中的谁哪一列啊。

是不是这个order杠DT对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_51.png)

是不是将这一列转成时间类型啊，走你转好了吗，转好了吧，那我们想要的时间格式是不是年月日啊，后边这些玩意是不是就不要了，对不对，那怎么办呢，还在这的话。

点format format等于我们控制一下它的格式呗，对吧，应该是年月日是不是就可以了，诶，这个D在这这样的话，是不是得到一个时间类型的数据啊对吧，把它重新的赋值给order杠DD是不是就可以了。

复制给这列吧，对吧好，我们再看一下DF啊，先赋值吧，好赋值完了之后呢，在这我们再看一下DF，点info。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_53.png)

走，你看一下我们的order杠DT，现在就不是int64了吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_55.png)

而是我们的date time64，对不对，是不是时间序列类型对吧，这块已经叫我们的这个类型转好了吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_57.png)

对不对，好在这啊，将我们的order杠DT转成时间类型，OK没问题了吧，好下一个查看数据的统计描述，那这个查看数据统计描述。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_59.png)

就是说在这我们可以看一下什么呢，这个describe它所调用的一个返回值。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_61.png)

就是DFDRDISCRIBE。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_63.png)

JONY好，那在这里边我们可以发现什么呢，在这看一下count count是不是个数啊对吧，个数那就是说我们的user id，order product跟我们的order杠OUNT这三列对吧。

这三列所对应的数值啊，所对应的数值的个数都是69659吧对吧，mean in是不是每一列值的均值啊，为什么只有这三列呢，是因为只有这三列，是不是我们的数值类型啊对吧。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_65.png)

我们的order杠DT10，这个什么这个时间，这一列是不是已经被我们转成了时间类型啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_67.png)

时间类型是不能做数值运算的，是不是，是不是只能是这些个数值型数据。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_69.png)

才能做数值运算，对不对，包括这里的最大值最小值，咱这边是不是都能看出来啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_71.png)

这都能看出来啊，那这块就主要是调我们describe，我们在这可以看到，用户购买商品的平均数量跟平均花费哪个是呢。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_73.png)

用户购买商品的平均数量是多少啊，是不是每用户平，每个用户平均购买了2。4个呀对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_75.png)

他的花费是多少，是不是35啊，对不对，35吧啊好。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_77.png)

然后的话看最后一个啊，最后一个说在原始数据中添加一列表示月份啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_79.png)

那现在我们的原始数据当中，是不是有一列表示的是时间啊对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_81.png)

是时间并没有一列单独表示月份吧对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_83.png)

你看DF点head在这，我们的这四列都表示的是什么呢，用户id购买的时间，购买产品的数量跟购买产品所花费的金额吧，并没有列表示月份吧，那怎么办呢，我们需要新建一列。

让这一列单独表示一个月份是不是就可以了，对吧，咋办呢，DF还要给它新建一列，我们叫做month吧，month表示的是月份，那怎么取月份呢，那看一下，那能不能基于我们的这个order杠DT。

来把月份取出来呢，可以吗。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_85.png)

OK吧好，那这怎么去怎么去取呢，在这的话我们来做一个操作啊，就是说基于什么基于order杠DT啊，DT然后取出啊，取出其中的月份啊，月份这块怎么取呢，首先DF中括号先是我们的order gu dt。

是不是order杠DT吧，那这返回的是一个series，对不对，我们只要的是月份的怎么办，点艾斯type s tape啥意思呀，as tape指的就是说我们需要更换order杠。

DP这一列它的类型吧对吧，类型更换成什么呢，更换成这个date time，64后边加一个中括号M就可以了。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_87.png)

M表示是不是月份10月份吧，看在这的话，我们执行之后看一下，那现在原始的order杠TT，就变成了这样的一组数值吧，你会发现哎这组数值跟原先的好像一样啊。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_89.png)

看下一样吗，不一样，你看这个是什么呀，这个是1月份，1月份1月份1月份是不是这个是3月份，这是不是4月份啊，你看后边的天数是不是都是一号啊对吧，那么意味着这会天数没用，我们只看的是不是月份的月份。

这是这是1月份，对不对，下边是不是3月份4月份的好，那这块咱们就把1月份取出来了吧，那怎么办，是把它赋值给谁。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_91.png)

复制给我们的这个这个这个month节点就可以了，对吧。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_93.png)

看一下我们的数据啊，走那现在我们会发现貌似这表示的是月份的，你看看这一行，这一行对应的时间，消费时间是不是1月1号刚好是不是1月啊，这个消息时间是不是1月12号，对应的是不是也是1月啊对吧。

这个是3月32号对应的是3月，那么意味着month这一列，表示的就是我们的月份吧对吧，它仅仅表示月份啊，而这个order杠DT表示的是咱们具体的消费时间，是不是OK吧。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_95.png)

那怎么从这个时间里边取月份，就是说修改我们时间这一列，它的类型就可以了吧对吧，在类型这块加一个中括M取的6月份吧，那中块地呢取的是不是天呀。



![](img/c7fa670606a3dafb2cf8d077d2681c3a_97.png)

中号Y取的是不是年啊，对不对。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_99.png)

OK吧，那么这是我们的第一部分对应的实现吧对吧，第一部分对应的实现啊。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_101.png)

那么下一小节咱们来看一下。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_103.png)