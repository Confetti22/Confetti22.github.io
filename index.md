

### 本次介绍 搜索算法中最重要的两种算法：BFS(广度优先搜索)+DFS（深度优先搜索）

一个搜索问题可以转化成4个重要维度：
①状态空间（搜索空间）：即所有可能出现状态的集合
②状态转移：即从当前状态如何获取下一个状态， 可以用搜索树这种树的结构表示
③起始状态：
④目标状态：原问题的解

**搜索**：就是**起始状态**经过一系列的**状态转移**最终抵达**目标状态**的过程 ，即对搜索树的遍历过程，从根节点（起始状态）-->最终的目标状态结点

而对搜索树的搜索策略可分为：

①BFS 当前状态全部扩展完后再扩展新状态 

②DFS 立即扩展新的到的状态



可以从对二叉树的BFS ,DFS遍历体会两者的区别：

#### **1）BFS遍历二叉树（层次遍历）**

<img src="C:/Users/confetti/AppData/Roaming/Typora/typora-user-images/image-20220714202231136.png" alt="image-20220714202231136" style="zoom:80%;" />

层次遍历的算法思想：

①初始化一个辅助队列

②根节点入队

③若队列非空， 则队头结点出队，访问该结点，并将其左右孩子插入队尾

④重复③直到队列为空

```c++
 void Bfs(BiTNode* T){
     queue<BiTNode*> Q;//为了节省空间，辅助队列Q中可以只存放指向结点的指针
     Q.push(T);//根节点入队
     while(!Q.empty()){
         //队头结点出队
         BiTNode* temp=Q.front(); 
         Q.pop();
         visit(temp);//访问该结点
         //当前结点的左右孩子入队
         if(temp->lchild!=nullptr)
             Q.push(temp->lchild);
         if(temp->rchild!=nullptr)
             Q.push(temp->rchild);     
     }    
 }
```

#### **2）DFS遍历二叉树（基于树的递归特性， 又可根据遍历根节点的次序分为前序，中序，后续遍历）**

<img src="C:/Users/confetti/AppData/Roaming/Typora/typora-user-images/image-20220714202702067.png" alt="image-20220714202702067" style="zoom: 33%;" />

```c++
//以先序遍历为例子
void PreOrder(BiTNode* T){
	if(T!=nullptr){
        vist(T);//先访问根节点T
        PreOrder(T->lchild);//递归遍历T的左子树
        PreOrder(T->rchild);//递归遍历T的右子树
    }
}
```



那么，针对一个搜索树，该采取怎样的搜索策略？

|      | 特点                             | 实现方式                                                     | 适用情况     |
| ---- | -------------------------------- | ------------------------------------------------------------ | ------------ |
| BFS  | 当前状态全部扩展完后再扩展新状态 | 队列：将得到的状态依次放入队尾，每次取队头元素扩展           | 求最优解     |
| DFS  | 立即扩展新的到的状态             | ①栈（先都到的状态后扩展）但栈实现的代码看起来不够简单明了  ②递归策略 | 判断是否有解 |



#### 3）以下分析两个典型的搜索问题

1. ##### Cath That Cow

农夫John的牛跑了， 假设John 和他的牛都在同一条直线段上， John 和牛的当前位置分别为N （0<=N<=100000）和K（0<=K<=100000）N， k 都为整数

John 有两种移动方式：

①走路：在一分钟内可以从任意位置点X 移动到 X-1或X+1

②瞬移：在一分钟内可以从任意位置点 X移动到 2X

假设牛在整个过程中不会移动， John 从 N 位置出发， 最短多长时间内可以抓到牛？http://poj.org/problem



阅读题目后可将本问题转化为以下结构：

状态空间  （位置n, 时间t）

状态转移 （n-1,t+1）\(n+1,t+1) (2*n,t+1) 

起始状态  （N，0）

目标状态（K， 最短时间）



又因为本题是典型的最优解问题（求最短时间）， 故采用BFS搜索策略

本题的搜索树：

![image-20220714193639582](C:/Users/confetti/AppData/Roaming/Typora/typora-user-images/image-20220714193639582.png)





解答代码， 其实本质上就是二叉树BFS遍历的变种，可对比二叉树BFS遍历 ##注释处

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <queue>

using namespace std;

const int MAXN = 1e5 + 10;

bool visit[MAXN];

struct Status {
    int position;
    int time;
    Status(int p, int t): position(p), time(t) {}
};

