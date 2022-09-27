---
layout: single
title:  "트리구조란"
---

트리구조란?(Tree Structure)각 요소가 바로 아래에 있는 하나 이상의 요소에 연결되는 데이터 구조 유형입니다.
1개 이상의 노드를 갖는 노드들의 집합요소 간의 연결을 말하며, 데이터 구조에서 다루는 트리는 일반적으로 꼭대기에 뿌리가 그려져 있으므로 거꾸로 된 트리 라고도 합니다.

소프트웨어 개발을 하면서 자연스레 공부하게 되는 데이터에는 큐(Queue), 버퍼(Buffer), 트리(Tree)등이 있습니다. 큐나 버퍼는 주로 메모리 상에서 관리되는 자료구조의 개념이기 때문에 어렵기도 하고 잘 모르지만, 트리구조는 사용자에게 보여지는 용도로도 많이 쓰이는 개념이어서 사용하는 경우가 많습니다. 그런 만큼 공부가 필요하겠죠?

트리구조는 우리 주위에서 쉽게 접할수 있습니다. 예를 들자면 월드컵 토너먼트 대진표, 집안의 가계도, 회사의 조직도, 윈도우 탐색기 폴더구조와 같은 것들이죠. 개발자들에게는 클래스 하이어라키(Hierarchy)도 트리구조이기 때문에 많이들 익숙하게 다뤄왔을 겁니다.

여기서 트리구조에 대해 설명하는 이유는 프로젝트 중 자주 구현하게 되는 조직도 및 Top Down(특정노드 하위의 모든 노드 가져오기), Bottom Up(특정노드 상위의 모든노드 가져오기)의 구현을 위해 필수적인 트리구조에 대한 이해를 돕기 위함이며, 이 글에서는 그에 맞춰 요약 설명하고 있습니다.

## 트리구조의 특징

1. 루트가 존재한다.
2. 순환구조를 갖지 않는 비방향성 연결그래프 구조이다.(각 노드들은 루트아래로 각각의 부속트리를 이루며, 순환구조를 띄고있지 않다.)
글로 설명하면 이해가 어렵지만 다음의 그림을 보시면 쉽게 이해가 가실겁니다.

### 그림1. 트리구조

부모노드(Parent Node) : A는 B의 부모노드입니다. B는 C, E, F의 부모노드입니다.자식노드(Child Node) : B는 A의 자식노드입니다. C, E, F는 B의 자식노드입니다.

[https://t1.daumcdn.net/cfile/tistory/99CC53435C0E614B2B](https://t1.daumcdn.net/cfile/tistory/99CC53435C0E614B2B)

- [노드](https://namu.wiki/w/%EB%85%B8%EB%93%9C)(node): 트리를 구성하는 기본 원소
    - 루트 노드(root node/root): 트리에서 부모가 없는 최상위 노드, 트리의 시작점
    - 부모 노드(parent node): 루트 노드 방향으로 직접 연결된 노드
    - 자식 노드(child node): 루트 노드 반대방향으로 직접 연결된 노드
    - 형제 노드(siblings node): 같은 부모 노드를 갖는 노드들
    - 리프 노드(leaf node/leaf): 루트 노드를 제외한 차수가 1인 정점을 뜻한다. 쉽게 말해 자식이 없는 노드. 단말 노드라 부르기도 한다.
- 경로(path): 한 노드에서 다른 한 노드에 이르는 길 사이에 있는 노드들의 순서
- 길이(length): 출발 노드에서 도착 노드까지 거치는 간선의 개수
- 깊이(depth): 루트 경로의 길이
- 레벨(level): 루트 노드(level=0)부터 노드까지 연결된 간선 수의 합
- 높이(height): 가장 긴 루트 경로의 길이
- 차수(degree): 각 노드의 자식의 개수[[3]](https://namu.wiki/w/%ED%8A%B8%EB%A6%AC(%EA%B7%B8%EB%9E%98%ED%94%84)#fn-3)
- 트리의 차수(degree of tree): 트리의 최대 차수 = \max(\deg_1, \deg_2, \cdots, \deg_n)max(deg1,deg2,⋯,deg*n*)
- 크기(size): 노드의 개수
- 너비(width): 가장 많은 노드를 갖고 있는 레벨의 크기
- 내부 정점(internal vertex): 차수가 2 이상인 정점을 뜻한다.
- 포레스트(forest): 서로 [독립](https://namu.wiki/w/%EB%8F%85%EB%A6%BD)인 트리들의 모임이다.
- 방향 트리(directed tree): 방향을 무시하고 생각했을 때 트리인 유향 그래프는 방향 트리이다. 자료구조의 트리는 방향 트리의 일종이다.[[4]](https://namu.wiki/w/%ED%8A%B8%EB%A6%AC(%EA%B7%B8%EB%9E%98%ED%94%84)#fn-4)

[](https://namu.wiki/w/%ED%8A%B8%EB%A6%AC(%EA%B7%B8%EB%9E%98%ED%94%84))