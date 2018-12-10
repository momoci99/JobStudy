## 04. 트리와 그래프(홀수)

####  4.1 노드 사이의 경로 : 방향 그래프가 주어졌을 때 두 노드 사이에 경로가 존재하는지 확인하는 알고리즘을 작성하라.

풀이

       너비 우선 탐색 or 깊이 우선 탐색을 사용하여 풀 수 있다.
       두 노드 가운데 하나를 고르고 탐색 도중 다른 노드가 발견되는지 검사하면 됨.


       


```java

//너비 우선 탐색으로 구현된 코드
enum State {Unvisited, Visited, Visiting}

boolean search(Graph g, Node start, Node end){
    
    if (start == end) return true;

    //Queue처럼 동작한다.
    LinkedList q = new LinkedList();

    for(Node u : g.getNodes()){
        u.state = State.Unvisited;
    }
    start.state = Visiting;
    q.add(start); //시작노드를 큐에 넣는다.
    Node u;
    while(!q.Empty()){
        //큐의 첫번째 노드를 뽑아낸다
        u = q.removeFirst();
        if(u != null){
            //인접 노드를 찾는다.
            for (Node v:u.getAdjacent()){

                //방문하지 않은노드이며
                if(v.state == State.Unvisited){

                    //지정한 노드와 현재 노드가 같으면
                    if(v == end){

                        //true를 리턴
                        return true;
                    } else {
                        v.state = State.Visiting;
                        q.add(v);
                    }
                }
            }
            u.state = State.Visited;
        }
    }
    return false;

}


    

```
    다른 풀이 : 대상 트리의 인접행렬을 만들고 해당 인접행렬을 탐색한다.





####  4.3 깊이의 리스트 : 이진 트리가 주어졌을 때 같은 깊이에 있는 노드를 연결리스트로 연결해 주는 알고리즘을 설계하라. 즉 트리의 깊이가 D라면 D개의 연결리스트를 만들어야 한다.

    풀이 1 전위순회방식 + 깊이우선탐색방식

> O(N)

```java

//전위순회방식 + 깊이우선탐색방식
void createLevelLinkedList (TreeNode root, ArrayList<LinkedList<TreeNode>> lists, int level){

    if(root == null) return;

    LinkedList list = null;
    if(lists.size() == level){
        list = new LinkedList();
        //깊이가 증가하는 순서로 순회했다는 사실에 유의
        //깊이 #i를 처음 마주쳤다면, 0부터 i-1번째 까지는 이전에 이미
        //lists에 추가되어 있어야한다.
        //따라서 새로운 깊이 #i를 lists의 끝에 추가해도 안전하다.

        list.add(list);
    }
    else {
        list = lists.get(level);
    }
    //전위순회 방식식이므로
    //루트
    //왼쪽 서브 트리
    //우측 서브 트리 
    //순으로 순회
    list.add(root);
    createLvelLinkedList(root.left, lists, level + 1);
    createLvelLinkedList(root.right, lists, level + 1);
}

ArrayList<LinkedList<TreeNode>> createLevelLinkedList(TreeNode root){
    ArrayList<LinkedList<TreeNode>> lists = new ArrayList();
    createLevelLinkedList(root, lists, 0);
    return lists;
}


```
    풀이 2 너비 우선 탐색

> O(N)

```java
ArrayList<LinkedList<TreeNode>> createLevelLinkedList(TreeNode root){
    ArrayList<LinkedList<TreeNode>> result = new ArrayList();

    //루트 방문
    LinkedList current = new LinkedList();
    if (root != null){
        current.add(root);
    }

    while(current.size() > 0){
        result.add(current);//이전 깊이 추가
        LinkedList parents = current;//다음 깊이 진행
        current = new LinkedList();
        for(TreeNode parent : parents){
            //자식 노드들 방문
            if(parent.left != null){
                current.add(parent.left);
            }
            if(parent.right != null){
                current.add(parent.right);
            }

        }
    }
}

```

