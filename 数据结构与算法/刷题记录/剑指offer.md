## 1.数组中重复的数字

https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/

利用题目中n个数在0~n-1范围内的特性，尝试将每个数i放在nums[i]的位置上，放之前与nums[i]处的元素比较，若相等则发现了重复数字。具体做法：遍历数组，若nums[i]==i，则该数已经放在了正确位置，继续遍历；否则，将nums[i]与nums[nums[i]]相比较，即将当前数与它本应该在的位置的数相比较，若相等则发现重复数字，返回，否则将两数互换，将原本的nums[i]放在正确位置，这样直到最后一定会找到某数与它本应该在的位置的数相等，从而发现了重复数字。

```C++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        if(nums.size()<=1)return -1;

        for(int i=0;i<nums.size();){          
            if(nums[i]!=i){
                if(nums[i]==nums[nums[i]])return nums[i];
                else swap(nums[i],nums[nums[i]]);
            }
            else i++;
        }
        return -1;
    }
};
```

## 2.二维数组中查找

https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/

利用矩阵行列皆有序的特性，从矩阵的左下角或右上角元素开始比较（代码中选择左下角），若比目标大，便可排除掉一行（因为该元素已经是该行最小的元素了），同理，若比目标小便可排除掉一列，直至找到目标元素。

```c++
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if(matrix.size()==0||matrix[0].size()==0)return false;

        int n=matrix.size(),m=matrix[0].size(); //保存n和m
        int row=matrix.size()-1,column=0;   //从左下角数字开始比较

        while(row>=0&&column<=m-1){
            if(target==matrix[row][column])return true;

            if(target>matrix[row][column]){  
                column++;
            }
            else
                row--;
        }

        return false;
    }
};
```

## 3.替换空格

https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/

这一题考察字符替换的思路，如果从前往后替换，则每个空格都要将后面的元素进行移动，有些元素会被移动多次。正确的思路是从后往前用双指针，首先遍历字符串求出空格个数，每个空格会使得字符串长度增加2，将字符串扩展到最终的长度后，指针p1指向原字符串末尾，p2指向扩展后的最后一个字符位置，若p1不是空格，则直接复制到p2，否则在p2处写入要替换的三个字符。

```c++
class Solution {
public:
    string replaceSpace(string s) {
        if(s.length()==0)return s;

        int Blank_Count=0,len=s.length();
        for(auto &c:s){
            if(c==' ')Blank_Count++;   //统计空格数目
        }
        int AddLength=Blank_Count*2;
        for(int i=0;i<AddLength;i++){
            s.push_back(' ');          //string扩容
        }

        int p1=len-1,p2=s.length()-1;
        while(p1>=0){
            if(s[p1]!=' '){
                s[p2]=s[p1];
                p1--;p2--;
            }
            else{
                s[p2--]='0';
                s[p2--]='2';
                s[p2--]='%';
                p1--;
            }
        }
        return s;
    }
};
```

## 4.逆向打印链表

https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/

像这种涉及逆向的题目一般都想到栈或是递归（本质还是栈），将链表顺序存入栈后全部出栈即可得到逆向序列。btw，C++中的Vector尽量使用emplace_back而不是push_back，性能更好。

```c++
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        if(head==NULL)return {};
        vector<int> res;
        stack<int> stk;
        for(ListNode *p=head;p!=NULL;p=p->next){
            stk.push(p->val);
        }
        while(!stk.empty()){
            res.emplace_back(stk.top());
            stk.pop();
        }
        return res;
    }
};
```

## 5.重建二叉树(C#)

https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/

建树的题目一般递归比较好做，利用前序序列和中序序列的特性，根结点在前序序列第一个，找到根结点在中序序列的位置后，中序序列中根结点左边的元素对应左子树，右边的元素对应右子树，即可得左右子树的结点数量，在前序序列中根结点后面紧跟的即是左子树的元素，然后是右子树的元素，递归创建即可。

