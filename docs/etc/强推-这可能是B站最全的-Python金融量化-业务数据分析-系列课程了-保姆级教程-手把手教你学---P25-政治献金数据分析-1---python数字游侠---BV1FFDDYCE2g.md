# 强推！这可能是B站最全的【Python金融量化+业务数据分析】系列课程了，保姆级教程，手把手教你学 - P25：政治献金数据分析~1 - python数字游侠 - BV1FFDDYCE2g

看一下这个第二个我们数据分析的一个项目，叫做2012年，美国大选现金项目的数据分析啊，那么这个需求呢已经给大家列出来了。



![](img/12f821d64911a8f2abcad3bb0e536b64_1.png)

然后呢这个需求所对应的数据呢，我们可以看一下啊，它所对应的是53万很多条的数据啊，那么这组数据的来源呢，是来源于2012年的时候。



![](img/12f821d64911a8f2abcad3bb0e536b64_3.png)

对应的一组数据，OK吧好，那么我们首先呢可以把这组数据呢，根据我们的需求呢进行一个加载吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_5.png)

加载完了之后，我们可以根据下边逐条的需求，进行一个实现就OK了，OK吧好我们在这的话，首先读取数据PDDRREID杠CSV，好在这个当前目录有一个data data，下边有个USA杠。

selection点TXT的文件，看是不是啊，selection uc下划线啊，uc got a election ele好。



![](img/12f821d64911a8f2abcad3bb0e536b64_7.png)

那这样的话，咱们就可以把这组数据呢读取出来了，然后读取出来之后呢，把它赋值给DF。

![](img/12f821d64911a8f2abcad3bb0e536b64_9.png)

在这呢我们首先先看一下这组数据啊，好那么这组数据我们看一下。

![](img/12f821d64911a8f2abcad3bb0e536b64_11.png)

他一共有很多列组成对吧，那么每一列各表示什么意思呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_13.png)

好我们来看一下啊，那首先我们重点记住如下几类就可以了，第一个叫做can的name，这个表示是候选人的姓名，就是当年参与竞选的这个人的名字吧对吧，这个叫做呃呃country杠name。

这个是不是捐赠者的名字呀对吧，这是捐赠者所在的周，这个是捐赠者对应的公司或老板吧，这个是捐钱的那个人的职业是不是，然后的话这个是捐赠的金额，下边是捐赠的日期吧，这几列你记住就可以了，OK吧。

那么你要知道各个党派在参与竞选的时候，他们会推选几个候选人，对不对，然后的话这几个候选人会在各地做演讲了，然后去拉阿米拉这个选票对吧，然后选票拿完之后的话，还会有一些财团给这些个啊，这些个党派。

这些个竞选人捐钱，是不是，那么我们大概会知道啊，那如果啊某一个党派他所拿到捐赠的钱越多，那么意味着他所啊他所这个得到的知率越高，那么他参与竞选成功的几率也会越高吧对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_15.png)

所以在这我们知道这么几列存在就可以了啊，那在这我们看一下吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_17.png)

比如说你看这个是我们捐赠者的名字呀，不是这个是候选人的名字，是不是好这个是捐赠者的名字。

![](img/12f821d64911a8f2abcad3bb0e536b64_19.png)

这个是什么呢，捐赠者对应的城市对不对。

![](img/12f821d64911a8f2abcad3bb0e536b64_21.png)

这个是捐赠者的公司或者老板吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_23.png)

这个是捐赠者的职业，是不是这个是a amt是吧，amount金额，捐赠的金额对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_25.png)

后边是不是捐赠日期啊，对不对好，那么这块的话数据咱们就读取出来了。

![](img/12f821d64911a8f2abcad3bb0e536b64_27.png)

然后接着往下去看啊，在这查看数据的基本信息。

![](img/12f821d64911a8f2abcad3bb0e536b64_29.png)

我们刚才已经看过了吧对吧，然后在这儿看一下下边的需求，我们说对新数据进行总览。

![](img/12f821d64911a8f2abcad3bb0e536b64_31.png)

查看是否存在缺失的数据，那在这的话我们一共啊一共有十几列的数据吧，我们要看一下哪些列当中存在缺失的数据吧，对不对好，那这怎么去做呢对吧，这怎么去做呢，首先我们说查看存在缺失数据，我们有两种方式吧。

第一种使用instant note呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_33.png)

Ending all，第二种就是使用DF点info，是不是就可以了，SONY看一下我们的数据。