####  4.5 BST검증 : 주어진 이진트리가 이진탐색트리인지 확인하는 함수를 작성하라.

    풀이 1. 전위 순회의 특징을 이용
    - 이진탐색트리를 전위 순회하면 결과가 정렬된 상태이다.
    - 정렬되어있는지만 확인하면 됨.


```java
    Integer last_printed = null;
    boolean checkBST(TreeNode n){
        if (n == null) return true;

        //왼쪽을 재귀적으로 검사
        if (!checkBST(n.left)) return false;

        //현재 노드 검사
        //현재 노드가 이전값보다 작거나 크면 false를 리턴함.
        if(last_printed != null && n.data <= last_printed){
            return false;
        }
        last_printed = n.data;

        //오른쪽을 재귀적으로 검사
        if(!checkBST(n.right)) return false;

        return true;
    }

```

    풀이 2. 최소 / 최대 기법

```java
boolean checkBST(TreeNode n){
    return checkBST(n, null, null);
}

boolean checkBST(TreeNode n, Integer min, Integer max){
    if (n == null){
        return true;
    }
    if ((min != null) && n.data <= min) || (max != null && n.data > max)){
        return false;
    }
    if(!checkBST(n.left, min, n.data) || !checkBST(n.right, n.data, max)){
        return false;
    }
}


```



####  4.7 순서정하기 : 프로젝트의 리스트와 프로젝트들 간의 종속 관계(즉, 프로젝트 쌍이 리스트로 주어지면 각 프로젝트 쌍에서 두 번째 프로젝트가 첫 번째 프로젝트에 종속되어 있다는 뜻)가 주어졌을 때, 프로젝트를 수행해 나가는 순서를 찾으라. 유효한 순서가 존재하지 않으면 에러를 반환한다.





#### 4.9 BST 수열 : 배열의 원소를 왼쪽에서부터 차례로 트리에 삽입함으로써 이진 탐색 트리를 생성할 수 있다. 이진 탐색 트리 안에서 원소가 중복되지 않는다고 할 때, 해당 트리를 만들어 낼 수 있는 모든 가능한 배열을 출력하라.

> 예제를 사용하여 푸는것이 현명 - 책 참고

1. 배열의 첫번째 요소는 루트 노드가 된다.
2. 루트 원소가 삽입된 이후의 자식노드들은 왼쪽, 오른쪽 삽입 순서와 상관없이 이진탐색트리의 규칙인 '왼쪽노드 value < 현재노드 value < 오른쪽 노드 value를 지키며 삽입된다. 즉 누가먼저 삽입되던 상관없다는 뜻이다.


```java
ArrayList<LinkedList<Integer>> allSequences(TreeNode node){
    ArrayList<LinkedList<Integer>> result = new ArrayList();

    if(node == null){
        result.add(new LinkedList());
        return result;
    }

    LinkedList<Integer> prefix = new LinkedList();
    prefix.add(node.data);

    //왼쪽과 오른쪽 부분 트리에 대한 재귀
    ArrayList<LinkedList<Integer>> leftSeq = allSequences(node.left);
    ArrayList<LinkedList<Integer>> rightSeq = allSequences(node.right);

    //왼쪽과 오른쪽 결과 리스트를 엮어 하나로 만들기
    for(LinkedList left:leftSeq){
        for(LinkedList righ : rightSeq){
            ArrayList<LinkedList<Integer>> weaved = 
                new ArrayList<LinkedList<Integer>>();
            weaveLists(left, right, weaved, prefix);
            result.addAll(weaved);
        }
    }
    return result;
}






```






 
#### 4.11 임의의 노드 : 이진 트리 클래스를 바닥부터 구현하려고 한다. 노드의 삽입, 검색, 삭제뿐만 아니라 임의의 노드를 반환하는 getRandomNode() 메서드도 구현한다. 모든 노드를 같은 확률로 선택해주는 getRandomNode 메서드를 설계하고 구현하라. 또한 나머지 메서드는 어떻게 구현할지 설명하라.