```c#
public class Solution {
    public TreeNode BuildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.Length == 0) return null;
        TreeNode Root = BuildSubTree(preorder,inorder,0,preorder.Length-1,0,inorder.Length-1);
        return Root;
    }

    TreeNode BuildSubTree(int[] preorder, int[] inorder, int s1, int e1, int s2, int e2)
    {
        if (s1 > e1) return null;
        if (s1 == e1) return new TreeNode(preorder[s1]); 
        int RootVal = preorder[s1];
        int RootIndex = 0;     //根在中序序列的位置
        for (int i = s2; i <= e2; i++)
        {
            if (inorder[i] == RootVal){
                RootIndex = i;
                break;
            }
        }
        int leftTreeCnt = RootIndex - s2;  //得到左子树的结点数
        TreeNode Root = new TreeNode(inorder[RootIndex]);
        //递归创建左子树和右子树
        Root.left = BuildSubTree(preorder, inorder, s1 + 1, s1 + leftTreeCnt, s2, RootIndex - 1);
        Root.right = BuildSubTree(preorder, inorder, s1 + leftTreeCnt + 1, e1, RootIndex + 1, e2);

        return Root;
    }
}
```

## 6.用两个栈实现队列(C#)

https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/

入队时直接入第一个栈，出队时需要借助第二个栈，由于本应出队的元素被压在第一个栈的栈底，所以将第一个栈全部出栈并压入第二个栈，这样第二个栈的栈顶即是队头元素，要出队时直接从第二个栈出栈即可。此时第二个栈的出栈顺序即是队列的出队顺序，当第二个栈有元素时直接出栈，没元素就把第一个栈元素压入第二个栈。

```c#
public class CQueue {
    public Stack<int> s1;
    public Stack<int> s2;
    public CQueue() {
        s1=new Stack<int>();
        s2=new Stack<int>();
    }
    
    public void AppendTail(int value) {
        s1.Push(value);
    }
    
    public int DeleteHead() {
        if(s2.Count>0){
            return s2.Pop();
        }
        else{
            if(s1.Count==0)return -1;
            while(s1.Count>0){
                s2.Push(s1.Pop());
            }
            return s2.Pop();
        }
    }
}
```

## 7.斐波那契数列

https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/

经典入门DP

```c++
class Solution {
public:
    int fib(int n) {
        if(n==1||n==0)return n;
        int pre1=0,pre2=1,cur=1;
        for(int i=2;i<=n;i++){
            cur=(pre1+pre2)%1000000007;
            pre1=pre2;
            pre2=cur;
        }
        return cur;
    }
};
```



## 8.旋转数组的最小数字

https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/

二分法判断旋转点在中点的左边还是右边，这里有两个点需要注意，首先为什么left=mid+1而right=mid?因为我们要找最小的元素，如果`numbers[mid]<numbers[right]`，则mid有可能是最小元素，此时无法把mid排除，所以令right=mid；而如果`numbers[mid]>numbers[right]`，那此时mid一定不是最小元素，可以排除，所以令left=mid+1。其次为什么`numbers[mid]=numbers[right]`时可以令right--?因为如果right是最小元素，那mid也是，所以去掉right也无所谓，相当于通过暴力法缩小范围。

```c++
class Solution {
public:
    int minArray(vector<int>& numbers) {
        if(numbers.size()==1)return numbers[0];

        int left=0,right=numbers.size()-1;
        while(left<right){
            int mid=left+(right-left)/2;
            if(numbers[mid]>numbers[right]) //旋转点在右边
                left=mid+1;
            else if(numbers[mid]<numbers[right]) //旋转点在左边
                right=mid;
            else //无法判断旋转点在哪边，排除掉right
                right--;
        }
        return numbers[left];
    }
};
```

## 9.矩阵中的路径

https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/

比较基础的DFS，套模板即可，但是看leetcode题解学了一招，以前一般是要开一个和矩阵一样大的visited数组来判断当前位置有没有被访问过，下面代码中在继续递归之前将当前位置的字符置为'\0'，递归返回后再改回来，然后通过当前位置字符是否等于word[k]来判断，省略了visited数组的空间，非常巧妙。

```c++
class Solution {
public:
    int rows,cols;
    bool exist(vector<vector<char>>& board, string word) {
        rows=board.size();
        cols=board[0].size();
        for(int i=0;i<rows;i++){
            for(int j=0;j<cols;j++){
                if(dfs(i,j,0,board,word))return true;
            }
        }
        return false;
    }

    bool dfs(int row,int col,int k,vector<vector<char>>& board,const string& word){
        if(row<0||col<0||row>=rows||col>=cols||board[row][col]!=word[k])return false;
        if(k==word.size()-1)return true;
        board[row][col]='\0';
        bool res=dfs(row-1,col,k+1,board,word)||dfs(row+1,col,k+1,board,word)||dfs(row,col-1,k+1,board,word)||dfs(row,col+1,k+1,board,word);
        board[row][col]=word[k];
        return res;
    }
};
```

