# 强推！这可能是B站最全的【Python金融分析+量化交易】系列课程了，计算机博士精细讲解，底层原理+项目实战，看完即可就业！人工智能／深度学习／机器学习 - P15：15.均线调参实例 - 口喜口合口合y - BV1su4m1u7TS

![](img/62f376cfe05aa10b670bede6196210eb_0.png)

行了说完这个数字策略之后，然后接下来我们看第二个第二个问题是什么呢，就是现在有个问题啊。

![](img/62f376cfe05aa10b670bede6196210eb_2.png)

哎呀你这个结果你说跟哪些因素有关系，大家可能会告诉我，那跟很多因素都会有关系的，那这里咱们怎么办，咱们也这样，你说现在啊我能不能去找一些参数，比如说我们的一个短线和一个长线，那你不是短线。



![](img/62f376cfe05aa10b670bede6196210eb_4.png)

长期短期和长期，我能不能设置一些不同的值来去看一看，短期究竟是五天合适呢，还是十天合适呢，长期是20天合适呢，还是四天合适呢，我可以算一算这样的组合吧，在探索当中，我们可以很轻松的做这样一个迭代好了。

把迭代工具先导进来，我们先把这个迭代工具啊给他拿到手，from di工具当中。

![](img/62f376cfe05aa10b670bede6196210eb_6.png)

Product，这个东西他就是能帮就给大家解释一下，他是做什么的，其实很简单啊。

![](img/62f376cfe05aa10b670bede6196210eb_8.png)

就是它需要让我们啊啊写反了SMA1，还有一个SMAR，它需要让我们分别去指定好两个参数空间，参数空间你可以自己随便指定，比如说从20，然后到这个61，只要取不到61，最多取到60，然后这么四个一加是吧。

到这里你也可以指定一个range，range当中从多少长线，再稍微大1。180，然后到这个280吧，然后取不到2。8，取到281，然后十个亿价行吧，然后这样，然后再指定一下当前有这个结果啊。

这个参数空间的意思，就是他这个组合的意思是这样，就是呃得能去帮我们去计算出来，所有的一个组合，比如这里你看这个20跟180行吧，20就是有这样一些组合，一块做完之后二十一百八，这是一种组合是吧。

然后20，然后这个112十一百九，然后二十三二十二百，然后二十二百一是吧点点，然后20万多少。

![](img/62f376cfe05aa10b670bede6196210eb_10.png)

二十四一百八，然后呢二十四一百九啊。

![](img/62f376cfe05aa10b670bede6196210eb_12.png)

这个遍历操作，能帮助我们把所有的一个结果，全部给它遍历掉啊。

![](img/62f376cfe05aa10b670bede6196210eb_14.png)

是这个意思，然后呢呃一会儿吧，咱在这里就是风云当中去写吧，在这一块就是我们可以写一下，复原当中我要做便利了啊，编辑当中我要拿到当前的两个参数，就是一个长线和短线，因为什么因这个product当中。

然后我把这个结果给把当前两个参数空间，传进去，这是第一个参数空间，然后这是第二个参数空间，能把所有结果全拿到手，然后在这里做遍地，然后编辑过程当中呢，我会把当前我们的每一个数据，全部给它指定出来吧。



![](img/62f376cfe05aa10b670bede6196210eb_16.png)

就是一个data data，我看一看之前哎呀。

![](img/62f376cfe05aa10b670bede6196210eb_18.png)

这个铁塔当中我得重新算了，在这一块这样吧，把它数据重导一下吧。

![](img/62f376cfe05aa10b670bede6196210eb_20.png)

然后之前那份数据可能让我做乱了，我我为了保险还是重挡一下。

![](img/62f376cfe05aa10b670bede6196210eb_22.png)

在这里好，这把这个数据在这里存档一下。

![](img/62f376cfe05aa10b670bede6196210eb_24.png)

这里吧，导一下data，就是等于我们刚才传进来的数据当中的。

![](img/62f376cfe05aa10b670bede6196210eb_26.png)

只需要我们的一个平衡骨架啊，咱们再把它复制过来，苹果的股价啊。

![](img/62f376cfe05aa10b670bede6196210eb_28.png)

把名字指定出来就可以了，然后现在就是我们拿到数据了。

![](img/62f376cfe05aa10b670bede6196210eb_30.png)

拿到数据之后，不要忘记对数据，还要做这样一个去缺失值的操作。

![](img/62f376cfe05aa10b670bede6196210eb_32.png)

我就全复制了，因为都是相同操作，但是也给大家写了，这里还是第一步去缺失值去完确认之后呢。

![](img/62f376cfe05aa10b670bede6196210eb_34.png)

然后下一步下一步就是我们要去算吧。

![](img/62f376cfe05aa10b670bede6196210eb_36.png)

要去算三个值，一个是return，一个是SMA1，还有SVA2是吧。

![](img/62f376cfe05aa10b670bede6196210eb_38.png)

我这三个结果也都复制过来啊，这是一个return方法都是一样的，这是把returns给它复制过来。

![](img/62f376cfe05aa10b670bede6196210eb_40.png)

