---
layout: single
title:  "Flutter key가 왜 필요할까?"
---

이전 Flutter 동작 원리를 이해하면 key의 필요성을 알 수 있다.

# **Key에 대한 상세정보**

대부분의 경우 Key가 필요하지 않습니다. Key를 사용해도 특별한 문제가 없지만 별로 필요한 것도 아니고 불필요하게 공간만 차지할 뿐이죠. new 키워드를 사용한다거나, 혹은 변수타입을 양쪽변에 기재하는 것과 같은 불필요한 일입니다. `Map<Foo, Bar> aMap = Map<Foo, Bar>()`). 

그러나 Key를 사용하는 것이 필요할 때도 있습니다. 동일한 상태의 동일 위젯으로 구성된 컬렉션을 재정렬하거나, 추가/삭제하는 등의 작업을 한다면, 머지않아 Key를 사용하게 될 것입니다.

위젯 모음을 수정할 때 키가 필요한 이유를 설명하기 위해 버튼을 탭할 때 위치를 바꾸는 무작위로 색상이 지정된 두 개의 위젯이 있는 매우 간단한 앱을 작성했습니다.

![https://miro.medium.com/max/640/1*edgczyvaQRgGRy8yhht0QQ.gif](https://miro.medium.com/max/640/1*edgczyvaQRgGRy8yhht0QQ.gif)

앱의 상태 비저장 버전에는 각각 무작위로 생성된 색상을 가진 두 개의 상태 비저장 타일이 있고 이 타일의 위치를 저장하기 위해 

`Row`호출 `StatefulWidget`됩니다 . 하단의 아래쪽을 `PositionedTiles`탭하면 목록에서 위치가 올바르게 바뀝니다.`FloatingActionButton`

![https://miro.medium.com/max/640/1*T7TBQx9DhaQ16gbX68XxVw.gif](https://miro.medium.com/max/640/1*T7TBQx9DhaQ16gbX68XxVw.gif)

그러나 stateless 대신 `ColorfulTiles` 

*stateful* 로 만들고 색상을 상태에 저장하면 버튼을 누를 때 아무 일도 일어나지 않는 것처럼 보입니다.

참고로 위에 표시된 코드는 *버그* 가 있습니다. 사용자가 "교체" 버튼을 눌렀을 때 색상이 바뀌는 것을 표시하지 않는다는 점입니다. 이에 대한 수정은 상태 저장 위젯에 키 매개변수를 추가한 다음 위젯이 원하는 대로 위치를 바꾸는 것입니다.

*그러나 이것은 수정 중인 하위 트리에 상태 저장* 위젯이 있는 경우에만 필요 합니다. 컬렉션의 전체 위젯 하위 트리가 *stateless* 이면 키 *가* 필요하지 않습니다.

그게 다야! 이것이 Flutter에서 키를 사용하기 위해 알아야 할 기술의 전부입니다. 하지만 이 모든 것의 근본적인 이유를 이해하고 싶다면…

![https://miro.medium.com/max/640/1*3XbdhaQ9_lPfILdViiipeQ.gif](https://miro.medium.com/max/640/1*3XbdhaQ9_lPfILdViiipeQ.gif)

# ****Key가 필요한 이유****

플러터는 내부적으로 모든 위젯 마다 Element를 생성합니다. 위젯 트리를 만드는 것처럼 Element 트리도 생성합니다. Element는 단순하게 구성되어 있습니다. 대응되는 위젯의 타입 정보와 자식 Element에 대한 참조를 가지고 있습니다. Element 트리는 플러터 앱의 골격으로 볼 수 있습니다. 앱의 구조를 보여주지만 자세한 정보는 원본 위젯에 대한 참조를 통해 확인할 수 있습니다.

위 `Row`의 예에서 위젯은 기본적으로 각 자식에 대해 정렬된 슬롯 세트를 보유합니다. 에서 Tile 위젯의 순서를 바꿀 때 `Row`Flutter `ElementTree`는 골격 구조가 동일한지 확인하기 위해 이동합니다.

![https://miro.medium.com/max/1200/1*sHDIVXBu9RpJYN9Zdn8iBw.gif](https://miro.medium.com/max/1200/1*sHDIVXBu9RpJYN9Zdn8iBw.gif)

Row 위젯부터 시작해서 자식 위젯으로 이동하며 검사를 수행합니다. Element는 대응되는 위젯의 Type과 Key가 이전의 위젯과 동일한지 확인합니다. 만약 동일하다면 위젯에 대한 참조를 갱신합니다. StatelessWidget 버전에서는 Key를 가지지 않으므로 플러터는 단지 Type만 확인합니다. (만약 복잡하게 느껴지신다면 위의 그림을 참고해주세요.)

**StatefulWidget의 경우 내부적인 Element 트리 구조가 약간 다릅니다. 이전과 동일하게 Element와 Widget이 존재하고, 거기에다가 State Object도 따로 존재합니다. 색상 정보는 위젯이 아니라 State Object에 저장되게 됩니다.**

![https://miro.medium.com/max/1200/1*noTkKudlGuaAkiGaubEcNA.gif](https://miro.medium.com/max/1200/1*noTkKudlGuaAkiGaubEcNA.gif)

Stateful 타일에 Key를 지정하지 않았을 때를 생각해봅시다. 두 타일의 순서를 바꾸면 플러터는 Element 트리를 검사합니다. 먼저 Row 위젯의 Type을 검사하고, 동일하므로 참조를 업데이트 합니다. 첫번째 Tile Element도 자신의 Widget의 Type을 검사합니다. Type이 동일하므로 Widget의 참조를 업데이트합니다. 두번째 Tile Element에도 동일한 작업이 진행됩니다. 위젯의 순서가 변경되었음에도 Element 트리의 순서는 변경되지 않았고 Widget에 대한 참조만 업데이트되었습니다. 플러터는 위젯을 화면에 출력하기 위해 Element 트리와 State Object의 정보를 사용합니다. 따라서 위젯의 순서가 변경되었음에도 출력되는 화면은 변경되지 않습니다.

![https://miro.medium.com/max/1200/1*7n-u4yexzRZDEtNvbrsG1g.gif](https://miro.medium.com/max/1200/1*7n-u4yexzRZDEtNvbrsG1g.gif)

버그를 수정하기 위해 Stateful 타일에 Key 값을 추가한 경우에는 다르게 동작합니다. 위젯의 순서를 변경하면 Row 위젯은 이전과 동일하게 Type 검사를 진행합니다. Tile Element에서는 Element의 Key 값과 Widget의 Key 값을 비교하게 되고 서로 다르다는 것을 발견합니다. 플러터는 불일치가 발견된 해당 TileElement를 비활성화하고 Element 트리에서 삭제합니다.

![https://miro.medium.com/max/1200/1*AcBxC8IF_irZpFARt-Nqyw.gif](https://miro.medium.com/max/1200/1*AcBxC8IF_irZpFARt-Nqyw.gif)

그 다음 플러터는 불일치가 발견된 Row의 자식 Widget들을 탐색하면서 Key가 일치하는 Element를 찾기 시작합니다. 일치하는 것을 찾으면 해당 Widget으로 참조를 업데이트 합니다. 이제 플러터는 우리가 기대하는 결과를 보여줄 것입니다. 버튼을 누를 때마다 위젯의 위치를 변경하고 색상을 업데이트하게 됩니다.

**요약하면 컬렉션에 포함된 Stateful 위젯의 순서나 개수를 변경할 때 Key가 중요한 역할을 합니다. 예제에서는 상태를 설명하기 위해 색상을 사용했지만, 실제상황에서 상태란 훨신 미묘합니다. 애니메이션 재생, 사용자가 입력한 정보, 스크롤 위치 등이 모두 상태와 관련이 있습니다.**

[출처] : [https://nsinc.tistory.com/214](https://nsinc.tistory.com/214)