![](img/12f821d64911a8f2abcad3bb0e536b64_35.png)

一共是436041条吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_37.png)

对不对，然后你找往下去找吧，你看首先这个什么这一列这一列的话，看一下是不是436041啊对吧，那哪一列比较少呢，你看这一列这一列就是捐赠的城市，是不是这个是436026啊，不是少了一些少一些。



![](img/12f821d64911a8f2abcad3bb0e536b64_39.png)

那少的这些都是空值吧对吧，你看不是它也有空值对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_41.png)

这是不是也有空值，是不是很多都有空值啊对吧，都有控制啊，那这块的话我们就看一下哪些这个列的数值，不是436041。



![](img/12f821d64911a8f2abcad3bb0e536b64_43.png)

那么意味着它在列当中就有空值吧对吧，使用info就可以了吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_45.png)

就可以了啊，好然后他说第二个需求，第二需求啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_47.png)

这块我们会学到一个新的函数啊，他说这个用统计学指标，快速描述数值型属性的概要。

![](img/12f821d64911a8f2abcad3bb0e536b64_49.png)

这个就是说我们可以使用DF调一个，什么叫做describe descript，好走你好。

![](img/12f821d64911a8f2abcad3bb0e536b64_51.png)

那么这个DESCRIPT返回的是啥呢，看这块的话，首先mt跟这个feel杠number，这两列对应的是数值型数据，那就是说这个describe可以将我DF当中，数值型的数据进行统计描述。

那在这的话我会看一下。

![](img/12f821d64911a8f2abcad3bb0e536b64_53.png)

看这个捐赠金额是数值型的数据吧，这块会统计什么，统计这一列的个数，这一列的什么的均值，这一列的方差，这定最小值，这列最大值懂懂懂懂吧对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_55.png)

这个是describe所对应的一个统计函数啊，这个我们知道怎么调就可以了啊，再往下看这个需求空值的处理对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_57.png)

空值处理啊，好，首先啊在这儿的话说，可能因为忘记填写或者保密等原因，相关字段出现了空值，将空值填充为not这个PROVOTE对吧，provide啊，那把它填充一下。

那这块是不是将我们所有控制都填充成它呀，对不对，那这块咋填充呢，直接使用我们的FIONA行不行，DF5。2FIA对吧，VUE就等于它是不是统一的，将所有的控制填充成它呀。



![](img/12f821d64911a8f2abcad3bb0e536b64_59.png)

对吧好，那现在的话我们再次的调一下这个统计描述啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_61.png)

再次调一下这个info吧，DF第2info看一下。

![](img/12f821d64911a8f2abcad3bb0e536b64_63.png)

那我们的列当中还是否存在空值呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_65.png)

看一下是不统一的，都是什么呀，C1C1C1C1啊，没有控制了吧，因为空值都被我们填充了。

![](img/12f821d64911a8f2abcad3bb0e536b64_67.png)

是不是，OK吧，好这是空值的一个处理啊，看下边的一个需求。

![](img/12f821d64911a8f2abcad3bb0e536b64_69.png)

说异常值的处理啊，异常值处理好，在这儿的话看一下需要我们做什么处理啊，然后他说这个将捐赠金额，小于等于零的数据删除，那首先看下DF。



![](img/12f821d64911a8f2abcad3bb0e536b64_71.png)

这是我们的原始数据吧，在原始数据当中呢有一列叫做amount吧是吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_73.png)

他说amount这一列当中啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_75.png)

有一些值是这个小于等于零的吧，将小于等于零的值给它删掉。

![](img/12f821d64911a8f2abcad3bb0e536b64_77.png)

OK吧，因为你捐赠金额不可能是零吧，零就没捐呗。

![](img/12f821d64911a8f2abcad3bb0e536b64_79.png)

没接为什么要记录呢对吧，更不可能均入是负数吧对吧，那在这咱们就去处理一下吧对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_81.png)

这怎么处理呢，首先我们是不是可以先找到什么，先找到这个amount这一列当中。

![](img/12f821d64911a8f2abcad3bb0e536b64_83.png)

哪些值是小于等于零啊对吧，好，把这一列呢先取出小于等于零，在这先做判断，是不是我们就判断哪些值为小于等于零。



![](img/12f821d64911a8f2abcad3bb0e536b64_85.png)

是不是OK吧，这返回的一定是一组布尔值吧，true就表示什么呢，该行对应的捐赠金额是小于等于零的吧，接下来是把这个布尔值作为原数据的行索引，对吧，ALLOCK好，把它作为原数据的一个行索引。

