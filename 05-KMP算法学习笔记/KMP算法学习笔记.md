### KMP算法学习笔记
#### KMP名字由来
KMP算法是由三位学者，D.E.Knuth、J.H.Morris和V.R.Pratt(其中Knuth和Pratt共同研究，Morris独立研究)发明的一个模式匹配算法，可以大大避免重复遍历的情况，我们把它称之为克努特-莫里斯-普拉特算法，简称KMP算法。
#### KMP算法应用
KMP主要应用在字符串匹配上。如leetcode第28题.实现strStr()。
KMP的主要思想是当出现字符串不匹配时，可以知道一部分之前已经匹配的文本内容，可以利用这些信息避免从头再去做匹配。
#### 什么是前缀表
我们写KMP的时候，都会创建一个next数组，这个next数组就是一个前缀表。前缀表是用来回退的，他记录了模式串与主串不匹配的时候，模式串应该从哪里开始重新匹配。前缀表的任务是当前位置匹配失败，找到之前已经匹配上的位置，再重新匹配，这也意味着在某个字符匹配失败时，前缀表会告诉你在下一步匹配中，模式串应该跳到哪个位置。
那么什么是前缀表：<font color = 'red'>记录下标i之前(包括i)的字符串中，有多大长度的相同前缀后缀。</font>
#### 如何计算前缀表
例如模式串为：aabaaf;

|下标：| 0 | 1 | 2 | 3 | 4 | 5 |
|---  | ---|---|---|---|---|---|
|模式串：|a|a|b|a|a|f|

长度为前一个字符的子串`a`，最长相同前后缀的长度为0。(注意字符串的前缀是指不包含最后一个字符的所有以第一个字符开头的连续子串；后缀是指不包含第一个字符的所有以最后一个字符结尾的连续子串)
长度为前两个字符的子串`aa`，最长相同前后缀的长度为1。
长度为前3个字符的子串`aab`，最长相同前后缀的长度为0。
长度为前4个字符的子串`aaba`，最长相同前后缀的长度为1。
长度为前5个字符的子串`aabaa`，最长相同前后缀的长度为2。
长度为前6个字符的字串`aabaaf`，最长相同前后缀的长度为0。
那么可以得到前缀表，如图：

|下标：| 0 | 1 | 2 | 3 | 4 | 5 |
|---  | ---|---|---|---|---|---|
|模式串：|a|a|b|a|a|f|
|前缀表：|0|1|0|1|2|0|

#### next数组
很多KMP算法的实现都是使用next数组来做回退操作，next数组就是前缀表，但是很多实现都是把前缀表统一减一（右移一位，初始位置为-1）之后作为next数组。其实这并不涉及到KMP的原理，而是具体实现，next数组既可以是前缀表，也可以是前缀表统一减一（右移一位，初始位置为-1）。
#### 前缀表统一减一代码实现(C++)
```JavaScript{.line-numbers}
class Solution {
public:
    void getNext(int* next, const string& s) {
        int j = -1;
        next[0] = j;

        for (int i = 1; i < s.size(); i++) {  // 注意i从1开始
            while (j >= 0 && s[i] != s[j + 1]) {  // 前后缀不相同了
                j = next[j];  // 向前回退
            }
            if (s[i] == s[j + 1]) {  // 找到相同的后缀
                j++;
            }
            next[i] = j;  // 将j(前缀的长度)赋给next[i]
        }
    }

    int strStr(string haystack, string needle) {
        if (needle.size() == 0) {
            return 0;
        }

        int next[needle.size()];
        getNext(next, needle);
        int j = -1;  // 因为next数组里记录的起始位置为-1
        for (int i = 0; i < haystack.size(); i++) {  // 注意i就从0开始
            while (j >= 0 && haystack[i] != needle[j + 1]) {  // 不匹配
                j = next[j];  // j寻找之前匹配的位置
            }

            if (haystack[i] == needle[j + 1]) {  // 匹配，j和i同时向后移动
                j++;  // i的增加在for循环里
            }

            if (j == (needle.size() - 1)) {  // 文本串s里出现了模式串t
                return (i - needle.size() + 1);
            }
        }

        return -1;
    }
};
```
#### 前缀表(不减一)实现(C++)
```JavaScript{.line-numbers}
class Solution {
public:
    void getNext(int* next, const string& s) {
        int j = 0;
        next[0] = j;

        for (int i = 1; i < s.size(); i++) {
            while (j > 0 && s[i] != s[j]) {  // j要保证大于0，因为下面有取j-1作为数组下标的操作
                j = next[j - 1];  // 注意这里，是要找前一位的对应的回退位置了
            }
            if (s[i] == s[j]) {
                j++;
            }
            next[i] = j;
        }
    }

    int strStr(string haystack, string needle) {
        if (needle.size() == 0) {
            return 0;
        }

        int next[needle.size()];
        getNext(next, needle);
        int j = 0;
        for (int i = 0; i < haystack.size(); i++) {
            while (j > 0 && haystack[i] != needle[j]) {
                j = next[j - 1];  
            }
            if (haystack[i] == needle[j]) {
                j++;
            }
            if (j == needle.size()) {
                return (i - needle.size() + 1);
            }
        }

        return -1;
    }
};
```
#### 时间复杂度分析
其中n为文本串长度，m为模式串长度，因为在匹配的过程中，根据前缀表不断调整匹配的位置，可以看出匹配的过程是O(n)，之前还要单独生成next数组，时间复杂度是O(m)。所以整个KMP算法的时间复杂度是O(n+m)的。
暴力解法显示易见是O(n*m)，因此KMP在字符串匹配中极大的提高了搜索的效率。