int BFS(int n, int k) {
    queue<Status> myQueue;
    myQueue.push(Status(n, 0));             //压入初始状态  ##根节点入队
    visit[n] = true;                        //起点已访问
    int answer = 0;
    while (!myQueue.empty()) {
        Status current = myQueue.front();   // ##队头结点出队
        myQueue.pop();
        if (current.position == k) {        //查找成功  ##已经到达目标状态，结束搜索
            answer = current.time;
        }
        for (int i = 0; i < 3; ++i) {       //转入不同状态
            Status next = current;
            if (i == 0) {
                next.position += 1;
            } else if (i == 1) {
                next.position -= 1;
            } else {
                next.position *= 2;
            }
            if (next.position < 0 || next.position >= MAXN || visit[next.position]) {
                continue;                   //新状态不合法
            }
            next.time++;
            myQueue.push(next);             //压入新的状态  ##当前结点的左右孩子入队
            visit[next.position] = true;    //该点已访问
        }
    }
    return answer;
}

int main() {
    int n, k;
    scanf("%d%d", &n, &k);
    memset(visit, false, sizeof(visit));
    printf("%d\n", BFS(n, k));
    return 0;
}
```



2. ##### A Knight's Journey

题目大意：任选一个起点，按照国际象棋马的跳法（马走日），不重复的跳完整个棋盘，如果有多种路线则选择字典序最小的路线（路线是点的横纵坐标的集合，注意棋盘的横坐标的用大写字母，纵坐标是数字)

**题目分析：** 

1.应该看到这个题就可以想到用DFS, 当首先要明白这个题的意思是能否只走一遍（**不回头不重复**）将整个地图走完，而普通的深度优先搜索是一直走，走不通之后沿路返回到某处继续深搜。所以这个题要用到的**回溯思想**，如果不重复走一遍就走完了，做一个标记，算法停止；否则在某种DFS下走到某一步时按马跳的规则无路可走而棋盘还有为走到的点，这样我们就需要**撤消**这一步，进而尝试其他的路线（当然其他的路线也可能导致撤销），而所谓撤销这一步就是在递归深搜返回时重置该点，以便在当前路线走一遍行不通换另一种路线时，该点的状态是未访问过的，而不是像普通的DFS当作已经访问了。

2.如果有多种方式可以不重复走一遍的走完，需要输出按**字典序最小的路径**，而注意到国际象棋的棋盘是列为字母，行为数字，如果能够不回头走一遍的走完，一定会经过A1点，所以我们应该**从A1开始搜索**，以确保之后得到的路径字典序是最小的（也就是说如果路径不以A1开始，该路径一定不是字典序最小路径），而且我们应该确保**优先选择的方向是字典序最小的方向**，**这样我们最先得到的路径就是字典序最小的**。



状态空间  (x,y,step)

状态转移 (x=2,y+1,step+1), (x+1,y+2,step+1)....共八个方向（但实际要受到棋盘边界和该点是否已访问过的制约）

起始状态（0,0,1）

目标状态（x,y,棋盘的总格子数）



```c++
#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>

using namespace std;

const int MAXN = 30;                            //棋盘参数最大为30*30

int direction[8][2] = {  //Knight可以走的8个方向
    {-1, -2}, {1, -2}, {-2, -1}, {2, -1}, {-2, 1}, {2, 1}, {-1, 2}, {1, 2}
};

bool visit[MAXN][MAXN];     //标记棋盘的某一位置是否已经路过

//当前位置为(x,y) 已路过的格子数为step , answer记录路线， 棋盘大小为p*q
bool DFS(int x, int y, int step, string answer, int p, int q) {
    if (step == p * q) {                        //搜索成功
        cout << answer << endl << endl;
        return true;
    }
    for (int i = 0; i < 8; ++i) {           //遍历8个方向
        int nx = x + direction[i][0];       //生成当前方向的下一步的位置(nx,ny)
        int ny = y + direction[i][1];
        
        //判断(nx,ny)是否超过棋盘边界或是已经被访问过，只能转移到合法的新状态(nx,ny)
        if (nx < 0 || nx >= p || ny < 0 || ny >= q || visit[nx][ny]) {
            continue;
        }
        char col = ny + 'A';                //该点编号
        char row = nx + '1';
        visit[nx][ny] = true;               //访问新状态(nx,ny)
        
        //通过修改递归参数进行状态转移
        if (DFS(nx, ny, step + 1, answer + col + row, p, q)) {
            return true;
        }
        visit[nx][ny] = false;   //如果没有返回成功，则需要重新选择一条路， 撤销（nx,ny)这一步
    }
    return false;
}

