#MapReduce
##问题一描述
1.计算数组的平方和，如[1,2,3,4]的平方和为12+22+32+42。
2.统计数组中正数的个数，如[-1,1,0,-2,5]的正数个数为2。
3.展平数组的数组，如[[1,2],[3,4,5],[6,[7]]]展平后为[1,2,3,4,5,6,7]
##思路：
map操作作用与一个列表时，只需将函数功能作用与列表中每一个元素，再返回新的列表即可。
reduce操作则是对列表中元素进行累积，第一个和第二个数作用的结果再与第三个数作用，以此类推返回最后的结果。如果列表只有一个元素则直接返回。
##函数代码
```
def MyMap(func,array):
    for i in range(len(array)):
        array[i]=func(array[i]) 
    return array
def MyReduce(func,array):
    if len(array)<=1:
        return
    r=array[0]
    for i in range(1,len(array)):
        r=func(r,array[i])
    return r
```
##结果
对于问题1，先将原列表所有数平方，再reduce求和
对于问题2，map对每一个数判断是否大于0，大于则计数
对于问题3，reduce将内层列表两两相加，最终规约为一个列表
```
l=[2,3,4]
print('原列表:',l)
print('新列表:')
print(MyMap(lambda x:x**2,l))
print('平方和为：')
print(MyReduce(lambda x,y:x+y,l))
print()

print('列表：',l)
l=[-1,1,0,-2,5] 
# new_l=[i for i in l if i>0]
# print(new_l)
# print(MyMap(lambda x:x>0,l))
count=0
for i in MyMap(lambda x:x>0,l):
    if i:
        count=count+1
print('正数个数：',count)
# def AddList(x,y)

print()
list =[[1,2,3],[4,5,6], [7], [8,9]]
print('原列表：',list)
print('展开：')
print(MyReduce(lambda x,y:x+y,list))

```
![](imgs/20190414-230839.png)

##问题二描述
扩展map和reduce，使得其能够作用到二叉树结构。只能作用到叶子节点。
为下列通讯录加上区号，并统计深圳电话的个数：
![](imgs/20190414-214241.png)
##思路
无论是何种数据结构，map和reduce都将对该数据结构中所有元素进行遍历。所以问题一和二的差距仅仅在于遍历方式不同，但是只要能实现遍历就同样能实现map和reduce。
##代码
以对叶子节点加区号为例：AddAreaCode函数定义了如何对一个叶子节点进行操作，MapTree函数采用前序遍历的方式：
if 当前节点是叶子结点，加区号
if 当前结点的左孩子非空，对左孩子Maptree
if 当前结点的右孩子非空，对右孩子maptree
这样便实现了对树的遍历操作，从而对所有的叶子结点加区号操作
```
class Node:
    def __init__(self):
        self.left= None
        self.right= None
        self.value= {}

def generate_binary_tree(header):
    R2 = Node()
    R3 = Node()
    D1 = Node()
    D2 = Node()
    D3 = Node()
    D4 = Node()
    D1.value = {'N':'小明','A':'深圳','T':'26530001'}
    D2.value = {'N':'小王','A':'广州','T':'26530002'}
    D3.value = {'N':'小丽','A':'深圳','T':'26530003'}
    D4.value = {'N':'小红','A':'北京','T':'26530004'}

    header.left = D1
    header.right = R2
    R2.left=D2
    R2.right=R3
    R3.left=D3
    R3.right=D4
    

# 前序遍历
def preorder_traversal(header):
    if header.value:
        print(header.value,',',end='')
    if header.left is not None:
        preorder_traversal(header.left)
    if header.right is not None:
        preorder_traversal(header.right)
        
        
def AddAreaCode(Node):
    if not Node.value:
        return
    if Node.value['A']=='深圳':
        Node.value['T']='755'+Node.value['T']
    elif Node.value['A']=='广州':
        Node.value['T']='020'+Node.value['T']
    elif Node.value['A']=='北京':
        Node.value['T']='010'+Node.value['T']
    else:
        pass

        
def MapTree(func,header):
    if header.value:
        AddAreaCode(header)
    if header.left is not None:
        MapTree(func,header.left)
    if header.right is not None:
        MapTree(func,header.right)
        
def getleaf(header):
    if header.value:
        yield header.value
    if header.left is not None:
        for subitem in getleaf(header.left):
            yield subitem
    if header.right is not None:
        for subitem in getleaf(header.right):
            yield subitem

def MyMap(func,array):
    for i in range(len(array)):
        array[i]=func(array[i]) 
    return array 

def MyReduce(func,array):
    if len(array)<=1:
        return
    r=array[0]
    for i in range(1,len(array)):
        r=func(r,array[i])
    return r

# 执行
R1 = Node()
generate_binary_tree(R1)
print('前序遍历')
preorder_traversal(R1)

print('\n加区号')
MapTree(AddAreaCode(R1),R1)
preorder_traversal(R1)

print('\n统计深圳')
l=[]
for n in getleaf(R1):
    l.append(n)
count=0
for i in MyMap(lambda x:x['A']=='深圳',l):
    if i:
        count=count+1
print(count)
```
##结果
![](imgs/20190414-215448.png)