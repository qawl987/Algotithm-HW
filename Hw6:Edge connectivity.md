``` c++
#include <bits/stdc++.h>
#define maxnode 1000
using namespace std;
int c[maxnode][maxnode];
int flowPassed[maxnode][maxnode];
vector<int> g[maxnode];
int parList[maxnode];
int currentPathC[maxnode];
int cnt=0;
int bfs(int sNode, int eNode)//breadth first search
{
   // 每次要bfs都先清空parent 跟 目前這個點的
   memset(parList, -1, sizeof(parList));
   memset(currentPathC, 0, sizeof(currentPathC));
   queue<int> q;//declare queue vector
   q.push(sNode);
   parList[sNode] = -1;//initialize parlist’s source node
   currentPathC[sNode] = 999;//initialize currentpath’s source node
   while(!q.empty())// if q is not empty
   {
      int currNode = q.front();
      q.pop();
      for(int i=0; i<g[currNode].size(); i++)
      {
         int to = g[currNode][i];
         if(parList[to] == -1)
         {
            if(c[currNode][to] - flowPassed[currNode][to] > 0)
            {
               parList[to] = currNode;
               currentPathC[to] = min(currentPathC[currNode],c[currNode][to] - flowPassed[currNode][to]);
               if(to == eNode)
               {
                  return currentPathC[eNode];
               }
               q.push(to);
            }
         }
      }
   }
   
   return 0;
}
int edmondsKarp(int sNode, int eNode)
{
   int maxFlow = 0;
   while(true)
   {
      int flow = bfs(sNode, eNode);
      if (flow == 0)
      {
         break;
      }
      cnt++;
      maxFlow += flow;
      int currNode = eNode;
      while(currNode != sNode)
      {
         int prevNode = parList[currNode];
         flowPassed[prevNode][currNode] += flow;
         flowPassed[currNode][prevNode] -= flow;
         currNode = prevNode;
      }
      
   }
   //cout<<sNode<<' '<<eNode<<' '<<maxFlow<<endl;
return maxFlow;
}
int main()
{
    // ios::sync_with_stdio(false);
    // cin.tie(0);
   int nodCount;
   cin>>nodCount;
   int a,b,cap=1;
   while(cin>>a>>b)
   {
        c[a][b] = cap;
        c[b][a] = cap;
        g[a].push_back(b);
        g[b].push_back(a);
   }
   int maxFlow=INT_MAX;
   int source = 0;
   memset(flowPassed,0,sizeof(flowPassed));
    for(int sink=1;sink<nodCount;sink++)
    {
        memset(flowPassed,0,sizeof(flowPassed));//刷新
        maxFlow = min(maxFlow,edmondsKarp(source,sink));
    }
   cout<<maxFlow<<endl;
   system("pause");
}
```