然后呢接下来SM1还有SMA2。

![](img/62f376cfe05aa10b670bede6196210eb_42.png)

我也复制一下，在网上哦，这块儿把它拿过来好了。

![](img/62f376cfe05aa10b670bede6196210eb_44.png)

这是一个SMAE，然后我把这个改一改，这是一个SMAR，分别用一个二就可以了，然后看一下对于当前的数据哦，这块也加上一下，刚才咱们就忘了，这里我需要加上就是当前是对谁做这个操作的，然后对我的股价。

这个也是对我的股价来执行这样一个操作，直接操作完之后啊，然后肯定哎呦，这块我估计又会有缺失值，再把这个确缺失值的东西加一遍，那接下来就是我们再去把上面的啊，判断给复制过来。



![](img/62f376cfe05aa10b670bede6196210eb_46.png)

就是一个position，一个东西啊，找一下，相当于把我们之前所有结果咱要做一个汇总了。

![](img/62f376cfe05aa10b670bede6196210eb_48.png)

正常写代码，其实也就是把上面的结果都写在一个函数，当中只不过说在给大家讲解过程当中啊，可能为了方便我就给大家一行一样去写的。



![](img/62f376cfe05aa10b670bede6196210eb_50.png)

方便，咱去展示每一行它的一个结果吗，其实实际上它就是一个函数而已。

![](img/62f376cfe05aa10b670bede6196210eb_52.png)

然后这块我看一下这块，再看一下我们的一个策略好了，然后一个我看到一个position，咱有了return，也有了我们的一个策略，也有了这部转完之后再去掉一个缺失值，每一步都会有缺失值啊，切换缺失值之后。

然后最终我会有一个结果，我把这个结果也拿过来啊。

![](img/62f376cfe05aa10b670bede6196210eb_54.png)

结果就是复制这个把这个结果给拿过来，这是当前的结果吧。

![](img/62f376cfe05aa10b670bede6196210eb_56.png)

好了有结果之后，我说我对这个结果做一个展示吧，展示就是一个result，这个result大家写好看一点，给它写成一个呃表格的一个形式吧。



![](img/62f376cfe05aa10b670bede6196210eb_58.png)

就是resource，这样一开始的时候我先创建一个表格吧，results等于我的一个pandas调呃，创建data frame先是空的吧，然后一会儿再往里去传。

在这个result result等于我这个resource里边，然后呢我看了一下，把它当前但凡我需要的东西全加进来呃，还是加一个data frame，把它做成一个表格的形式。



![](img/62f376cfe05aa10b670bede6196210eb_60.png)

在这个data frame当中啊，我用一个key value的形式，把我的一个列名和它实际的值给传进去啊，第一个名字就是一个SMA是吧，好了，S m a1，然后它的值就是这个SMA1。

然后第二个我直接复制了，第二个就是我的一个SMAR把它复制过来，然后逗号也复制一下，有好几个，这是SMAR，这也是SMA2，然后第三个第三个就是呃我的一个returns。

这个returns把这个returns复制复制过来，这是个returns，然后下一个下一个就是咱们的一个策略，我的这个策略啊，策略拿过来，策略诶，好像复制错了，在这里，这是我，我找一找，这是名字。

这是value名字，value名字value啊，该复制这个了，这是我的一个策略，然后再复制一个就行了，最后一个最后一个咱们写一个吧，就是我的一个输出结果，输出结果相当于就是呃我当前的一个预测值。

然后比你的一个结果值，就是咱用这个策略的结果和，不用这个策略能好多少，哎呦不是把它放到当前这个理当中了吧，那行了，这一块儿我稍微改一改，等于我的一个策略值，我把这个策略值拿到手。

这个是我的一个策略值是吧，用我当前策略值，然后我要去减法。

![](img/62f376cfe05aa10b670bede6196210eb_62.png)

减去一下当前我什么都不做的时候，它的一个结果，这个returns这里咱还得改一改。

![](img/62f376cfe05aa10b670bede6196210eb_64.png)

这块直接return不行，我这块不是把这个returns都放到这里了吗，得给他列一下哦，我看一下，这是呃return，然后returns它的一个结果呃。



![](img/62f376cfe05aa10b670bede6196210eb_66.png)

这块稍微改一改，这块看看这写乱了，S m a2。

![](img/62f376cfe05aa10b670bede6196210eb_68.png)

然后这returns得把它改一下，在这个当中它是取returns这个结果吧，然后策略策略当中也是一样的，我把这个也复制过来，也复制过来之后，然后他取我策略的一个结果。



![](img/62f376cfe05aa10b670bede6196210eb_70.png)

这就行了，我先来试一下吧，看看结果能不能出来，就心需要写这么多，看一下。

![](img/62f376cfe05aa10b670bede6196210eb_72.png)

他说要在我算这个returns过程当中啊，然后就没有了这个key啊。

![](img/62f376cfe05aa10b670bede6196210eb_74.png)

这怎么改啊，先来看一下吧，看一下就是最简单的一个找错误方法。

![](img/62f376cfe05aa10b670bede6196210eb_76.png)

