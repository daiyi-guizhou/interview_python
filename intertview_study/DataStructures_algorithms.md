<!-- TOC -->

- [排序](#排序)
    - [插入](#插入)
    - [选择](#选择)
    - [冒泡](#冒泡)
    - [快速排序](#快速排序)
    - [归并排序](#归并排序)
    - [基数排序](#基数排序)
        - [桶排序](#桶排序)
        - [链式基数排序](#链式基数排序)
    - [希尔排序](#希尔排序)
- [算法](#算法)
    - [常用算法](#常用算法)
        - [穷举法 - 又称为暴力破解法，对所有的可能性进行验证，直到找到正确答案。](#穷举法---又称为暴力破解法对所有的可能性进行验证直到找到正确答案)
        - [贪婪法 - 在对问题求解时，总是做出在当前看来最好的选择，不追求最优解，快速找到满意解。](#贪婪法---在对问题求解时总是做出在当前看来最好的选择不追求最优解快速找到满意解)
        - [分治法](#分治法)
        - [回溯法](#回溯法)
        - [动态规划](#动态规划)
- [递归函数](#递归函数)
    - [累加， 累乘     （n+1）  （n-1）](#累加-累乘-----n1--n-1)

<!-- /TOC -->

# 排序
https://blog.csdn.net/qq_41427568/article/details/89217536

## 插入
	将线性表看作有序列和无序列的两部分，有序列子表是a[0:i-1]，无序列子表是a[i:n-1]，排序过程中每次从无序列子表中取出一个元素，将这个元照大小插入有序列子表中的正确位置。

## 选择
	选择排序仍然是将线性表看成有序和无序两个部分，其中有序子表是a[0:i-1]，无序子表是a[i:n-1]。排序过程中每次从无序子表中挑选出最小的一素，将其添加到有序子表的末尾，有序子表保持有序状态并长度加1，无序子表长度减小1，重复这个过程直到无序子表为空。

## 冒泡
	把数组a[n]中的n个元素看成一个有序表和一个无序表，与前两种排序不同，冒泡排序的有序表部分处于表的右端，而插入和选择排序堵塞有序表在表侧。开始时，有序表中没有元素，无序表中有n个元素。当进行排序的时候，将无序表中前后相邻元素（a[j]和a[j+1]）逐个比较，如果满足大于或者条件就将两个元素进行交换，这样可以从选出无序表中最大or最小的元素放在无序表最后一个位置。最后将无序表的长度减一，有序表的长度加一，再无序表中的逐个比较，直到无序表长度为0，有序表长度为n为止

## 快速排序
	介绍完上面最基本的三种排序算法，接下来介绍目前一种实测平均速度最快的排序算法：快速排序，它是在冒泡排序的基础上进行改进的算法。
	快速排序基本思想：
	通过一趟划分将要排序序列分割成独立的三个部分，即为左部、基准值、右部三部分。其中，左部的所有数据都比基准值要小，右部的所有数据都比基大。经过这样一趟划分，就将数据分为两部分，且有初步的顺序。然后再对左部和右部进行划分，对左右部划分后的四个区间再进行左右划分，直到不划分为止。整个过程可以用递归实现。
	举例
	原始数据：49 38 65 97 76 13 27（以49为基准值进行一趟划分）
	一趟划分的结果：27 38 13 49 76 97 65（分别取27和76作为左右部的基准值对左右部进行划分）
	二趟划分的结果：13 27 38 49 65 76 97
	可以看到，每次根据基准值划分的步骤
	1、先向前搜索小于pivotkey（基准值）的值，将这个值放在前面，然后空出这个值的位置。
	2、然后从前面开始搜索大于pivotkey的值，放在刚刚空出那个值的位置上。
	3、然后再从后面向前搜索，开始重复1、2步骤。

## 归并排序
5.归并排序

先贴个我写的递归函数。要理解归并，感觉递归熟了之后还是很好理解的，也可以借着学习递归巩固下对递归的认识。
https://blog.csdn.net/qq_41239584/article/details/82253771
归并排序 就像这个名字一样，把一个个列表合并在一起。怎么来合并呢，简单来说就是先拆后拼。
1.先将一个列表分成两个，两个分四个 分到不能再分，每一个列表都只有一个元素
2.再将列表一级一级的有序的合并起来，4个一个元素的列表变成 2个有序的2元素列表 再变成一个有序的4元素列表
举个例子 列表[4,8,9,3,6,5,2,1]
1.将列表从中分开分成
[4,8,9,3] [6,5,2,1]
2.接着分
[4,8] [9,3] [6,5] [2,1] （如果奇数个数字就2,2,2,3分没事）
3.分到最简
[4] [8] [9] [3] [6] [5] [2] [1]
3.最简后开始合并,按照分解的步骤合并，但有序的合并。
[4,8] [3,9] [5,6] [1,2] (第2点怎么分现在怎么合，但列表数要排序)
4.按第一部分解的合并
[3,4,8,9] [1,2,5,6]
5.最后两个列表合并 小的放前面
假设创建一个新列表A[ ]空集合
2个列表都取第一个数开始比，1和3先比1小 先丢进去——A[1]
右边列表的指针往后移 指向2，2和3比较 2小 丢进去——A[1,2]
右边列表的指针接着后移，指向5，5和3比较 3小 ——A[1,2,3]
左边列表的指针向后移动，指向4, 4和5比较 4小 ——A[1,2,3,4]
左边列表的指针向后移动，指向8, 8和5比较 5小 ——A[1,2,3,4,5]
右边列表的指针接着后移，指向6，6和8比较 6小 ——A[1,2,3,4,5]
最后右边列表空了，左边列表还剩[8,9] 拼在A列表后————A[1,2,3,4,5,6,8,9]
整个过程结束
其实整个过程 就是两个东西 一直拆，选一个中点拆成两边，拆到不能拆 。有没有很递归
接着一直比较，然后合并1合2 2合4 4合8 比较到数都比完 合并到没得合
上代码
			
原文：https://blog.csdn.net/qq_41239584/article/details/82193879 

```
# include <iostream>

# define N 10

using namespace std;

void Output(int* a){
    for(int i=0;i<N;i++)
        cout<<a[i]<<" ";
    cout<<"\n";
}

//合并算法，将a[l:m]和a[m+1:h]合并到b数组上
void Merge(int a[],int b[],int l,int m,int h){
    int i,j,k;
    i=l;
    j=m+1;
    k=l;
    while(i<=m&&j<=h){
        if(a[i]<a[j]){
            b[k]=a[i];
            k++;
            i++;
        }
        else{
            b[k]=a[j];
            k++;
            j++;
        }
    }
    while(i<=m){
        b[k]=a[i];
        k++;
        i++;
    }
    while(j<=h){
        b[k]=a[j];
        k++;
        j++;
    }
}

void MergeSortByRecu(int Q[],int P[],int s,int t){
    cout<<s<<" "<<t<<"\n";
    //对a[s:t]进行归并排序，将排序后的记录存入b[s:t]
    int T[N];  //T数组来存储归并排序中，中间结果的辅助空间
    if(s==t){
      P[s]=Q[s];
    }
    else if(s<t){
        int m=(s+t)/2;               //将a[s:t]分为a[s:m]和a[m+1:t]
        //int temp=m+1;
        cout<<"m="<<m<<"s="<<s<<"\n";
        MergeSortByRecu(Q,T,s,m);//递归，将a[s:m]归并为有序的T[s:m]
        MergeSortByRecu(Q,T,m+1,t);//递归，将a[m+1:t]归并为有序的T[m+1,t]
        Merge(T,P,s,m,t);          //将T[s:m]和T[m+1:t]的结果归并到P[s:t]
    }
}

int main()
{
    int a[N]={1,2,4,43,11,456,1982,9,10,167};
    //b数组来存储排序后的数组
    int b[N];
    MergeSortByRecu(a,b,0,N-1);
    Output(b);
    return 0;
}
```

## 基数排序

### 桶排序     
根据关键字来 进行排序。 

### 链式基数排序
				链式基数排序是利用桶排序思想进行的排序算法。
举个例子：考虑十进制（基数为10）的数组D，数组D={123,42,453,45,654,76,432,75,3,99}
数组D中关键字对多为3位，不足3位的补0其变成3位，结果如下：
D={123,042,453,045,654,076,432,075,003,099}
下面那我们就用桶排序的思想对D进行排序：

    由于基数排序的基数是10，所以每一位数字的取值范围应该是0-9
    对关键字的每一位都可以用桶排序（10个桶，编号是0-9）
    百位数的关键字有三位，应该使用三次桶排序
    应该先从最低位（个位）开始排序，然后是十位，最后对百位排序，这称为最低位优先。

第一趟桶排序结果（对个位排序）：
042, 432, 123, 453, 003, 654,045, 075, 076, 099
第二趟桶排序结果（对十位排序）：
003, 123, 432, 042, 045, 453, 654, 075, 076, 099
第三趟桶排序结果（对百位排序）：
003, 042 ,045, 075, 076, 099, 123, 432, 453,654
可见第三趟桶排序过后数组已经排序完成。
		
        
## 希尔排序

希尔排序是插入排序的一种
	https://blog.csdn.net/m0_37345402/article/details/79504969

# 算法
算法：解决问题的方法和步骤

评价算法的好坏：渐近时间复杂度和渐近空间复杂度。

渐近时间复杂度的大O标记：

    - 常量时间复杂度 - 布隆过滤器 / 哈希存储
    - 对数时间复杂度 - 折半查找（二分查找）
    - 线性时间复杂度 - 顺序查找 / 桶排序
    - 对数线性时间复杂度 - 高级排序算法（归并排序、快速排序）
    - 平方时间复杂度 - 简单排序算法（选择排序、插入排序、冒泡排序）
    - 立方时间复杂度 - Floyd算法 / 矩阵乘法运算
    - 几何级数时间复杂度 - 汉诺塔
    - 阶乘时间复杂度 - 旅行经销商问题 - NP

			图片
				
		https://github.com/jackfrued/Python-100-Days/blob/master/Day16-20/16.Python%E8%AF%AD%E8%A8%80%E8%BF%9B%E9%98%B6.md

## 常用算法

### 穷举法 - 又称为暴力破解法，对所有的可能性进行验证，直到找到正确答案。

### 贪婪法 - 在对问题求解时，总是做出在当前看来最好的选择，不追求最优解，快速找到满意解。
按照 性价比 最高的 来排序， 之后  快速找到最优解
					假设小偷有一个背包，最多能装20公斤赃物，他闯入一户人家，发现如下表所示的物品。很显然，他不能把所有物品都装进背包，所以必须确定拿走哪些物品，留下哪些物品。
名称 	价格（美元） 	重量（kg）
电脑 	200 	20
收音机 	20 	4
钟 	175 	10
花瓶 	50 	2
书 	10 	1
油画 	90 	9

"""
贪婪法：在对问题求解时，总是做出在当前看来是最好的选择，不追求最优解，快速找到满意解。
输入：
20 6
电脑 200 20
收音机 20 4
钟 175 10
花瓶 50 2
书 10 1
油画 90 9
"""
```
class Thing(object):
    """物品"""

    def __init__(self, name, price, weight):
        self.name = name
        self.price = price
        self.weight = weight

    @property
    def value(self):
        """价格重量比"""
        return self.price / self.weight


def input_thing():
    """输入物品信息"""
    name_str, price_str, weight_str = input().split()
    return name_str, int(price_str), int(weight_str)


def main():
    """主函数"""
    max_weight, num_of_things = map(int, input().split())
    all_things = []
    for _ in range(num_of_things):
        all_things.append(Thing(*input_thing()))
    all_things.sort(key=lambda x: x.value, reverse=True)
    total_weight = 0
    total_price = 0
    for thing in all_things:
        if total_weight + thing.weight <= max_weight:
            print(f'小偷拿走了{thing.name}')
            total_weight += thing.weight
            total_price += thing.price
    print(f'总价值: {total_price}美元')


if __name__ == '__main__':
    main()
```	    

### 分治法 
- 把一个复杂的问题分成两个或更多的相同或相似的子问题，再把子问题分成更小的子问题，直到可以直接求解的程度，最后将子问题的解进行合并得到原问题的解。

### 回溯法 
- 回溯法又称为试探法，按选优条件向前搜索，当搜索到某一步发现原先选择并不优或达不到目标时，就退回一步重新选择。

### 动态规划 
- 基本思想也是将待求解问题分解成若干个子问题，先求解并保存这些子问题的解，避免产生大量的重复运算。
				动态规划原理

虽然已经用动态规划方法解决了上面两个问题，但是大家可能还跟我一样并不知道什么时候要用到动态规划。总结一下上面的斐波拉契数列和钢条切割问题，发现两个问题都涉及到了重叠子问题，和最优子结构。

①最优子结构

用动态规划求解最优化问题的第一步就是刻画最优解的结构，如果一个问题的解结构包含其子问题的最优解，就称此问题具有最优子结构性质。因此，某个问题是否适合应用动态规划算法，它是否具有最优子结构性质是一个很好的线索。使用动态规划算法时，用子问题的最优解来构造原问题的最优解。因此必须考查最优解中用到的所有子问题。

②重叠子问题

在斐波拉契数列和钢条切割结构图中，可以看到大量的重叠子问题，比如说在求fib（6）的时候，fib（2）被调用了5次，在求cut（4）的时候cut（0）被调用了4次。如果使用递归算法的时候会反复的求解相同的子问题，不停的调用函数，而不是生成新的子问题。如果递归算法反复求解相同的子问题，就称为具有重叠子问题（overlapping subproblems）性质。在动态规划算法中使用数组来保存子问题的解，这样子问题多次求解的时候可以直接查表不用调用函数递归。

# 递归函数
		递归函数中必须有两个条件。像if n==0 称为基线条件，用来让递归函数停止。else则是递归条件，后面接递归的内容。
```
def recursive_test(n):
    print('数字:',n)
    if n==0:
        print('结束')
    else:
        recursive_test(n-1)

recursive_test(5)
		https://blog.csdn.net/qq_41239584/article/details/82253771
		5！=5*4*3*2*1 

1 + 2+3+4+5+6    
```

## 累加， 累乘     （n+1）  （n-1）  