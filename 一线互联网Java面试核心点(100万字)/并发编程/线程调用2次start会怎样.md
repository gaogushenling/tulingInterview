# 线程调用2次start会怎样

**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);"></font>**

**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">各种各样</font>****<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">五花八门的面试</font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">题让你</font>****<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">防不胜防</font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">！     今天一个5年经验的小伙伴去面试被问到：</font>**

**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">线程调用2次start</font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">会怎样，  直接就没答上来， </font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">这没做过啊</font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">！ </font>****<font style="color:#4C16B1;background-color:rgb(248, 248, 248);">谁有事没事调用2次start()</font>****<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">。</font>**

**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);"></font>**

**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">哈喽大家好！我是徐庶， 如果有打算面试的同学， 我这里有一份80万字面试题资料， 各种你没见过的面试题都总结在里面 ，需要的小伙伴在评论区扣666.</font>**

**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);"></font>**

<font style="color:rgb(37, 41, 51);">我们知道 ， 虽然很多八股文面试题中说</font>**<font style="color:rgb(37, 41, 51);">Java创建线程的方式有3种、4种，或者更多种</font>**<font style="color:rgb(37, 41, 51);">，但实际上他们</font>**<font style="color:#E4495B;">底层都是new Thread()</font>**

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">那当</font>**<font style="color:rgb(37, 41, 51);">创建完一个Thread</font>**<font style="color:rgb(37, 41, 51);">，这时线程处于</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">NEW状态</font>**<font style="color:rgb(37, 41, 51);">，那这时</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">调用start()</font>**<font style="color:rgb(37, 41, 51);">方法，会让线程</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">进入到RUNNABLE状态</font>**<font style="color:rgb(37, 41, 51);">。</font>

<font style="color:rgb(37, 41, 51);">那如果在</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">RUNNABLE的线程再次调用start呢</font>****<font style="color:rgb(37, 41, 51);">？其实就会</font>**<font style="color:rgb(37, 41, 51);"> </font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">线程状态的异常</font>**<font style="color:rgb(37, 41, 51);">。</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">那面试官接着问， </font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">为什么会报错呢</font>**<font style="color:rgb(37, 41, 51);">？ 这个时候很多人</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">想楱面试官</font>**<font style="color:rgb(37, 41, 51);">，  其实没什么这么多为什么，   就是底层源码在</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">调用start()</font>**<font style="color:rgb(37, 41, 51);">方法的时候</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);"> </font>**<font style="color:rgb(37, 41, 51);">针对状态</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">做了判断</font>**<font style="color:rgb(37, 41, 51);">， 如果</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">不是NEW状态就会</font>**<font style="color:rgb(37, 41, 51);">报线程状态的</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">异常</font>**<font style="color:rgb(37, 41, 51);">。</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">那面试官接着继续问！   如果</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">线程执行完，再调用一次start</font>**<font style="color:rgb(37, 41, 51);">又会怎么样</font>**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">？  那</font>**<font style="color:rgb(37, 41, 51);">线程</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">运行完会进入TERMINATED</font>**<font style="color:rgb(37, 41, 51);">状态</font>**<font style="color:rgb(102, 102, 102);background-color:rgb(248, 248, 248);">！   也不是NEW状态！ </font>****<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">所以肯定也是报错啊！</font>**

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">所以这个面试题说白了就是</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">另一种线程状态的考核方式</font>**<font style="color:rgb(37, 41, 51);">， 你要知道</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">线程有几种状态</font>**<font style="color:rgb(37, 41, 51);">， 只要知道线程只能是</font>**<font style="color:#DF2A3F;background-color:rgb(248, 248, 248);">刚NEW 出来的才可以start()</font>**<font style="color:rgb(37, 41, 51);">;   记住了没有！   </font>

<font style="color:rgb(37, 41, 51);">如果看完有帮助那还是得给徐庶老师一个三连哈，  这样我才有继续更新的动力！</font>



> 更新: 2024-06-03 15:51:16  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/nzzn4r5i0q20gemp>