## 10.机器人的运动范围

https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/

这题我一开始还是想着用上题类似的DFS模板，结果写出来过不了，看到Krahets这篇题解简直惊呆了，剑指offer书上用的也是传统的DFS，这篇题解竟然如此简洁，看来还是高手在民间，以后做完题一定要看看题解，学习别人的思路。这个解法有两个巧妙之处，一是证明只有往右或往下搜索才能得到其他可行解，去掉了左和上两个方向，具体见题解。二是用增量的方式计算数位和，由于每次dfs只有一个坐标会变，所以单独计算这个坐标和的增量即可，而数位和增量正常情况是+1，当坐标增加到10的倍数时会-8，通过这种方法大大减少了关于数位和的计算，很巧妙。以后做题一定要有数学思维，多想想能否在数学上简化计算。

```c++
class Solution {
public:
    int movingCount(int m, int n, int k) {
        vector<vector<bool>> visited(m,vector<bool>(n,false));
        return dfs(0,0,0,0,visited,m,n,k);
    }

    int dfs(int x,int y,int sumx,int sumy,vector<vector<bool>>& visited,int m,int n,int k){
        if(x>=m||y>=n||visited[x][y]||sumx+sumy>k)return 0;
        visited[x][y]=true;
        return 1+dfs(x+1,y,(x+1)%10==0?sumx-8:sumx+1,sumy,visited,m,n,k)+dfs(x,y+1,sumx,(y+1)%10==0?sumy-8:sumy+1,visited,m,n,k);
    }
};
```

## 11.剪绳子

https://leetcode-cn.com/problems/jian-sheng-zi-lcof/

```C++
class Solution {
public:
    int cuttingRope(int n) {
        vector<int> dp(n+1,0);
        dp[1]=1;
        for(int i=2;i<=n;i++){
            int maxn=0;
            for(int j=1;j<i;j++){
                maxn=max(maxn,max(j*dp[i-j],j*(i-j)));
            }
            dp[i]=maxn;
        }

        return dp[n];
    }
};
```

我的解法如上，时间复杂度$O(n^2)$的动态规划，然后又是看了Krahets的题解，再一次惊呆了.......真的第一次感觉到**数学如此重要!!!!!!**......以后一定好好补起来.......

```c++
class Solution {
public:
    int cuttingRope(int n) {
        if(n <= 3) return n - 1;
        int a = n / 3, b = n % 3;
        if(b == 0) return (int)pow(3, a);
        if(b == 1) return (int)pow(3, a - 1) * 4;
        return (int)pow(3, a) * 2;
    }
};
```

评论区找到了更简洁的代码：

```C++
class Solution {
public:
    int cuttingRope(int n) {
        return n <= 3? n - 1 : pow(3, n / 3) * 4 / (4 - n % 3);
    }
};
```

## 12.剪绳子_2

https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/

比上题多了一个大数问题要考虑，同时可以用快速幂优化。

```c++
class Solution {
public:
    int cuttingRope(int n) {
        if(n<=3)return n-1;
        long a=n/3,b=n%3;
        if(b==2)return quickPow(3,a)*2%1000000007;
        else return quickPow(3,a-1)*(b+3)%1000000007;
    }

    long quickPow(long x,long n){
        long res=1;
        while(n){
            if(n&1){
                res=res*x%1000000007;
            }
            n>>=1;
            x=x*x%1000000007;
        }
        return res;
    }
};
```



## 13.二进制中1的个数

https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/

