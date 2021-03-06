###problem 87 Scramble String

[link](https://leetcode.com/problems/scramble-string/)


###方法

scrample string的定义，是指随意在一个字符串中找一个位置，把字符串分为两个子树，每个子树的值为分割的字符串值。此过程递归下去，直到每个叶节点都只有一个字母。这些叶节点从左往右组合，和原来的字符串相同。

现在要随机交换一颗非叶节点子树的左右子树，即不限定交换的是那颗子树，也不限定交换次数，每次交换后从左往右拼接叶子节点字母得到的字符串就称为原始字符串的scamble string .

一开始看到就懵，觉得根本无从下手。而且有一个疑问，这样的随机交换条件下，会不会构成无穷枚举的情况？

重重困惑下，一看题解，竟然思想这么简单！！而且就是这么简单！不过现在还是有些不相信——首先要说明，这样的交换，不能得到所有枚举的情况。因为如果可以得到所有枚举情况，那么只需判断两个字符串包含的字母是否一致就行了。答案似乎是显然的，因为毕竟不能这么简单啊。


1. 此种交换得到的字符串集合，是组成该字符串的字母随机排列构成的枚举字符串集合的子集。

    怎么去证明呢？最直接的想法就是写个程序分别枚举一下以及按此种方式交换，分别输出各自的数量。这样肯定是完美的，即使给不出证明，结论也是OK的。但是这么做也不是很简单啊。首先自己目前为止都没有写过枚举算法。其次在本题目的交换规则似乎全部枚举也不是那么简单。无奈放弃。

    何来决心自己枚举一下,选了长度为3的字符串，结果发现二者似乎是一致的？有些怀疑，决心去discuss看看。

    翻到最后一页，倒数第二个问题，真的有人提问了！楼下有人给出了一个反例：`bdac`与`abcd`，这两个是怎么都不能通过题目规则得到的，但是枚举显然能得到。

    为什么呢？ 不知啊...

2. 怎么做

    递归！注意到问题的定义就是递归的。

    暴力，枚举所有的可能。

    设字符串s1 , s2 , 长度均为N。如果s1与s2是scramble string , 那么满足：

    存在一个划分位置p(1 ~ N-1 ),将s1 分为 f1 ( [0 - p) ) , r1 ( [ p , N )) 两个子串 ；且满足划分位置p将s2分为f2 ( [0 , p) )和 r2( [p , N)) , f1 与 f2 , r1 与 r2 互为 scamble string 或者 划分从s2的尾部开始，将s2分为 sr2([0 , N - p )) 和 sf2 ( [ N-p , 0) ) , f1 与 sf2 , r1 与 sr2 互为scramble string.

    递归结束条件：当待比较的两个子串长度都为1时，返回 二者是否相等。

    上面的逻辑，完全是按照题目所给的规则来设计的... 

    这么看来，递归真的是太强大且直观的！ 

    奈何想不到啊...

3. 优化

    1. 剪枝

        如果两个待判断的字符串所包含的字符不一致，那么必然不是scramble string ， 可以剪枝。

        这样一条简单的规则可以省掉大量的递归分支！！ 

        而且为了消除递归调用的开销，在递归调用之前执行该检查！

    2. 动态规划

        似乎递归的东西就可以用动态规划来做...

        定义F[len\][i\][j\]表示s1中从i开始，s2中从j开始，且长度均为len的子串是否是scramble string 。

        递推关系与递归是一致的，不写了...

        初始化就是就是递归的结束条件，即len=1的值。

    3. 加cache

        这个与动态规划优化思想是一致的！

        动态规划可以优化重叠子问题，加cache也是为了快速返回前面已经算过的结果。

        将两个待判断的子串作为key，将其判断结果作为值。在递归调用前先看该判断是否已经完成。

        可以用map或者hash.

        可以与剪枝一起使用。

###代码

#### 递归+剪枝 (16ms)

    class Solution {
    public:
        bool isScramble(string s1, string s2) {
            if(s1.size() != s2.size()) return false ;
            return isScrambleRecursive(s1 , s2) ;
        }

    private:
        bool isEqualAfterSorted(string s1 , string s2)
        {
            sort(s1.begin() , s1.end()) ;
            sort(s2.begin() , s2.end()) ;
            return s1 == s2 ;
        }
        bool isScrambleRecursive(const string &s1 , const string &s2)
        {
            size_t len = s1.size() ;
            if( 1 == len ) return s1 == s2 ;
            for( size_t splitPos = 1 ; splitPos < len ; ++splitPos )
            {
                string frontS1 = s1.substr(0 , splitPos) ;
                string frontS2 = s2.substr(0 , splitPos) ;
                string swapedFrontS2 = s2.substr( len - splitPos) ;
                
                string rearS1 = s1.substr(splitPos) ;
                string rearS2 = s2.substr(splitPos) ;
                string swapedRearS2 = s2.substr(0 , len - splitPos) ;
                
                bool caseTest =  ( isEqualAfterSorted(frontS1 , frontS2) && isEqualAfterSorted(rearS1 , rearS2) && 
                         isScrambleRecursive(frontS1 , frontS2) && isScrambleRecursive(rearS1 , rearS2) ) ||
                       ( isEqualAfterSorted(frontS1 , swapedFrontS2) && isEqualAfterSorted(rearS1 , swapedRearS2) &&
                         isScrambleRecursive(frontS1 , swapedFrontS2) && isScrambleRecursive(rearS1 , swapedRearS2)
                        )  ;
                if(caseTest) return true ;
            }
            return false ;
            
        }
    };


### 3阶动态规划(196ms)

    class Solution {
    public:
        bool isScramble(string s1, string s2) {
            if(s1.size() != s2.size()) return false ;
            int len = s1.size() ;
            vector< vector< vector<bool> > > F(len + 1 , 
                                               vector<vector<bool>>(len , 
                                               vector<bool>(len , false))) ;
            // init
            for(size_t i = 0 ; i < len ; ++i)
                for(size_t j = 0 ; j < len ; ++j)
                    F[1][i][j] = ( s1[i] == s2[j] ) ;
            
            // transition
            for(size_t L = 2 ; L <= len ; ++L )
                for(size_t i = 0 ; i <= len - L ; ++i)
                    for(size_t j = 0 ; j <= len - L ; ++j)
                    {
                        bool isScramble = false ;
                        for(size_t k = 1 ; k < L ; ++k)
                        {
                            isScramble = ( F[k][i][j] && F[L-k][i+k][j+k] )  || ( F[k][i][j+L-k] && F[L-k][i+k][j]) ;
                            if(isScramble) break ;
                        }
                        F[L][i][j] = isScramble ;
                    }
            return F[len][0][0] ;
        }
    };

其中定义3阶vector真是要了命！完全不会了....

mark一下，

`vector<vector<vector<bool>>> F(10 , vector<vector<bool>(10 , vector<bool>(10 , false))>)`

没有等号！vector关键字加上类模板类型才是一个完整的类型！！

###其他

加cache的就没有再做了。

理论上，在递归上加cache效率应该有一个提升。

不过动态规划的结果较剪枝的递归差了好多啊...10倍了！当然，不剪枝是要TLE的（没有试过，看题解上这么说的...）

另，递归传递的是子串，题解上使用的是传迭代器，或者说索引，理论上应该效率更高，毕竟子串拷贝是要内存申请的！

另，剪枝判断使用的是sort，题解用的是count每个字母！这样有些风险（似乎没有说是小写字母！），不过开26*2的空间再count一遍，与sort的效率相比，究竟哪个更好呢？需要事实说话啊... 

-> 测试了一下，按照题解，只开26个字母的空间，只需要8ms的时间！！关键代码如下（只改了内容，没有改函数名）

    bool isEqualAfterSorted(string &s1 , string &s2)
    {
        int A[26] ;
        fill(begin(A) , end(A) , 0) ;
        for(auto i = s1.begin() ; i != s1.end() ; ++i) ++A[*i - 'a'] ;
        for(auto i = s2.begin() ; i != s2.end() ; ++i) --A[*i - 'a'] ;
        for(size_t i = 0 ; i < 26 ; ++i) if(A[i] != 0) return false ;
        return true ;
    }

相比之前，省了两次拷贝，两次sort，多了申请内存和count的时间，但是总时间竟然整整是原来的一半！即是性能提升100%！！不可思议啊。

再试试开26*2的情况，考虑大写字母的话，比较会多一个if，除去空间开销，速度也会有影响——结果是16ms... 又回来了... 把if判断去掉（单纯想看下是申请内存的开销还是if的开销），发现时间又到了8ms -> 这说明小内存申请开销可以不计，但是分支条件对于速度的影响还是比较明显的！

不过，毕竟只有8ms啊，不要产生强迫症。