这块咱们就获取了什么呀，这个捐赠捐赠这个金额小于等于零的行数据吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_87.png)

走你看这些行数据，我们来找一下，捐赠金额是不是都是小于等于零的呀对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_89.png)

那么这些是不是都是我们的异常值啊，是不是把这些都删掉就可以了对吧，删掉就可以了吧对吧，那在这怎么去删呢，那在这的话，首先咱能不能获取这些异常值所对应。



![](img/12f821d64911a8f2abcad3bb0e536b64_91.png)

行数据的行索引啊，DROP杠indexes对吧，这里边所存储的是我们异常数据，所对应的行数据的什么呢，行索引吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_93.png)

OK吧，那把这些行从原始数据里边删除，是不是就可以了，对吧上吧，DFDR什么呢，DROP是不是job呀，job啊，好label s就等于什么呢，d r o p job杠indexes吧，对不对。

好多了个等号啊，我们删的是行吧，行是谁行，是不是领了对吧，in place啊，in place等于true，将删除操作映射后源数据吧，同理，那现在我们就已经将异常值进行了一个删除。



![](img/12f821d64911a8f2abcad3bb0e536b64_95.png)

是不是OK吧，这一步也是比较简单啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_97.png)

看下一个需求说需要新建一列。

![](img/12f821d64911a8f2abcad3bb0e536b64_99.png)

这一列为各个候选人所在的党派。

![](img/12f821d64911a8f2abcad3bb0e536b64_101.png)

看这怎么实现啊，那首先看一下我们的原始数据，原始数据当中有没有一列显示的是候选人所定。

![](img/12f821d64911a8f2abcad3bb0e536b64_103.png)

能打开呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_105.png)

是没有啊对吧，没有啊，那就是说我现在啊，我知道有一个候选人是不是这个人呐对吧，叫做贝克汉姆，是不是啊，那么这个人啊这个人他是属于哪个党派呢，在这一行里边是不是没有显示啊对吧。

那我就想知道每一个候选人所对应的党派是谁，能怎么去做呢，首先那这些候选人所对应的党派，你上网去搜是能搜到的吧对吧，收到之后怎么办呢。



![](img/12f821d64911a8f2abcad3bb0e536b64_107.png)

收到之后啊，我在这把它全部封装到一个字典当中看一下，那么前面的K就是我们的竞选人吧，候选人后边是它所对应的反派，那么这个值我们是可以从网上搜到的，OK吧，那首先把它封装成一个字典。

字典叫做parties，是不是parties啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_109.png)

好那么这个字典有了，接下来我们要干什么呢，不需要新建一列一列。

![](img/12f821d64911a8f2abcad3bb0e536b64_111.png)

为每一个候选人所对应的党派啊，那么是不是意味着我们需要给每一个候选人，绑定一个党派的值对吧，那这毫无疑问需要用到咱们的映射，是不是映射啊，就说我们需要给谁呢。

给看的杠name是不是给每一个候选人的名字对吧，点map映射或绑定一个什么呢，叫做党派吧，那么我们需要有一个映入式关系表，那刚好啊，前边的这个parties的字典，就可以充当咱们的映射文件表吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_113.png)

等你不是拿到每一个候选人锁就能打开了，再把它汇总到原数据吧，好在原数据当中我新建一类叫做part配对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_115.png)

就等于我们这映射的结果吧，SONY那这块的话我们来看一下，有数据d f d r head走。

![](img/12f821d64911a8f2abcad3bb0e536b64_117.png)

你这会不会有一列对应的是party，有了吧，那么这个值就是我们的该行所对应候选人，所对应的一个打开是不是不会吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_119.png)

好这个需求也可以啊，不难再往下去看啊，说要查看party这一列有哪些不同的元素啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_121.png)

party这一列有哪些不同元素，首先是先把party这列取出啊，DF封号party好。

![](img/12f821d64911a8f2abcad3bb0e536b64_123.png)

这列取出了，那这一列有哪些不同元素呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_125.png)

是不是得去下除啊，点uni可整理不，一共有1234个不同元素啊，那么意味着12年参与竞选的党派，是不是一共有四个呀对吧，当年一共是四个党派参与了此次竞选吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_127.png)

对不对，好去虫，我们知道啊，好那下一个需求啊，说统计party列当中各个元素出现了一个次数。