位运算，`n&(n-1)`相当于把n二进制中最右边的1变成0，因此不断令`n=(n-1)&n`，直至`n==0`即可得知有多少个1。

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int count=0;
        while(n){
            count++;
            n=(n-1)&n;
        }
        return count;
    }
};
```

## 14.数值的整数次方

https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/

老快速幂了

```c++
class Solution {
public:
    double myPow(double x, int n) {
        double result=1.0;
        long b=n;   //因为当n为-2147483648时，转为正数会超出int范围
        if(b<0){
            x=1/x;
            b=-b;
        }
        while(b){
            if(b&1)result*=x;
            b>>=1;
            x*=x;
        }
        return result;
    }
};
```

## 15.打印从1到最大的n位数

https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/

这题leetcode上跟剑指offer不一样，没有考虑大数问题，直接暴力就过了。

```c++
class Solution {
public:
    vector<int> printNumbers(int n) {
        int maxN=(int)pow(10,n);
        vector<int> res;
        for(int i=1;i<maxN;i++){
            res.emplace_back(i);
        }
        return res;
    }
};
```

剑指offer上这题实际考察的是大数问题，用字符串模拟大数，另外在打印时要注意去掉高位多余的0。更简洁的解法是使用递归来对数字进行全排列。

```c++
class Solution {
public:
    vector<int> res;
    vector<int> printNumbers(int n) {
        char* num=new char[n];
        for(int i=0;i<10;i++){
            num[0]=i+'0';
            printNumbersRecursively(num,n,0);
        }
        return res;
    }

    void printNumbersRecursively(char* num,int length,int index){
        if(index==length-1){
            int temp=CharArratToInt(num,length);
            if(temp!=0)res.emplace_back(temp);
            return;
        }
        for(int i=0;i<10;i++){
            num[index+1]=i+'0';
            printNumbersRecursively(num,length,index+1);
        }
    }

    int CharArratToInt(char* num,int length){
        int p=0;
        int res=0;
        while(p<length){
            res=res*10+(num[p]-'0');
            p++;
        }
        return res;
    }
};
```

## 16.删除链表节点

https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/

基础的链表操作题

```c++
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
        if(head->val==val)return head->next;
        ListNode *p=head;
        while(p!=NULL){
            if(p->next->val==val){
                p->next=p->next->next;
                break;
            }
            p=p->next;
        }
        return head;
    }
};
```

这题leetcode和剑指offer上不同，剑指offer是直接把要删除的结点作为函数参数，要求在O(1)时间删除结点，由于单链表无法在O(1)时间得到前驱结点，所以思路是将后继结点的值复制到当前结点，然后把后继结点删除，效果等同于删除了当前结点。

## 17.正则表达式匹配

https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/

这题一开始理解错了....我还以为星号前面所有的字符都可以重复呢.....给我吓坏了...看了题解才知道是前面一个字符可以重复。

一切匹配问题都是状态机，星号意味着可以转向下一个状态也可以停留在当前状态，所以每次将字符串和模式串中的一个字符比较。如果模式串后面一个字符是星号的话，此时有三种选择，一种是当前两个字符不匹配，则只能模式串向后移两位，忽略掉星号，第二种是两个字符匹配则字符串向后移一位，模式串向后移两位，转向下一状态，第三种是两个字符匹配，字符串移位但模式串不动，停留在当前状态。如果模式串后一个字符不是星号就简单了，直接匹配当前字符即可。

```c++
class Solution {
public:
    int size_s,size_p;
    bool isMatch(string s, string p) {
        size_s=s.length();
        size_p=p.length();
        return MatchCore(s,p,0,0);
    }

