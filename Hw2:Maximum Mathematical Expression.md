# Hw2:Maximum Mathematical Expression
- code

``` c++
#include <bits/stdc++.h>
#define len 100
using namespace std;
typedef long long int ll;
/*
    left,right 左右邊要查最大表或最小表0,1表示
    next這次是做哪一個operator
*/
struct record{  
    int left,right,next;
};
record t_m[len][len],t_M[len][len];     //建立最大最小表
vector<ll> s;   //儲存切割下來的數字
vector<char> o;     //切割下來的operator
int l;      //有幾個operator
string in;      //輸入的字串
/*
遞迴方式上括號
*/
void pare(int i,int j,int M_m)
{
    if(i==j)
    {
        cout<<s[j];     //切到左右相同時輸出裡面的數字
        return;
    }
    else
    {
        if(i!=0||j!=l)      //最外面不用括號其他要輸出
            cout<<'(';
        int x,y,z;
        if(M_m==0)      //根據入參決定這次是看最大表還是最小表
        {
            x = t_M[i+1][j].next;   //表中下一個operator為何。+1是因為從(0,l)開始但表從1開始
            y = t_M[i+1][j].left;   //表中左邊是從最大還是最小來的
            z = t_M[i+1][j].right;  //表中右邊是從最大還是最小來的
        }
        else 
        {
            x=t_m[i+1][j].next;
            y = t_m[i+1][j].left;
            z = t_m[i+1][j].right;
        }
        pare(i,x-1,y);      //左邊區塊去遞迴最大值或最小值的表達式
        cout<<o[x];     //輸出這次的operator
        pare(x,j,z);
        if(i!=0||j!=l)
            cout<<')';
    }
}
int main()
{
    cin>>in;
    string q="";
    o.push_back('@');   //表從1開始亂塞
    q+=in[0];   //處理開頭為-
    for(int i=1;i<in.length();i++)      //切割輸入數字&operator
    {
        if(in[i]!='+'&&in[i]!='-'&&in[i]!='*')
            q+=in[i];
        else
        {
            s.push_back(atoi(q.c_str()));
            q="";
            o.push_back(in[i]);
        }
    }
    l=o.size()-1;   //operator數量
    s.push_back(atoi(q.c_str()));   //最後一個數字放進去
    for(int i=1;i<=l;i++)
    {
        t_M[i][i].next=i;   //初始化大小表的斜對角式自己，重要遞迴才切割的對
        t_m[i][i].next=i;
    }
    ll m[len][len],M[len][len];     //指儲存最大跟最小值其實可以跟trace放一起懶得改
    int j;      //迴圈變數
    for(int i=0;i<len;i++)      //其實好像不用初始化這一些應該是在下面的時候初始化就好
    {
        for(int j=0;j<len;j++)
        {
            m[i][j]=9123372036854775807;
            M[i][j]=-9123372036854775807;
        }
    }
    for(int i=1;i<=l;i++)   //算第一層倆倆的答案
    {
        ll x;
        if(o[i]=='+')
            x=s[i-1]+s[i];
        else if(o[i]=='-')
            x=s[i-1]-s[i];
        else if(o[i]=='*')
            x=s[i-1]*s[i];
        m[i][i]=M[i][i]=x;
    }
    for(int c=1;c<l;c++)    //根據觀察順序是j-i==c這樣的順序
    {
        for(int i=1;i<l;i++)
        {
            j=i+c;
            for(int k=i;k<=j;k++)
            {
                ll L,R,M_m[2][2]={{M[i][k-1],m[i][k-1]},{M[k+1][j],m[k+1][j]}};     //左右都要用最大最小排列組合所以用迴圈搞定
                switch(o[k])    //根據每個Operator有自己的行為
                {
                    case '+':
                    for(int a=0;a<2;a++)
                    {
                        for(int b=0;b<2;b++)    //最大最小的排列組合
                        {
                            if(k==i)    //當k是一開始則左邊只能是一個數字而非一個區段
                                L=s[k-1];
                            else
                                L=M_m[0][a];
                            if(k==j)    //同理k最右邊也只能一個數字
                                R=s[k];
                            else
                                R=M_m[1][b];
                            if(L+R>=M[i][j])    //如果最大值超過則把值記錄下來並且把左右是最大或最小以及下一步做誰(k)記錄下來
                            {
                                M[i][j]=L+R;
                                t_M[i][j]={a,b,k};
                            }
                            if(L+R<m[i][j])
                            {
                                m[i][j]=L+R;
                                t_m[i][j]={a,b,k};
                            }
                        }
                    }
                    break;
                    case '-':   //同理
                    for(int a=0;a<2;a++)
                    {
                        for(int b=0;b<2;b++)
                        {
                            if(k==i)
                                L=s[k-1];
                            else
                                L=M_m[0][a];
                            if(k==j)
                                R=s[k];
                            else
                                R=M_m[1][b];
                            if(L-R>=M[i][j])
                            {
                                M[i][j]=L-R;
                                t_M[i][j]={a,b,k};
                            }
                            if(L-R<m[i][j])
                            {
                                m[i][j]=L-R;
                                t_m[i][j]={a,b,k};
                            }
                        }
                    }
                    break;
                    case '*':
                    for(int a=0;a<2;a++)
                    {
                        for(int b=0;b<2;b++)
                        {
                            if(k==i)
                                L=s[k-1];
                            else
                                L=M_m[0][a];
                            if(k==j)
                                R=s[k];
                            else
                                R=M_m[1][b];
                            if(L*R>=M[i][j])
                            {
                                M[i][j]=L*R;
                                t_M[i][j]={a,b,k};
                            }
                            if(L*R<m[i][j])
                            {
                                m[i][j]=L*R;
                                t_m[i][j]={a,b,k};
                            }
                        }
                    }
                    break;
                }
            }
        }
    }
    cout<<M[1][l]<<endl;
    pare(0,l,0);
    cout<<endl;
    system("pause");
}
//5+6-7*8-9
//11+44*77-88+44
//78154*24+58-89+91
//78*536-153+4531*1285+45-45*8
//1-2*3+4*5-6
``` 
- input output
- <img width="117" alt="螢幕擷取畫面 2022-04-07 191745" src="https://user-images.githubusercontent.com/62208230/162187193-c825eda1-477b-47f5-9ba5-3fce802a75c0.png">
