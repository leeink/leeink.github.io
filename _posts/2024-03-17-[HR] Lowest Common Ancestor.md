---
title: HackerRank Lowest Common Ancestor
date: 2024-03-17 21:10:00 +09:00
categories: [ HackerRank ]
tags: [ HackerRank, Algorithm, Programming ]
---

# 이진트리 최소 공통 조상 찾기 알고리즘

이진트리의 최소 공통 조상은 어느 두 노드의 공통 조상 중 깊이가 가장 큰
조상을 말한다.  

문제에서 트리와 두 노드(v1, v2)가 주어지면 
두 노드의 최소공통조상을 찾아야 한다.

이 문제는 루트를 기준으로 오른쪽 자식이 더 크고 왼쪽 자식이 더 작다는
가정이 있다.

이 문제에서 최소 공통 조상을 찾기 위한 아이디어는 다음과 같다.

1. 재귀적 탐색으로 두 노드의 최소공통조상을 찾는다.
2. 현재 노드의 데이터가 v1, v2보다 크면 현재 노드를 
현재 노드의 오른쪽 자식으로 이동한다.
3. 현재 노드의 데이터가 v1, v2보다 작으면 현재 노드를
현재 노드의 왼쪽 자식으로 이동한다.
4. 현재 노드의 데이터가 v1보다 크고 v2보다 작으면(반대의 경우도 포함)
현재 노드가 v1, v2의 최소 공통 조상이다.

위 아이디어를 코드로 옮겨 작성한다.

```cpp
Node *lca(Node *root, int v1,int v2) {
        if((root -> data > v1) && (root -> data > v2))
            return lca(root -> left, v1, v2);
        else if((root -> data < v1) && (root -> data < v2))
            return lca(root -> right, v1, v2);
        else
            return root;
}
```

root를 현재 노드로 해여 계속해서 옮겨 가다가 찾게 되는 것이다.

이번 포스팅을 마친다.