    bool MatchCore(string& str,string& pattern,int sp,int pp){
        if(sp==size_s&&pp==size_p)return true;
        if(sp!=size_s&&pp==size_p)return false;

        if(pp+1<size_p&&pattern[pp+1]=='*'){
            if(sp<size_s&&(str[sp]==pattern[pp]||pattern[pp]=='.')){
                return MatchCore(str,pattern,sp+1,pp)||MatchCore(str,pattern,sp+1,pp+2)||MatchCore(str,pattern,sp,pp+2);
            }
            else
                return MatchCore(str,pattern,sp,pp+2);
        }
        
        if(sp<size_s&&(str[sp]==pattern[pp]||pattern[pp]=='.'))
            return MatchCore(str,pattern,sp+1,pp+1);
        return false;
    }
};
```

以上是剑指offer的解法，比较清晰易懂，结果一交，好家伙，只击败了5%的用户，下面是动态规划的解法，效率会高很多（但是很难想）：

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size() + 1, n = p.size() + 1;
        vector<vector<bool>> dp(m, vector<bool>(n, false));
        dp[0][0] = true;
        for(int j = 2; j < n; j += 2)
            dp[0][j] = dp[0][j - 2] && p[j - 1] == '*';
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                dp[i][j] = p[j - 1] == '*' ?
                    dp[i][j - 1] || dp[i][j - 2] || dp[i - 1][j] && (s[i - 1] == p[j - 2] || p[j - 2] == '.'):
                    dp[i - 1][j - 1] && (p[j - 1] == '.' || s[i - 1] == p[j - 1]);
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

## 18.表示数值的字符串

https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/

这题剑指offer上就是直接扫描字符串判断每一部分的，虽然对这题来说效率高但我觉得没什么学习的价值，leetcode的题解用了状态机的思想，虽然在这题上效率会低一些，也更难写，但我觉得是更值得学习的思想，对于状态机来说，最复杂的就是画出状态转换图了，这里实在懒得画了毕竟只是学习下思想真要面试也不可能这么写，直接用Krahets题解里的了。

![Picture1.png](https://pic.leetcode-cn.com/6f41d7e46fd0344c013980e3f46429dd7a7311bb4292783a482466a90f15747b-Picture1.png)

```c++
class Solution {
public:
    bool isNumber(string s) {
        unordered_map<int,unordered_map<char,int>> FSM{
            {0,{{' ',0},{'s',1},{'d',2},{'.',4}}},
            {1,{{'d',2},{'.',4}}},
            {2,{{'d',2},{'.',3},{'e',5},{' ',8}}},
            {3,{{'d',3},{'e',5},{' ',8}}},
            {4,{{'d',3}}},
            {5,{{'s',6},{'d',7}}},
            {6,{{'d',7}}},
            {7,{{'d',7},{' ',8}}},
            {8,{{' ',8}}}
        };
        int p=0;
        char cur_state;
        for(char c:s){
            if(c>='0'&&c<='9')cur_state='d';
            else if(c=='+'||c=='-')cur_state='s';
            else if(c=='e'||c=='E')cur_state='e';
            else if(c=='.'||c==' ')cur_state=c;
            else cur_state='?';
            if(FSM[p].find(cur_state)==FSM[p].end())return false;
            p=FSM[p][cur_state];
        }
        return p==2||p==3||p==7||p==8;
    }
};
```

## 19.调整数组顺序使奇数位于偶数前

https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/

基础双指针

```c++
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        for(int i=0,j=0;i<nums.size();i++){
            if(nums[i]&1){
                swap(nums[i],nums[j]);
                j++;
            }
        }
        return nums;
    }
};
```

## 29.从上到下打印二叉树

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/

最基本的BFS，套模板即可。

```C++
class Solution {
public:
    vector<int> levelOrder(TreeNode* root) {
        vector<int> res;
        if(!root)return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            TreeNode* p=q.front();
            q.pop();
            res.emplace_back(p->val);
            if(p->left)q.push(p->left);
            if(p->right)q.push(p->right);
        }
        return res;
    }
};
```

## 30.从上到下打印二叉树_2

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/

和上题类似，套BFS模板，多了一个分层打印的要求，所以每次将队列里所有元素出队即为一层，再将下一层所有元素入队即可。

```C++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root)return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int cnt=q.size();
            vector<int> Cur_level;
            while(cnt--){
                TreeNode* p=q.front();
                q.pop();
                Cur_level.emplace_back(p->val);
                if(p->left)q.push(p->left);
                if(p->right)q.push(p->right);
            }
            res.emplace_back(Cur_level);
        }
        return res;
    }
};
```

## 31.从上到下打印二叉树_3

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/

比上题多了个之字形打印的要求，直接把奇数层逆置即可，学了一个reverse函数。

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root)return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int cnt=q.size();
            vector<int> Cur_level;
            while(cnt--){
                TreeNode* p=q.front();
                q.pop();
                Cur_level.emplace_back(p->val);
                if(p->left)q.push(p->left);
                if(p->right)q.push(p->right);
            }
            res.emplace_back(Cur_level);
        }
        for(int i=0;i<res.size();i++){
            if(i%2!=0)reverse(res[i].begin(),res[i].end());
        }
        return res;
    }
};
```

