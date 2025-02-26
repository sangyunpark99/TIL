깊이 우선 탐색이 뭘까?




이 글은 큰돌의 터전님의 강의인 10주 완성 C++ 코딩테스트 알고리즘 개념 교안을 참고하여 작성했습니다.



깊이 우선 탐색
깊이 우선 탐색인 DFS(Depth First Search)는 그래프를 탐색할 때 쓰는 알고리즘으로 한 노드부터 시작해 인접한 노드들을 재귀적으로 방문합니다. 한번 방문한 정점은 다시 방문하지 않고, 매 분기마다 가장 멀리 있는 노드까지 탐색 가능한 알고리즘 입니다.



깊이 우선 탐색의 수도 코드를 알아볼까요?


수도 코드는 프로그램 로직을 표현하기 위해 사용되는 코드입니다.

DFS 알고리즘의 수도 코드는 아래와 같습니다.

```
DFS(u, adj)
u.visited = true
for each v ∈ adj[u]
if v.visited == false
DFS(v, adj)
```


각 코드가 가지는 의미는 다음과 같습니다.

한 정점 u를 방문처리(u.visited = true) 해주고, u와 연결된 다른 지점 v를 탐색합니다.
다른 지점 v 중 방문처리(u.visited = false)가 안되어 있는 노드에 대해 재귀적으로 DFS를 호출합니다.

각 노드가 이미지와 같이 연결되어 있는 경우, DFS 탐색을 하면 어떠한 순서로 진행이 될까요?

코드로 구현하고, 방문 순서를 출력해 보겠습니다.



구현한 코드는 다음과 같습니다.

```java
private static void dfs(int curNode, boolean[] visited) {

        for(int nextNode : nodes.get(curNode)) {
            if(!visited[nextNode]) {
                visitOrder.add(nextNode);
                visited[nextNode] = true;
                dfs(nextNode, visited);
            }
        }
    }

```

출력한 방문 순서는 어떻게 될까요? 출력한 결과는 다음과 같습니다.




다음은 각 노드의 연관관계를 나타낸 코드입니다.
```java
nodes.get(1).add(2);
nodes.get(1).add(3);
nodes.get(2).add(4);
nodes.get(2).add(5);
nodes.get(1).add(3);

```

DFS를 구현하는 방법은 2가지가 존재합니다.



구현 방법 1 : 돌다리를 두들겨 보자 방법
```java
void DFS(int here){
visited[here] = 1;
for(int there: adj[here]){
if(visited[there] == 1) continue;
dfs(there);
}
}

```

방문이 되어 있는 경우라면 방문을 하지 않고, 방문이 안되어있는 경우 방문처리를 해줍니다.

```java
void DFS(int here){
for(int there: adj[here]){
    if(visited[there] == 1) continue;
    visited[there] = 1;
    dfs(there);
    }
}

```

이 코드인 경우엔 시작지점에 대해 방문처리를 해주어야 합니다.

1번 노드가 시작점인 경우엔 dfs로 탐색하기전 아래와 같이 방문 처리를 해주어야 합니다.

```java
visited[1] = 1;
DFS(1);

```

구현 방법 2 :  못먹어도 GO
방문여부와 상관 없이 무조건 DFS를 호출하고 해당 함수에 방문이 되어 있는 경우 return해서 함수를 종료시키는 방법입니다.

```java
void DFS(int here){
    if(visited[here] == 1) return;
    visited[here] = 1;
    for(int there: adj[here]){
        DFS(there);
    }
}
```


문제에 따라 더 편한 구현 방법을 사용하면 됩니다.