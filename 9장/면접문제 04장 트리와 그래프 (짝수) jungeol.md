# 4. 트리와 그래프
### 4.2 최소트리:
오름차순으로 정렬된 배열이 있다. 이 배열 안에 들어 있는 원소는 정수이며 중복된 값이 없다고 했을 때 높이가 최소가 되는 이진 탐색 트리를 만드는 알고리즘을 작성하라.

```javascript
class TreeNode {
    constructor(data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}

function solution(array) {
    return createMinimalBST(array, 0, array.length - 1);
}

function createMinimalBST(arr, start, end) {
    if (end < start) {
        return null;
    }
    const mid = Math.floor((start + end) / 2);
    const n = new TreeNode(arr[mid]);
    n.left = createMinimalBST(arr, start, mid - 1);
    n.right = createMinimalBST(arr, mid + 1, end);
    return n;
}
```

### 4.4 균형확인:
이진 트리가 균형 잡혀있는지 확인하는 함수를 작성하라. 이 문제에서 균형잡흔 트리란 모든 노드에 대해서 왼쪽 부분 트리의 높이와 오른쪽 부분 트리의 높이의 차이가 최대 하나인 트리를 의미한다.

```javascript
let flag;

function solution(tree) {
    flag = true;
    checkBST(tree);
    return flag;
}

function checkBST(node) {
    if (node === null) {
        return 0;
    }
    const leftCount = checkBST(node.left);
    const rightCount = checkBST(node.right);
    flag = flag && Math.abs(leftCount - rightCount) <= 1;
    //console.log(node.data, ':', leftCount, rightCount, flag);
    return Math.max(leftCount + rightCount) + 1;
}
```

### 4.6 후속자: 
이진 탐색 트리에서 주어진 노드의 '다음' 노드(중위 후속자(in-order successor))를 찾는 알고리즘을 작성하라. 각 노드에는 부모 노드를 가리키는 링크가 존재한다고 가정하자.

- 중위 순회는 "왼쪽->현재 노드->오른쪽" 순이다.
- 노드의 오른쪽이 있으면, 다음 순서는 노드의 오른쪽 부분 트리에서 가장 왼쪽 노드가 될것이다.
- 그렇지 않다면, 부모를 봐야함.
    - 부모 입장에서, 현재 노드가 왼쪽에 있으면 다음에 방문할 노드는 부모가 됨
    - 그렇지 않다면, 부모의 부모로 올라가야함.
    - 이것을 계속 반복
```javascript
function solution(n) {
    if (n === null) {
        return null;
    }
    /* 오른쪽 자식이 존재 -> 오른쪽 부분 트리에서 가장 왼쪽 노드를 반환한다. */
    if (n.right !== null) {
        return leftMostChild(n.right);
    } else {
        let q = n;
        let x = q.parent;
        // 오른쪽이 아닌 왼쪽에 있을 때까지 위로 올라간다.
        while (x !== null && x.left !== q) {
            q = x;
            x = x.parent;
        }
        return x;
    }
}

function leftMostChild(n) {
    if (n === null) {
        return null;
    }
    while (n.left !== null) {
        n = n.left;
    }
    return n;
}
```

### 4.8 첫번째 공통 조상:
이진 트리에서 노드 두 개가 주어졌을 때 이 두 노드의 첫번째 공통 조상을 찾는 알고리즘을 설계하고 그 코드를 작성하라. 자료구조내에 추가로 노드를 저장해 두면 안 된다. 반드시 이진 탐색 트리일 필요는 없다.

