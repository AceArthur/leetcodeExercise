# problem 292. Nim Game

[题目链接](https://leetcode.com/problems/nim-game/)

## 方法

题目里说一旦剩4个子，那么你肯定输了。然后尝试递推了下，发现如果剩5、6、7个子的话，都可以拿掉1、2、3个石子使得桌子上只剩4个子，这样你就肯定赢了。而如果8个石子的话，那么不管你怎么拿，都会剩7、6、5个石子，而这种情况下根据前面的推理，对手肯定赢了。

一下子就变得很简单了！！这么一个递推过程，最后的结果是，如果你走的时候，石子数是4个倍数，那么肯定你完蛋了；否则你就赢了，赢的关键是把石子数控制着不让它成为4的倍数。最后再测验了下4下面的1、2、3的情况，没有问题。

如果人人都会MAX，那么或许所有的游戏就只跟先手顺序、游戏规则相关了。

## 代码

```C++
class Solution {
public:
    bool canWinNim(int n) {
        return n % 4 != 0;
    }
};
```