![](img/12f821d64911a8f2abcad3bb0e536b64_129.png)

那统计一下party，这列是不是一共有四个不同元素啊，我们要统计这每一个元素，在原数据当中出现的次数，啥意思。



![](img/12f821d64911a8f2abcad3bb0e536b64_131.png)

我们要知道原始数据当中，一行是表示一个候选人所对应的。

![](img/12f821d64911a8f2abcad3bb0e536b64_133.png)

一组捐赠信息啊，那么意味着啊，如果哈在原始数据当中，哪些党派出现的次数越多，那么意味着这个党派接受到捐赠的次数越多吧，接受捐赠的次数越多，那么相对来讲他接受到的捐赠的金额就会越多，是不是。

那么意味着他可能得到的知率越高，可能竞选成功的几率越高，是不是OK吧，那这怎么去统计呢是吧，怎么统计呢，好DF，首先的话我们在这先取出party这列，party这列一共有四种不同元素吧。

要统计它们出现的个数怎么办，点一个value杠。

![](img/12f821d64911a8f2abcad3bb0e536b64_135.png)

走，你看一下value杠cos，是不是可以帮我们去统计series当中，每一个元素所出现的次数啊，你会发现是不是前两个党派出现次数最多，那么意味着这两个党派接受到捐赠的次数越多。

那么最终他可能拿到的钱越多吧，那么有可能竞选成功的几率越高，是不是，OK吧，这块我们要知道value counts，它是用来统计series当中，每一个元素出现的一个次数。



![](img/12f821d64911a8f2abcad3bb0e536b64_137.png)

OK吧好啊，再往下查看各个党派收到政治现金的一个总数。

![](img/12f821d64911a8f2abcad3bb0e536b64_139.png)

哎这个就有意思了，查看每一个党派收到政治现金的总数吧，那我们通过上边的一个需求，咱们大概能够知道是不是，前两个党派是接受到捐赠的次数越多，那么是不是意味着他拿到的钱就越多呢对吧，那在这就可以做验证吧。

我们需要统计查看，每一个党派收到政治现金的总数是多少吧，我们知道是不是一共有四个党派呀，那这四个党派在当年竞选的时候，每个党派都拿到多少钱的，就拿到多少总额的政治现金呢，是不是得算一下呢，这怎么去算呢。

这能不能用分组啊，分组聚合呗，是不是DFDR格式或败，分组的话，首先有一个分组条件吧，分组条件应该是谁，查看每个党派收到政治现金的总数，那分组条件应不应该是党派啊，党派是谁，是不是原始数据party。

这里对吧好，然后的话我们要看政治现金的总数吧，那么这一列表示的，是不是我们的这个捐赠金额啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_141.png)

看一下amount这一列是不是我们的这这总金额。

![](img/12f821d64911a8f2abcad3bb0e536b64_143.png)

不要求这列的和对不对，要知道每一个党派接受到捐赠的一个什么呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_145.png)

SAM吧，tony看一下一共有四组数据吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_147.png)

意味着你看republic，这个党派收到的政治现金最多啊，因为你看后边是不是八次方对吧，其次是这个什么呀，Demove correct，对不对，是不是这两个呀，跟上面是不是一样的呀。

就这两个捐赠次数出现的次数越多，意味着接受到捐赠次数越多，那么意味着这两个哪怕是不是拿到钱，也数越多啊，对不对啊，那么意味着这两个党派。



![](img/12f821d64911a8f2abcad3bb0e536b64_149.png)

那可能竞选成功的几率会越大，是不是会越大啊，好再往下看啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_151.png)

查看具体每天各个党派收到政治现金的总数啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_153.png)

这个那这个跟上一个需求是不是有点雷同啊，对吧，他说查看每天各个党派收到政治现金的总数，那跟上一个需求有怎样的区别呢，不是多了一个分组条件叫每天啊，每天不就是我们的日期吗，那这个是多条件分组，OK吧。

DF点group by好，那这块by分组条件就不光是我们党派了吧，是不是还有日期啊对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_155.png)

日期那一列叫什么，看看啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_157.png)

不是日期DT啊，日期是不是好，首先是不是根据日期分组，再根据我们的party分组，是不是两个分组条件对吧，要求的是政治现金的总数吧，这个是不是我们的政治现金啊。



![](img/12f821d64911a8f2abcad3bb0e536b64_159.png)

对不对，然后点sum整理看一下啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_161.png)

