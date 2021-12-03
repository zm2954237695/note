#### leetcode 394

#### [394. 字符串解码](https://leetcode-cn.com/problems/decode-string/)

难度中等937

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 *encoded_string* 正好重复 *k* 次。注意 *k* 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 *k* ，例如不会出现像 `3a` 或 `2[4]` 的输入。

 

**示例 1：**

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

**示例 2：**

```
输入：s = "3[a2[c]]"
输出："accaccacc"
```

**示例 3：**

```
输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

**示例 4：**

```wiki
输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

思路：因为本题可能出现多次括号嵌套的情况，比如 `2[a2[bc]]`，这种情况下我们可以先转化成 `2[abcbc]`，在转化成 `abcbcabcbc`。所以可以使用栈来保存每次的字符串状态。情况如下：

1. 数字，则把数字计算完加入栈中。
2. 左括号或者单个字母，直接入栈
3. 右括号。开始进行出栈操作，在这个过程不断记录栈中的字符串，直到遇到左括号就退出。然后左括号出栈，此时栈顶一定是数字，也就是现在的字符串的重复次数，可以对获得的字符串进行重复操作。最后把得到的字符串再次入栈继续进行处理。



#### code如下：

```c++
class Solution {
    string getDigits(string& s,int & ptr){
        string res="";
        while(isdigit(s[ptr])){
            res+=s[ptr++];
        }
        return res;
    }
    string getString(vector<string>&v){
        string ans;
        for(string &s:v){
            ans+=s;
        }
        return ans;
    }
public:
    string decodeString(string s) {
         vector<string>stk;
         int n=s.size();
         int ptr=0;
         while(ptr<n){
             char cur=s[ptr];
             if(isdigit(cur)){
                 string digit=getDigits(s,ptr);
                 stk.push_back(digit);
             }else if(isalpha(cur)||cur=='['){
                 stk.push_back(string(1,s[ptr++]));
             }else {
                 ++ptr;
                vector<string>tmp;
                while(stk.back()!="["){
                    tmp.push_back(stk.back());
                    stk.pop_back();
                }
                stk.pop_back();
                reverse(tmp.begin(),tmp.end());
                int nums = stoi(stk.back());
                stk.pop_back();
                string t,o=getString(tmp);
                while(nums--){
                    t+=o;
                }
               stk.push_back(t);
             }
         }
         return getString(stk);
    }
};
```



递归的代码如下：

```c++
class Solution {

     int ptr;
     int n;
     string s;
     int getDigits(){
         int ans=0;
         while(ptr<n&&isdigit(s[ptr])){
             ans=ans*10+s[ptr++]-'0';
         }
         return ans;
     }
     string getString(){
         if(ptr==n||s[ptr]==']'){
             return ""; //递归出口
         }
         char cur=s[ptr];
         string res;
         int time=1;
         if(isdigit(s[ptr])){
             int time=getDigits();
             ++ptr;
             string tmp = getString();
             ++ptr;
             while(time--)res+=tmp;
         }else if(isalpha(s[ptr])){
             res=string(1,s[ptr++]);
         }
         return res+getString();
     }
public:
    string decodeString(string s) {
         this->s=s;
         n=s.size();
         ptr=0;
         return getString();
    }
};
```



