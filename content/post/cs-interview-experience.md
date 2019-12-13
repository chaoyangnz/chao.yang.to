---
title: CS interview experience
toc: true
id: 59
categories:
  - Uncategorized
date: "2015-01-13T12:19:39+00:00"
---

本人CS硕士名校非牛人,一年前去了一家中型软件公司做SD,不喜欢,刚刚跳去一家小HF.面试
的过程好像西游记一样,路途遥遥,艰险不断,怪物层出不穷,自己的本领也日渐增长,2年来承蒙
版上各路豪杰照顾分享,今日也算有个结果;特此拿出小弟所见所闻共勉,纪念找工作的艰辛,愿
大家早日心想试成,取到真经!

/***********************
小测验
***********************/

首先来个小测验,看你能看懂多少
1.array,list,BST,Hashtable,queue,stack,suffix tree,collection...
2.BFS,DFS,DP,D&amp;C,Greedy,Dijkstra,tree traversal,recursion,quick
sort...
3.A,F,G,L,M,O,T,Y...
4.OOP,GC,Polymorphism,interface,abstract class,singleton...
5.bar raiser,white board programming,lunch interview...

如果以上任何概念不能熟练给出详细解答,请在往下面看之后抓紧复习1.数据结构(这个如果一
个没看懂可以按后退关窗口了)2.算法3.公司背景4.面向对象编程5.onsite流程.全看懂了也别
骄傲,这其实只是很简单的小测验,拿笔每行接着写10项.

/***********************
公司面试心得
***********************/

下面按照公司我大概讲讲面试题的注意事项,由于有不少是1年前的题目,记得不太清楚,我以框
架为主,后面主要讨论复习要点和经验教训:

A:phone interview会问到关于OOD的概念和设计,onsite有问过题:数组找定和,Hash
table溢出, isBST,Rome letter,GC设计,byte[] getIP(int),deadlock,优化搜索速
度.数组求定和是最最常见的问题,基本每两个interview就有一个问这个,后面还会说到,一定不
要陷入固定思维,自己多想想不同的condition下的解答.听说最近在狂面人,可以在linkedin上
面直接找到HR要面试.

F:现在已经很难进了,主页能找到一些puzzle,很多很tricky,不过也还算不难,觉得普通面
试题缺乏挑战的童鞋可以拿做当onsite前热手,phone里面主要是基础题目,只记得链表反转,以
OOP为主,没有onsite过. http://www.facebook.com/careers/puzzles.php

G:被问到都是非主流问题和设计,什么"估算躺在地上硬币总价值",无语.设计也都是开放性
的,没啥参考价值,可能天生相克吧.

L:很注重java的概念,例如hash原理,多态,继承,gc等的深层概念和implementation,对于
概念有点要求的过严了,但是还算是让人向往的公司吧

M:注重flawless的编程,pointer的操作(能用array/bitwise不要用hashtable),DP,
dictionary/index,recursion.最近狠狠的涨了一下工资,让人很眼红的说

O:基本只看GPA3.8以上或者内荐,面试主要是algorithm,SQL

其实我大公司的面试经历不算多,也只能这样点到为止,但是还有一些公司,招人规模不及以上,
但是我也稍微点评一下.

Bloomberg:变态的测试,据说除了c的测试外其他没有过的记录.面试也是集中与算法和数据
结构,以c/c++为主要背景,压力大工作辛苦薪水高,一但进黑名单貌似是永远不会考虑了

ebay:招人不是很给力,最好内荐,OOP为主,一流的流量一流的薪水二流的软件,我的意思不是
说ebay不好,而是说其实他家软件,尤其比起A来,还有很大上升空间,其实趁着软件还不够好参
与开发是种机会,你懂的

rapleaf:漫天广告,实际上没啥诚意,现在市场转好,建议不要考虑

D.E.shawn/two sigma/citadel:面试门槛很高,但是效率也很高,舍得花钱,就是录取
率不高,建议阅读相应金融知识,并且有相关实习经验或者大公司背景,new grad很有难度

laserfiche:坑爹专业户,如果你从没onsite经历可以去一下,否则就是浪费时间,薪水超
低(尤其以LA为背景)

ning:新兴social network,100人不大但也还算不错的startup,不过他们manager很对
不起我,估计我这辈子是不考虑了,推荐想做social network但是去不了F,L,T的

quantcast:强烈推荐,感觉非常棒,但是codetest也很难,时间很紧张,2个小时测试,到点
的时候只写出2/7个答案,4小时写出完美程序,大概200行的样子,加上10个testcase,然后
就悲剧了,估计就是嫌我慢