在这看是不是在这一天这一天啊，在这一天，那么这两个党派分别拿到的捐赠，是不是50跟12635啊对吧，那这一天啊这一天是不是一共有四个党派，是不是接收到捐赠了对吧，他们拿到的钱分别是这几个数呗，OK吧啊。

那这块是我们的这个分组条件为多个，那你看一下这啊，这的话你看这个分组条件谁在前，谁在后是有区别的，谁在前先基于先分组，那就是说我们是先基于时间分组之后，再对党派分组吧，对不对啊，这是我们的多条件分组啊。



![](img/12f821d64911a8f2abcad3bb0e536b64_163.png)

好再看下一个需求。

![](img/12f821d64911a8f2abcad3bb0e536b64_165.png)

将表中的日期格式转换成我们中国所显示的。

![](img/12f821d64911a8f2abcad3bb0e536b64_167.png)

年月日的格式吧，对不对好，首先我们看一下啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_169.png)

这个df d r head的在原始数据当中，它的日期格式。

![](img/12f821d64911a8f2abcad3bb0e536b64_171.png)

是不是老外所用的这种日期格式啊，对吧，那这个是不是我们的这个这个偏月年吧，我们要把它转成年月日吧，并且这个月份是不是得转换成这个这个数字啊，而不是英文的。



![](img/12f821d64911a8f2abcad3bb0e536b64_173.png)

是不是这块怎么去做呢对吧，这块咱们需要是对这一列的数据，进行某种形式的运算啊对吧，那咱们可不可以使用map嘛，map不就是说可以做映射，也可以对指定的一列对应的这个series，做某种形式的运算。

那这块做什么运算呢，我们能不能将每一个cs元素根据减号拆分，拆分完了之后重新组合呀，可以吧，应该是先取得我们日期这一列，是不是好，先取得日期这一列，好然后是不是点map，OK吧。

那这里边我们就写一个什么呢，运算的法则吧，我们叫做transform data对吧好。

![](img/12f821d64911a8f2abcad3bb0e536b64_175.png)

那首先前面去定义一个什么呢，叫做transform data，需要传一个参数吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_177.png)

D是不是我们的日期啊对吧，咋做呢，首先先D点4p date是不是拆分对吧，根据减号拆分吧，那返回的应该是什么呢，应该是我们的day天数是吧，month跟一二吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_179.png)

那这里的mouth是不是得需要转换一下的呀，mouth是不是英文的，把它转成数字吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_181.png)

怎么转呢，之前我可以在这呢先定一个字典，你看这个字典当中它的key是英文形式的，月份值，是不是数字形式月份啊。



![](img/12f821d64911a8f2abcad3bb0e536b64_183.png)

那在这的话我就可以把什么呢，可以把我们的这个英文形式的月份作为K，作为K这返回的不就是数字形式的月份吗，对不对，那在这啊，在这就是将英文形式的月份，转换成了数字形式的月份吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_185.png)

对不对好，那是不是最后拼接一下子呀，return啊，拼接怎么拼接呢，首先这个是你是二零几几年啊对吧，拼上一个什么呢，是不是页啊，加上一个什么呢，井号再加上一个月份吧，月份是不是数字形式啊，不转成字符串。

能拼接month，对不对，好，再跟上一个减号，再跟上一个倍数不就可以了。

![](img/12f821d64911a8f2abcad3bb0e536b64_187.png)

OK吧好走，你转好了吗，转好了吧，转号这怎么办，是把它再赋值给原始的这一列。

![](img/12f821d64911a8f2abcad3bb0e536b64_189.png)

造完事了吧对吧，再看一下咱们的原始数据呗。

![](img/12f821d64911a8f2abcad3bb0e536b64_191.png)

好原始数据看一下这个月份啊，看下时间都转好了，是不是年月日的性质啊对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_193.png)

这块咱们是使用map，对我们的某一列所对应的FS，进行了某种形式的运算吧，运算不一定非得是数值形式的运算吧对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_195.png)

其他的也叫做运算，是不是OK吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_197.png)

好那么只剩下最后一个了啊，最后一个查看老兵这个职业的捐赠者最支持谁。

![](img/12f821d64911a8f2abcad3bb0e536b64_199.png)

是不是OK吧，首先你要知道啊，老兵啊，它对应的是捐赠者的职业吧是吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_201.png)

在原始数据当中，你看有一列叫做OCC，是不是这一列，是不是我们表示的是捐钱的那个人的职业啊，对吧，我们现在就想看啊。



