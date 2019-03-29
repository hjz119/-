#python全排列
##问题描述
输入一个字符串,打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。 去除重复：如输入aac，打印结果：aac,aca,caa
##递归实现
###思路
无论给定的字符串长度多少，其排列出来的组合样式均可以分解为“第一个字符+剩下的字符”的样式。可以通过遍历分别赋予第一位上不同的字符，那“剩下的字符”又可以如上分解。
###代码
```
import time
def Permutation(ss):
    list = []
    if len(ss) <= 1:
        return ss
    for i in range(len(ss)):
        for j in map(lambda x: ss[i]+x, Permutation(ss[:i]+ss[i+1:])):
            if j not in list:
                list.append(j)
    return list

start=time.process_time()
print(Permutation('abc'))
end=time.process_time()
print("CPUtime:",end-start)

start=time.process_time()
print(Permutation('aac'))
end=time.process_time()
print("CPUtime:",end-start)
```
过程：Permutation(ss)返回ss全排列的列表
输入abc,ss[i]=a,x=Permutation(bc)=bc,cb，返回abc,acb
输入bc，ss[i]=b,x=Permutaion(c)=c，返回bc
输入c，返回c
注意map返回一个序列，针对x中每一个元素做操作，序列中元素值为ss[i]+x
###结果
![](imgs/20190329-133048.png)
##非递归实现（字典序法）
###思路
对于每一种可能排列的情况，我们都想办法使其与某种顺序建立对应关系，这种关系是一一对应的，这样我们就能通过遍历得到的某种顺序来生成全排列，这样就能避免递归过程了。这种按照某种顺序来生成全排列的方法就被称为是字典序。
要找到一种顺序关系，我们就首先要定义大小关系，对于两个序列{0,2,1,3}和{0,2,3,1}来说，序列{0,2,3,1}要比{0,2,1,3}大，比较的方法是从前到后依次比较相同位置上的元素，如果相同则继续比较下一个元素，直到遇到一个不同的元素，元素值大的序列就大于元素值小的序列。按照这样的大小关系形成的序列的顺序，就是字典序。可以看到，最小的序列一定是{0,1,2,3}，最大的序列是{3,2,1,0}。而字典序算法就是从字典序中最小的序列开始，一直不停寻找下一个仅比上一个序列大的序列，直到到达最大的序列。

现在的问题就变成了，如何从当前状态生成一下个状态？

**字典序算法过程**(假设当前排列是a[1⋯n]):

1.从a中找到满足a[k]<a[k+1]的k的最大值，如果不存在这样的k，那就是说已经达到字典序最大的序列了。

2.从a[k+1⋯n]中寻找比a[k]大的数中的最小数a[j])

3.交换a[k]和a[j]，并将a[k+1⋯n]中的元素全部倒序。

经过上述三步，得到的序列就是a[1⋯n]在字典序中的下一个序列了。

**例如:**
起点：字典序最小的排列，例如122345。起点为正序 
终点：字典序最大的排列，例如543221。终点为倒序 
过程：按当前的排列找出刚好比它大的下一个排列 
如：524321的下一个排列是531224 
如何计算？ 
我们从后向前找第一双相邻的递增数字，”21”、”32”、”43”都是非递增的，”24”即满足要求，称前一个数字2为替换数，替换数的下标称为替换点，再从后面找一个比替换数大的最小数（这个数必然存在），1、2都不行，3可以，将3和2交换得到”534221”，然后再将替换点后的字符串”4221”颠倒即得到”531224”。 
对于像”543221”这种已经是最“大”的排列，返回false。 
###代码
```
def nextstate(arg):
    flag = False
    #步骤1
    for i in range(len(arg)-2,-1,-1):#下标从n-2到0的一组数字
        if(arg[i] < arg[i+1]):
            flag = True
            break
    if flag:
        k = i
    else:
        return False

    #步骤2
    for i in range(len(arg)-1,k,-1):#从n-1到k+1
        if arg[i] > arg[k]:
            break
    j = i
    #步骤3
    arg[j],arg[k] = arg[k],arg[j]
    t = arg[k+1:]
    t.reverse()
    arg[k+1:] = t
    return True


def dictgenerate(arg):
    myarg = list(range(len(arg)))
    order = []
    t = ''
    for i in myarg:
        t+=arg[i]
    order.append(t)
    while True:
        t = ''
        flag = nextstate(myarg)
        if flag == False:
            break
        for i in myarg:
            t+=arg[i]
        if t not in order:
            order.append(t)
    return order

start=time.process_time()
print(dictgenerate('abc'))
end=time.process_time()
print("CPUtime:",end-start)

start=time.process_time()
print(dictgenerate('aac'))
end=time.process_time()
print("CPUtime:",end-start)
```
###结果
![](imgs/20190329-134340.png)

##结果分析
###效率
结果似乎显示递归的算法和字典序的算法效率上相差不大，甚至有时候递归比字典序还快。但是出现这样的现象不是算法的问题，而是Python语言的问题，Python在很多方面都有着非常不错的特点，但是在运行效率上却不是很高(相对来说它的开发效率很高)，因此会出现递归和字典序效率差不多的现象。
###可读性和可写性
递归的代码更简洁清晰，更容易理解，可读性和可写性比迭代要好。
###可靠性
可靠性迭代更好。递归调用函数，浪费空间；递归太深容易造成堆栈溢出。而迭代没有额外开销，空间上也没什么增加。