就是他到哪儿遇到问题，然后在前面咱给它打印一下行吧，我说我看一下当前这个结果，他到底咋地了，这个data行不行好了。



![](img/62f376cfe05aa10b670bede6196210eb_78.png)

重新执行一下，我来看一看呃，我看一下打印出来的结果会是什么。

![](img/62f376cfe05aa10b670bede6196210eb_80.png)

打印出来这个数据数据当中只有date，只有我一个索引，但是呢你看他像是把这个数据当中啊，像是没有这个列了是吧，这源于什么，我们当前取完之后，当前我们得到的是一列数据，一页数据当中没有给我带这个名字。



![](img/62f376cfe05aa10b670bede6196210eb_82.png)

所以此时我们得稍微改一改，我得把这个东西啊再加上点东西，你得让它接着成为一个data frame才行吧，所以这块咱这样咱把它呀给它加上一个data frame。



![](img/62f376cfe05aa10b670bede6196210eb_84.png)

data frame给它包起来就行了，应该再来执行一下好了。

![](img/62f376cfe05aa10b670bede6196210eb_86.png)

果然不出所料，又报错了，看一下哦，这块我得再重新执行一下。

![](img/62f376cfe05aa10b670bede6196210eb_88.png)

因为刚才改东西之后，都得从头来执行，来看看啊。

![](img/62f376cfe05aa10b670bede6196210eb_90.png)

这回又报错了，但是报错不太一样，看一下是哪里，最后一行，我做这个result table的时候。

![](img/62f376cfe05aa10b670bede6196210eb_92.png)

构建我的一个数据啊，数据表的时候，然后他说了数据表当中啊。

![](img/62f376cfe05aa10b670bede6196210eb_94.png)

哦哦你必须指定一个索引，但我不需要索引啊。

![](img/62f376cfe05aa10b670bede6196210eb_96.png)

不需要索引也没招啊，就是人家说了，里边是必须去指定索引的，怎么办呢。

![](img/62f376cfe05aa10b670bede6196210eb_98.png)

那人家说指定索引，咱自己指定呗，index随便指定成一个值得了。

![](img/62f376cfe05aa10b670bede6196210eb_100.png)

然后我来执行一下吧，好了执行完了来看一看。

![](img/62f376cfe05aa10b670bede6196210eb_102.png)

先看一下这个结果怎么样，我该随便指定索引哦，看一看，你看我随便指定个零索引。

![](img/62f376cfe05aa10b670bede6196210eb_104.png)

但是感觉有点奇怪，这么对吧，咱便便指定零了，我先给他指定出来索引，然后呢，我说我再给他一个闹一下，用原始的索引，就是按那个数字012345678再标吧，这个索引我给它忽略掉吧。



![](img/62f376cfe05aa10b670bede6196210eb_106.png)

然后再执行一下哦，来执行执行执行，在咱们传进这个索引，传这个索引的时候，那应该是给这个data frame传索引是吧，然后呢ignore的时候应该是对于这个判断来说，我不需要再加索引了。

是不是应该这样一件事吧，所以说啊咱得把这个括号给对应上，但想想怎么改啊，在这里这个index应该是对于data frame当中，我再加个括号，看对不对应上当前怎么加中文逗号，加上一个逗号。

你看现在对应了这个data frame吧，你看这个data frame这亮了，所以说现在一个now我对应了啊，不是这些指令，你DEX对于data frame当中有索引了，然后再看最后一个。

我这个IGNINDEX，他说的是对于我这个判断来说，我不需要再加索引了，来看一看当前我这个括号，这括号好像多了一个。



![](img/62f376cfe05aa10b670bede6196210eb_108.png)

再去掉一个哎，对应上了吧，对应是判断行了，咱再执行一下。

![](img/62f376cfe05aa10b670bede6196210eb_110.png)

这回没问题了吧，展示一下结果，这回我们的索引当中，你看01234咱就全有了吧，SM1SM2就是对于不同的结果，我展示十个吧。



![](img/62f376cfe05aa10b670bede6196210eb_112.png)

对于不同的一个，就是那个什么不同的一个参数选择吧，咱得到结果returns，还有我们策略得到结果，那你看对于不同的结果来说，第一个挺有意思啊，就是看咱们out out，相当于就是我们这个策略。

比那个啥也不做的时候能强多少，那第一个你看如果说你选的这个窗口不太合适，那反而还不如啥也不管了，是不是，但是大部分条件下还是比之前要强一些吧。



![](img/62f376cfe05aa10b670bede6196210eb_114.png)

然后甚至有的还是一个处于持平的一个状态。

![](img/62f376cfe05aa10b670bede6196210eb_116.png)

行了，这个大家看了一下，就是呃咱们又给大家对比了一下，就是怎么样去基于我们不同的参数去选择一下。

![](img/62f376cfe05aa10b670bede6196210eb_118.png)

什么样的一个结果才是一个合适的。

![](img/62f376cfe05aa10b670bede6196210eb_120.png)

这给大家说一下，在Python当中啊，怎么样借助这些工具包，来完成我们的一个双均线策略。

![](img/62f376cfe05aa10b670bede6196210eb_122.png)

来给大家做了一个分析。