각자의 depth를 맞춘 후 한단계씩 올라가면서 parent가 맞는지 체크함(Tree 자료구조에 Parent가 필요함)
```javascript
function solution(n1, n2) {
    const diff = getDepth(n1) - getDepth(n2);
    let high = diff > 0 ? n1 : n2;
    let low = diff > 0 ? n2 : n1;

    const absDiff = Math.abs(diff);
    for (let i = 0; i < absDiff; i++) {
        high = high.parent;
    }
    
    while (high.parent !== null && low.parent !== null) {
        if (high.parent === low.parent) {
            return high.parent;
        }
        high = high.parent;
        low = low.parent;
    }

    return null;
}

function getDepth(node) {
    let n = node;
    let depth = 0;
    while (n !== null) {
        depth += 1;
        n = n.parent;
    }
    return depth;
}
```

### 4.10 하위 트리 확인:
두 개의 커다란 이진트리 T1과 T2가 있다고 하자. T1이 T2보다 훨씬 크다고 했을 때, T2가 T1의 하위 트리(subtree)인지 판별하는 알고리즘을 만들라. T1 안에 있는 노드 n의 하위 트리가 T2와 동일하면, T2는 T1의 하위트리다. 다시말해, T1에서 노드 n의 아래쪽을 끊어 냈을 때 그 결과가 T2와 동일해야한다.

1. 전위 탐색과 Null노드를 표현하는 String을 만든뒤 비교하는 방법
    - t1의 노드 수: n / t2의 노드 수: m
    - 시간복잡도: O(n + m);
    - 공간복잡도: O(n + m);
```javascript
function containsTree(t1, t2) {
    const s1 = getTreeValueString(t1);
    const s2 = getTreeValueString(t2);

    return s1.indexOf(s2) !== -1;
}

function getTreeValueString(tree) {
    const arr = [];
    treeValueString(tree, arr);
    return arr.join('');
}

function treeValueString(node, arr) {
    if (node === null) {
        arr.push('n');
        return;
    }
    arr.push(node.data);
    treeValueString(node.left, arr);
    treeValueString(node.right, arr);
}
```

2. 큰 트리를 순회하면서 작은 트리와 data가 일치하는 경우만 비교하는 방법
    - t1의 노드 수: n / t2의 노드 수: m / t1의 값과 t2의 root 값이 같은 경우의 수: k
    - 시간복잡도: O(n + km);
    - 공간복잡도: O(log n + log m);
    - 공간복잡도는 확실히 우월함.
    - t2의 root의 값과 t1의 값이 같을 확률은 적음(0 ~ p 사이의 값일경우에 1/p 확률)
    - 또한 위의 조건이라도, 두 부분간에 차이점을 금방발견하게 되므로 결과적으로 빨리끝나게 됨
```javascript
function containsTree(t1, t2) {
    if (t2 === null) {
        return true;
    }
    return subTree(t1, t2);
}

function subTree(t1, t2) {
    if (t1 === null) {
        return false;
    } else if (t1.data === t2.data && matchTree(t1, t2)) {
        return true;
    }

    return subTree(t1.left, t2) || subTree(t1.right, t2);
}

function matchTree(t1, t2) {
    if (t1 === null && t2 === null) {
        return true; // 왼쪽에 하위 트리가 없다.
    } else if (t1 === null || t2 === null) {
        return false; // 트리가 완전히 비어 있을때, 따라서 둘이 같지 않을 때
    } else if (t1.data !== t2.data) {
        return false; // 값이 다를 때
    } else {
        return matchTree(t1.left, t2.left) && matchTree(t2.right, t2.right);
    }
}
```

### 4.12 합의 경로:
각 노드의 값이 정수(음수 및 양수)인 이진 트리가 있다. 이때 정수의 합이 특정 값이 되도록 하는 경로의 개수를 세려고 한다. 경로가 꼭 루트에서 시작해서 말단 노드에서 끝날 필요는 없지만 반드시 아래로 내려가야 한다. 즉, 부모 노드에서 자식노드로만 움직일 수 있다. 알고리즘을 어떻게 설계할 것인가?

1. 무식한 방법: 노드를 하나씩 순회하며, 각 노드마다 모든 경로의 수를 체크함
    - 시간복잡도: O(NlogN)

2. 