addepar:很个性的公司,先一轮phone,然后做蚂蚁大战,时间不限,
[http://addepar.com/challenge.php,要是我在学校可能还有心情测一测](http://addepar.com/challenge.php,%E8%A6%81%E6%98%AF%E6%88%91%E5%9C%A8%E5%AD%A6%E6%A0%A1%E5%8F%AF%E8%83%BD%E8%BF%98%E6%9C%89%E5%BF%83%E6%83%85%E6%B5%8B%E4%B8%80%E6%B5%8B,%E5%9C%A8%E8%81%8C%E5%A4%8D%E4%B9%A0)
根本不太可能静心给他做这个,若是能达到400-600turn过关,那么可以拿到下轮面试,你要
是觉得自己很牛没有题做,试试这个

epic:狂招人,面试比较简单,需要准备presentation,有性格测试

cisco/emc/ibm/adobe:感觉对local看的很重,不太招外州的人

yahoo/SAP:没落中,需求量越来越小

apple:对SDE需求不大,有些ee的职位,手机卖这么好,没听有招人(店面不算)

GS/UBS/Citi/boa:看重GPA,如果你喜欢做纯技术,去了会后悔的;如果想做金融软件,但是
达不到bloomberg和hf标准,是个不错的跳板

fiverings:很小的HF,10几个人吧,问一下brain teaser和古怪的程序,有兴趣可以试一下,
老板是从jane street出来自己做的

其实还有数不完的公司,不过本人主要经历也就这些.

/***********************
复习清单
***********************/

我只列出关键字和一些特别注意的要点,其实一个词可能包含无数知识点:
Array(pointer)
list(reverse,loop,ring)
tree(traversal,search)
sort
queue/stack(BFS,DFS)
hash table
string(suffix tree)
recursion
graph/greedy
divide and conquer
dynamic programming
bitwise
OOD(GC,serialization,exception,UML,singleton)
regular expression
deadlock/multi-thread
I/O,memory,buffer
testing(unit test,white box,black box,development cycle)
Network(TCP/IP,socket)
SQL(index,bcnf,3nf,optimization)
Scale(distributed system)
Security(buffer overflow,protocol)
Machine learning/AI
probability(bayes)
Web programming
最后是behavior(strength,weakness,goals)

参考书和网站:
crack the technical interview(强烈推荐)
programming interviews exposed(强烈推荐)
编程之美
data structures with java
algorithm design
database management system
computer networking
thinking in java
thinking in c++
software testing
java how to program
[http://www.mitbbs.com/bbsdoc/JobHunting.html](http://www.mitbbs.com/bbsdoc/JobHunting.html) (还用多说么)
[http://www.careercup.com](http://www.careercup.com/) (会上面的题不代表就能过面试!)
[http://www.glassdoor.com](http://www.glassdoor.com/)
[http://www.dice.com](http://www.dice.com/)
[http://www.javacertificate.net/core_java_iqns.htm](http://www.javacertificate.net/core_java_iqns.htm)
[http://www.techinterviews.com/core-java-interview-questions](http://www.techinterviews.com/core-java-interview-questions)
[http://javaquestion.tripod.com/InterviewWithAnswer.html](http://javaquestion.tripod.com/InterviewWithAnswer.html)
[http://www.freejavaguide.com/java-interview-questions.html](http://www.freejavaguide.com/java-interview-questions.html)
[http://www.roseindia.net/interviewquestions/](http://www.roseindia.net/interviewquestions/)
[http://www.geekinterview.com/Interview-Questions/J2EE/Core-Java](http://www.geekinterview.com/Interview-Questions/J2EE/Core-Java)
[http://javaquestion.tripod.com/InterviewWithAnswer.html](http://javaquestion.tripod.com/InterviewWithAnswer.html)
[http://www.indiabix.com/technical/core-java/](http://www.indiabix.com/technical/core-java/)

这里小小感慨一下阿三的团结,咱们怎么没有更多像印度玫瑰这样的网站呢...

/***********************
经验教训
***********************/

最后一部分,琐碎的经验教训

1.对方电话里问你下周能不能来,一定要问清楚他们急不急用人,急得话要当机立断,我因此疏
忽本想拖延一下面试避免时间紧张,结果一周后被告知不用去了,四大皆空,干脆悲剧

2.地区是一个很重要的因素,如果你还没开始h1b或者绿卡,在找工作受阻的情况下可以考虑申
请opt然后搬去加州或者纽约,在local找,很多公司为了省phone的多回合和onsite的开销,
只找local或者优先在local找.至于有些在职的人想辞职裸奔挂语言学校,也是个办法.

3.不要背题.迄今为止我还没见到过一模一样的面试题,优秀的面试官一定不会只准备所谓经典
的面试题,一定有改动和引伸.举个例子,数组找定和很简单吧,怎么不用hash table照样
O(n),找3数定和?4数(有O(n^2)解)?负数?已排序数列?写白板!再举个例子hash table大
家都知道,让你用数组实现?hash function?碰撞处理?碰撞处理方法的利弊比较?equal和
hashcode的关系?写白板!希望大家准备的时候能够多问自己问题,写程序也不要只完成题目,多
想想如果哪个要求换了应该怎么改.

4.练习口语.比起阿三,老美不见得喜欢中国的口音,很多人的发音和语法不正确,自己从来没用
心改正过.这里推荐一款iphone的免费app-dragon,
[http://itunes.apple.com/us/app/dragon-dictation/id341446764?mt=](http://itunes.apple.com/us/app/dragon-dictation/id341446764?mt=8,%BE%CD)
是一语音识别软件,你找一篇新闻,认真读看看老美的软件能听懂你多少,反复训练,更上一层楼.
说话的时候尽量嘴巴变化明显一点,试着拉长放慢大声和夸张的读每个单词,尤其是字母lnr的
卷舌鼻音

5.实习经历还是很重要的,重要程度多过绝大部分in class project,争取毕业前能在美国
公司里面有过实习经历.

6.linkedin其实是一个很好的平台,很多recruiter在上面搜罗人才,你要表现的活跃一些,
完善自己的资料.另外如果通过朋友等渠道,你可以找到recruiter的邮箱,一定要主动自报家
门,我干过无数回了,直接email给recruiter,附上简历,说明背景,百试不爽,基本每次都有
面试

7.搞好和同学前辈的关系,最大化争取referral(朋友的朋友也行),有面试什么都能靠你自
己,没面试你自己再牛也不行

8.白板练习很必要.为了面试我用eclipse至少写了60个函数的题目,但是换成白板/白纸,还
是无法每次写出flawless的程序,这个必须多加练习,而且尽量找一些自己没听过的题目或者
变形的题目

9.准备好所有工作经历包括实习的描述,控制好时间,另外着重准备1-2个对你来说重要的
project/design,一定要做到拿起来就能说,而且说的简洁明了重点突出,让不很懂的人也能
听明白

10.behavior question要花时间准备,准备过的话一问就能问出来,说话要简洁,用多个简
短的句子回答,不要用冗长的从句.给面试官一个好的印象.

11.屡试不行的童鞋请考虑 i.改简历 ii.monk interview iii不同type的职位.原则上
找能给你反馈的人的意见,反馈可以说是非常难得到的,是指引你进步的关键.monk
interview要涉及技术和behavior,而且面试人要认真准备,问题要有深度,当场要卡时间写
白板,不要觉得认识面试人就跳过诸如自我介绍,project描述等环节.

12.木桶原理,找到自己的不足,尽量提高综合能力,不要让任何不足拖你后腿.举个不一定合适
的例子,如果你是路痴,面试前多查查map要不就打出来,留一个紧急求助电话像是tax或者HR
的电话,一定要保证按时到达.路痴不能怪你,不守时那就是你的责任.现在公司招人,基本上都
是找综合能力强并且有亮点的人,弥补你的短板往往能更加有效的留下好印象.

13.列个表或者找个日历,合理安排面试顺序和集中程度,确保面试前对公司有所了解,尤其是要
看至少一遍job description,知道他们的需求是什么.这里废话一句,对于dream
company,最好不要放在面试经历少或者时间仓促的时候,如果你从来没去过onsite或者很久
没去过onsite,用其他公司热身是有必要的.一般一年左右的黑名单还是很难熬的,况且有些像
F这样的公司,2年前和现在的口气明显就不一样,现在连要个面试都很难.

14.学会换位思考,从面试官公司角度考虑他究竟在考察你什么.如果可以,你甚至也可以给别
人做interview,你会体会良多.crack technical interview里面说的比较夸张,面试官其
实在找"Would I have a beer with this guy?"的答案,但是你不可否认的是,他们如果觉
得和你在一起不舒服,随便找个借口就可以把你打发了.

15.面试过程中很多东西是无形的,面试前题目,公司招人的主要原因,老板性格态度,职位竞争
的激烈程度,面试评价标准等等,所以要尽可能的把握自己看的见的,技术要点,措辞态度,白板
编程等等.未知因素不由得你,不要老想着哪里有窍门哪里有小道消息来投机取巧;超越自己,把
握自己才是真正的提高.

16.这里再废话一段,面过这么多公司,感觉中国人还是比较受压迫,比起阿三,中国人在软件
公司中层的人数明显少,很少人接触到核心技术和做决策.准备面试的时候,要多与朋友交流
互相帮助,阿三老美的优点要尽量学来,以后职场做的久了,也要多帮助后生.我们的爷爷辈受
尽别人压迫欺负,父辈受自己人欺负,能提供机会我们出来,很不容易.我们这一代其实是肩负
着历史使命的,而在美国的各路精英应该是民族复兴的领军人,希望有朝一日中国能有更多的
AFGM为世界贡献优秀的软件.

话说到这里也差不多了,以上的话也都是小弟自己的体会,有不到位的地方也希望和大家讨论
切磋.关于详细的每家的面试题,还有比本版(尤其是精华贴)更好的地方么.最后用一句话与
大家共勉,机会青睐有准备的头脑,还等什么,继续打怪升级吧!