int main() {
    int n;
    scanf("%d", &n);
    int caseNumber = 0;
    while (n--) {
        int p, q;
        scanf("%d%d", &p, &q);
        memset(visit, false, sizeof(visit));
        cout << "Scenario #" << ++caseNumber << ":" << endl;
        visit[0][0] = true;                       //标记A1点
        if (!DFS(0, 0, 1, "A1", p, q)) {
            cout << "impossible" << endl << endl;
        }
    }
    return 0;
}
```



作业题中的leet 127其实就可以采用BFS搜索策略，只是这种简单的实现效率不是很高（O(a *n2), 词长度为a, wordList长度为n, ），不过也可以提交通过的， 更高效的算法可以参照leetcode上的其他题解

单词接龙

```c++
#include<iostream>
#include<vector>
#include<string>
#include<string.h>
#include<queue>
#include<map>
using std::cout;
using std::endl;
using std::string;
using std::vector;
using std::queue;
using std::map;
class Solution {
public:
    struct status{
        string word;//当前单词
        size_t idx;//由当前单词进行转换时不能改变的字母下标
        int cnt;//当前路线上总单词数
    };
    map<string,bool>visit; //记录list中的单词是否被访问过
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        queue<status>myqueue;
        size_t length=strlen(beginWord.c_str());
        //根节点入队
        myqueue.push(status{beginWord,0,1});
        cout<<"enqueue:"<<beginWord<<" "<<0<<" "<<1<<endl;
        visit[beginWord]=true;

        //当队列不空时，不断让队头结点出队，判断是否已到达目标状态，
        //已到达目标状态时就结束，否则依据转换规则，生成新的状态结点并入队
        while(!myqueue.empty()){
            //队头结点出队
            status current=myqueue.front();
            myqueue.pop();
            //判断队头结点是否以达到目标状态
            if(current.word==endWord){
                return current.cnt;
            }
            for(size_t i=0;i<wordList.size();i++){//遍历wordList中的每个word
                size_t diffCnt=0,diffidx=0;
                bool idxSame=true;
                string newWord=wordList[i];

                //判断newWord是否满足与current.word只有一个字母不同，且idx指定的下标相同
                idxSame=(newWord[current.idx]==current.word[current.idx]);
                if(idxSame||current.cnt==1){//根节点只要求有一个字母不同即可
                    for(size_t i=0;i<length;i++){
                        if(newWord[i]!=current.word[i]){
                            diffCnt++;
                            diffidx=i;

                        }
                    }

                    //新的状态结点入队
                    if(diffCnt==1&&(!visit[newWord])){
                        myqueue.push(status{newWord,diffidx,current.cnt+1});
                        cout<<"enqueue:"<<newWord<<" "<<diffidx<<" "<<current.cnt<<endl;
                        visit[newWord]=true;

                    }

                }//if(idxSame)

            }//for

        }//while

        //遍历完整颗搜索树后不存在可行的转换路径   
        return 0;

    }//ladderLength(string beginWord, string endWord, vector<string>& wordList) {
};//class Solution


int main(){
    vector<string>wordList{"hot","dot","lot","log","cog"};
    Solution solu;
    cout<<solu.ladderLength("hit","cog",wordList)<<endl;
}
```



作业题之：LRU（unordered_map+list）以实现O(1)

```c++
#include<iostream>
#include<unordered_map>
#include<string>
#include<list>
using std::cout;
using std::endl;
using std::string;
using std::list;
using std::unordered_map;


class LRUcache{
public:
    LRUcache(int capacity):_capacity(capacity) ,_size(0){}

    //read LRUcache for key 
    //return key's value, -1 if key doesn't exit
    int get(int key) {
        auto search=_lruMap.find(key);
        if(search!=_lruMap.end()){
            //cache中时，将该结点（ptr所指结点）移动到链表LRU_list头
            _lruList.splice(_lruList.begin(),_lruList,search->second.ptr);
            return search->second.value;           
        }else{
            return -1;
        }
    }

    //write LRUcache
    void put(int key, int value) {
        auto search1=_lruMap.find(key);
        if(search1==_lruMap.end()){
                //cache->命中且还未达到capacity
                //在list头新插入一个key结点，ptr指向key结点
                //并在LRU_map中新建一个key——{value，ptr}映射 
                _lruList.push_front(key);
                auto ptr=_lruList.begin();
                _lruMap[key]={value,ptr};
                _size++;
                
                if(_size>_capacity){
                //已超过capacity
                //list的表尾结点出队,根据该结点的key值去删除map中的相应映射;
                //然后执行上一种情况的操作
                int &key2dele=_lruList.back();
                int ret= _lruMap.erase(key2dele);
                if(ret!=1){
                    std::cerr<<"ret="<<ret<<endl;
                    std::cerr<<"_lruMap.erase("<<key2dele<<")"<<" error"<<endl;
                }
                _lruList.pop_back();
                _size--;

                }
        }else{

            //若key已存在，则变更key值
            search1->second.value=value;      
            //cache->中时，将该结点（ptr所指结点）移动到链表LRU_list头
            _lruList.splice(_lruList.begin(),_lruList,search1->second.ptr);
        }
    }

private:
    typedef struct{
        int value;//值
        list<int>::iterator  ptr;//指向list中的相应结点
    }data;
    int _capacity;
    int _size;
    list<int>_lruList;
    unordered_map<int,data>_lruMap;
};

```