![](img/12f821d64911a8f2abcad3bb0e536b64_203.png)

捐钱的人肯定是有各种各样不同的职业吧对吧，那么这些职业当中有一个职业就是退伍老兵，对吧，退伍老兵，我们想看一下，退伍老兵到底最支持的人是谁吧对吧，那这你要知道，那怎么知道他最支持谁呢，是不是给。

捐赠的钱越多，表示越支持谁啊对吧，你比如说老兵，如果不就是这个不支持某一个人的话，会给他捐钱吗，不会吧，肯定是老兵支持他才会给的申请吧，那老兵可能有一百一百万个老兵对吧。

那么这100万个老兵最主要的支持的人是谁呢，对吧，这算出来吧，那么在这咱们就需要知道啊，老兵支持的人是谁，那么意味着看他给谁捐的钱最多吧，他给谁捐的钱最多，意味着他越支持谁吧，对不对，那第一步怎么做。

第一步我们就可以啊，可以先将什么将原数据中的老兵，老兵这个职业对应的行数据取出吧，因为现在我们只针对老兵了，不针对其他职业了吧，我先把老兵所对应的这个职业的行数据取出吧。

争取呢DF原数据是不是先取出我们的。

![](img/12f821d64911a8f2abcad3bb0e536b64_205.png)

就是我们先取出我们的职业这一列职业哪呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_207.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_208.png)

找到我们的职业哈。

![](img/12f821d64911a8f2abcad3bb0e536b64_210.png)

这个吧是我们的职业，对不对。

![](img/12f821d64911a8f2abcad3bb0e536b64_212.png)

好，先把职业找出啊，找出职业这一列好，我们看一下职业这列当中到底谁是老兵吧，老兵是谁，老兵是这个啊，这么写的，这返回的是不是一组布尔值啊，布尔值当做true对应的不就是老兵吗。

再把它作为原数据的行索引吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_214.png)

现在我们所取出的，不就是老兵所对应的好数据吗，你看这的OCC是不是都是老兵啊。

![](img/12f821d64911a8f2abcad3bb0e536b64_216.png)

基本都是老兵啊，好那么老兵所对应的函数有了，那这保存一下DF杠什么呢。

![](img/12f821d64911a8f2abcad3bb0e536b64_218.png)

杠old，可以吧，DF杠order里面存的是老兵所对应的行数据吧，那我得知道这个老兵到底最支持谁呢对吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_220.png)

那下一步怎么做呀，下一步我们应该是根据谁去做怎样的操作呢，下边我们能不能做分组啊，分组分组怎么分组呢，那在这我们可以根据候选人分组，然后的话再对捐赠金额，求和是不是就可以了。

DF杠old d2group by我们要对捐赠者做分组吧，by等于什么呢，can的杠name，不就是捐赠这个这个这个这个是后候选者吧，对吧，对候选人分组啊，分组之后我们要对金额是不是求和呀。



![](img/12f821d64911a8f2abcad3bb0e536b64_222.png)

金额是不是那个amount对吧，amount啊，amount是不是它呀。

![](img/12f821d64911a8f2abcad3bb0e536b64_224.png)

求和是不是点儿sum啊，走你这块我们看一下返回的是不是四个值对吧。

![](img/12f821d64911a8f2abcad3bb0e536b64_226.png)

四个值啊，这四个值当中看谁的值最大，不是42。5，最大42。5就能是谁，是不是奥巴马，那么意味着在这我们都知道，那么老兵最支持的人是奥巴马，为什么呢，是因为老兵给奥巴马这个人捐的钱是最多的吧。

因为知道老兵一共是给四个人捐过钱吧，但是给奥巴马捐的钱最多，那么意味着大部分的老兵最支持的人，就是我们的奥巴马，对不对，好，那这块的话，咱们就求出老兵最支持的人就是奥巴马了吧。



![](img/12f821d64911a8f2abcad3bb0e536b64_228.png)

对吧，至此的话咱们这整个的需求也就实现了，你会发现这个需求并不难，在这里边，咱们是不是大量的用到了分组跟映射啊是吧，包括我们的运算工具是不是OK吧，但是这里的话交叉跟透视表没有用到，是不是没关系啊。

我们到数据分析基础的试讲之后，所有的内容讲了之后，我们会有一个综合的大的一个项目啊，在大的项目当中，我们会用到我们这个前面所讲到所有的知识点。



![](img/12f821d64911a8f2abcad3bb0e536b64_230.png)