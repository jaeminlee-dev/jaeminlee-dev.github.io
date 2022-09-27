---
title:  "FireStore를 공부해보자"
categories:
  - 개발 정보
tags:
  - Firebase
  - FireStore
toc: true
toc_sticky: true
toc_label: "GITHUB BLOG JEKYLL"
toc_icon: "bookmark"
---

FireStore는 Firebase문서에서 말했듯이 Firebase를 쓰는 가장 큰 이유다.

## 시작 방법

[https://firebase.google.com/docs/firestore/quickstart?hl=ko](https://firebase.google.com/docs/firestore/quickstart?hl=ko) - 해당 링크 참조. 

## FireStore의 특징

collection, document로 이루어져 있다. 자세한 것은 [데이터 모델 링크](https://firebase.google.com/docs/firestore/data-model?hl=ko)

즉 collection - doc - collection - doc 형태로 계속 이어진다는 것을 이해하면 쉽다.

[FireStore에서 지원하는 데이터 유형](https://firebase.google.com/docs/firestore/manage-data/data-types?hl=ko)

[데이터 구조 선택시 참고할 사항](https://firebase.google.com/docs/firestore/manage-data/structure-data?hl=ko)

## 공식 문서에서 지원하는 CRUD

### C(Create)

```
db = FirebaseFirestore.instance;

// Create a new user with a first and last name
final user = <String, dynamic>{
  "first": "Ada",
  "last": "Lovelace",
  "born": 1815
};

// Add a new document with a generated ID
db.collection("users").add(user).then((DocumentReference doc) =>
    print('DocumentSnapshot added with ID: ${doc.id}'));
```

<String, dynamic> 형식의 user 변수 를 만든 다음에 

DocumentReference 클래스를 상속받는 doc를 생성한다. 그리고 doc.id를 출력

여기처럼 doc 아이디를 따로 설정 안해주면 Firebase에서 자동으로 doc.id를 생성해준다.

위에 코드는 컬렉션이 없으면 생성해주고, user를 Firestore에 등록했다.

### [DocumentReference](https://firebase.google.com/docs/reference/android/com/google/firebase/firestore/DocumentReference) ?

DocumentReference는 데이터베이스의 문서 위치를 나타내고 위치를 쓰거나, 수신할수도 있다. 이 클래스 안에 여러 메소드들이 있는데, 리스너를 등록해서 값이 변경될 때마다 출력을 받을 수 도 있다.

만약 단일 doc를 만들거나(Create) 덮어쓰려면(Update) Set()메서드를 사용하면 된다. 

이것도 동일하게 doc 아이디를 따로 설정 안해주면 Firebase에서 자동으로 doc.id를 생성해준다.

add(...)와 .doc().set(...)은 완전히 동일하게 작동한다고 한다.

## set 예제

```
db
    .collection("cities")
    .doc("LA")
    .set(city)
    .onError((e, _) => print("Error writing document: $e"));
```

만약 문서를 업데이트 하는데 기존의 내용하고 병합(새로운 데이터는 추가, 기존 내용은 유지, 중복되는거 있으면 적용 안함)되게 하려면 어떻게 할까? 다음과 같다

```
// Update one field, creating the document if it does not already exist.
final data = {"capital": true};

db.collection("cities").doc("BJ").set(data, SetOptions(merge: true));
```

근데 매번 이렇게 userType을 생성하고 넣어주는게 불편하지 않는가? 만약에 게임을 하나 만든다고 가정해보자

유저 1000명을 추가해야하는데 똑같은 객체를 계속 코드를 짜면 불편할 것이다.

그래서 Firebase는 커스텀 객체를 지원한다.

아래는 커스텀 객체(City) 모델을 만들고 적용한 코드이다.

### City 모델 생성

```
class City {
  final String? name;
  final String? state;
  final String? country;
  final bool? capital;
  final int? population;
  final List<String>? regions;

  City({
    this.name,
    this.state,
    this.country,
    this.capital,
    this.population,
    this.regions,
  });

  factory City.fromFirestore(
    DocumentSnapshot<Map<String, dynamic>> snapshot,
    SnapshotOptions? options,
  ) {
    final data = snapshot.data();
    return City(
      name: data?['name'],
      state: data?['state'],
      country: data?['country'],
      capital: data?['capital'],
      population: data?['population'],
      regions:
          data?['regions'] is Iterable ? List.from(data?['regions']) : null,
    );
  }

  Map<String, dynamic> toFirestore() {
    return {
      if (name != null) "name": name,
      if (state != null) "state": state,
      if (country != null) "country": country,
      if (capital != null) "capital": capital,
      if (population != null) "population": population,
      if (regions != null) "regions": regions,
    };
  }
}

```

### City 모델 적용

```
final city = City(
  name: "Los Angeles",
  state: "CA",
  country: "USA",
  capital: false,
  population: 5000000,
  regions: ["west_coast", "socal"],
);
final docRef = db
    .collection("cities")
    .withConverter(
      fromFirestore: City.fromFirestore,
      toFirestore: (City city, options) => city.toFirestore(),
    )
    .doc("LA");
await docRef.set(city);
```

훨씬 줄지 않았는가? 이렇게 모델을 만들면은 함수 재활용 할 때 유용하다.

## R(Read)

문서 읽기는 3가지 방식으로 구현할 수 있다.

- 메서드를 호출하여 데이터를 한 번 가져오기
- 데이터 변경 이벤트를 수신하는 리스너를 설정합니다.
- [데이터 번들](https://firebase.google.com/docs/firestore/bundles?hl=ko)을 통해 외부 소스에서 Firestore 스냅샷 데이터를 일괄 로드합니다. (아직 안써봄)

## 문서 가져오기

다음 예시에서는 `get()`을 사용하여 단일 문서의 내용을 검색하는 방법을 보여줍니다.

```
final docRef = db.collection("cities").doc("SF");
docRef.get().then(
  (DocumentSnapshot doc) {
    final data = doc.data() as Map<String, dynamic>;
    // ...
  },
  onError: (e) => print("Error getting document: $e"),
);firestore.dart

```

## 커스텀 객체로 만든 것을 가져오기

```
final ref = db.collection("cities").doc("LA").withConverter(
      fromFirestore: City.fromFirestore,
      toFirestore: (City city, _) => city.toFirestore(),
    );
final docSnap = await ref.get();
final city = docSnap.data(); // Convert to City object
if (city != null) {
  print(city);
} else {
  print("No such document.");
}
```

## 컬렉션에서 여러 문서 가져오기(쿼리 사용)

컬렉션의 문서를 쿼리하여 하나의 요청으로 여러 문서를 검색할 수도 있습니다. 예를 들어 `where()`를 사용하여 특정 조건을 충족하는 모든 문서를 쿼리하고 `get()`을 사용하여 결과를 가져올 수 있습니다.

where 옵션을 빼면 모든 문서를 가져올 수 있습니다

```
db.collection("cities").where("capital", isEqualTo: true).get().then(
      (res) => print("Successfully completed"),
      onError: (e) => print("Error completing: $e"),
    );firestore.dart

```

기본적으로 Cloud Firestore는 쿼리를 만족하는 모든 문서를 문서 ID에 따라 오름차순으로 검색하지만, 반환되는 [데이터를 정렬하고 제한](https://firebase.google.com/docs/firestore/query-data/order-limit-data?hl=ko)할 수 있습니다.

## U(Update)

## 문서 업데이트

전체 문서를 덮어쓰지 않고 문서의 일부 필드를 업데이트하려면 `update()` 메서드를 사용합니다.

```
final washingtonRef = db.collection("cites").doc("DC");
washingtonRef.update({"capital": true}).then(
    (value) => print("DocumentSnapshot successfully updated!"),
    onError: (e) => print("Error updating document $e"));firestore.dart

```

## D(Delete)

## 문서 삭제

문서를 삭제하려면 `delete()` 메서드를 사용한다.
 주의 : 문서를 삭제해도 하위 컬렉션은 삭제되지 않는다. 

즉, 예를 들어 `/mycoll/mydoc`에서 상위 문서를 삭제했더라도 `/mycoll/mydoc/mysubcoll/mysubdoc`
 경로에 있는 문서에 액세스할 수 있는 것이다. 이를 방지하기 위해서는 자세한  [컬렉션 삭제](https://firebase.google.com/docs/firestore/manage-data/delete-data?hl=ko#collections)를 참조.

```
db.collection("cities").doc("DC").delete().then(
      (doc) => print("Document deleted"),
      onError: (e) => print("Error updating document $e"),
    );firestore.dart

```

## 필드 삭제

문서에서 특정 필드를 삭제하려면 문서를 업데이트할 때 `FieldValue.delete()` 메서드를 사용합니다.

```
final docRef = db.collection("cities").doc("BJ");

// Remove the 'capital' field from the document
final updates = <String, dynamic>{
  "capital": FieldValue.delete(),
};

docRef.update(updates);firestore.dart

```

## 컬렉션 삭제

Firestore에서는 유저 인터페이스 단에서의 컬렉션 삭제를 지원하지 않는다. 

그 이유는 Delete 명령 하나에 모든 하위 Collection의 문서들을 삭제하게될 경우, 엄청나게 방대한 양의 데이터 처리가 될 수 있기 때문이라고 한다…

여러 하위 컬렉션 삭제를 방법을 찾아봤지만 직접 삭제 말고는 따로 없었다ㅠ

---

## 메모장 앱을 제작하면서 느낀점 & 알게 된 점

- timestamp를 사용해서 문서 수정 시간을 적용할 수 있었다
- CRUD중에 Firestore에서 지원하는 R, U, D를 사용하기 위해서는 doc의 이름을 알아야했다.
    
    근데 자동 id로 R, U, D를 사용하려면은 지금 출력된 리스트의 문서 id를 알고 넘겨줘야 하는데 여기에서 오래걸렸다.
    
    해결한 방법은 아래와 같다.
    
    비동기 스냅샵(문서 수정시 바로 표시되게 하기 위함)을 지원하는 콜렉션 쿼리 snapshot을 생성
    
    해당 쿼리 스냅샷의 문서를 리스트뷰에 넣고 이런 식으로 표현해서 구현했는데
    
    ```dart
    snapshot.data!.docs.map((DocumentSnapshot document).list
    ```
    
     
    
    FireStore에서 documentsnapshot을 이용해 .data형식으로 받아오고 이것을 map 형식으로 반환시켜야 하기 때문이다.
    
    이 반환한 내용을.list화 시켜서 ListView에 사용할 수 있게 만들어서 표현한 것이다.
    

![Untitled](FireStore%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%A9%E1%86%BC%E1%84%87%E1%85%AE%E1%84%92%E1%85%A2%E1%84%87%E1%85%A9%E1%84%8C%E1%85%A1%200d49c3a032754a4c9843ea18eaac7e71/Untitled.png)

- 처음에는 title을 doc.id로 설정하고 가져갈려했는데 이런 경우 중복된 동일 title문서를 생성하면 1개만 생성되는 문제가 있었고,
- timestamp를 이용해서 문서 등록 시간으로 doc.id를 만들려고 했는데 이런 경우 Update 한 다음에 doc.id를 불러올 수 없었다.(최신화된 timestamp와 지금 출력된 doc.id를 맞출 수 없기 때문)
    
    여기에서 doc.id를 수정해서 업데이트 한다는 선택지도 있었지만 찾아보니 이것은 좋은 방법이 아니라고 한다. (한번 지정된 doc.id를 수정하는 것은 새로 리소스를 생성하고, 기존 것을 삭제하기 때문, 또한 참조 기능이 늘면은 충돌할 가능성도 높아진다)
    
- 아니면 전체 쿼리를 받아서 length를 얻은 뒤에 이것을 토대로 문서 번호를 만들어서 doc를 설정할까도 했는데 이 과정에서 DocumentSnapshot사용법을 알아서 해결했다. (이렇게 구현해도 되겠지만, 직접 참조하는 것보다는 문서 제목과 내부에 문서 번호라는 옵션이 추가되어서 싫었다)
- 하지만 문서 id가 지정되지 않은 것은 리소스 관리에 좋을지는 몰라도 불편하다..! 리스트 뷰 같은 경우에는 스냅샷을 이용해서 어찌저찌 됬지만 만약 다른 구현 방법이 필요할 때 CRUD가 힘들지도 모른다.
    
    만약 문서 스냅샷을 이용하지 못하는 경우가 있다면 
    
    컬렉션 전체 읽기에.where(is equal to : ) 옵션으로 해당 쿼리 스냅샷을 받아 온 뒤에(1개의 문서뿐이더라도 자료형이 쿼리 스냅샷이다)
    
    이것을 for 문을 돌려 리스트에 각각 넣어주고 
    
    이 doc의 Data를 추출해서 출력해야한다..
    

- 예전에 다른 프로젝트에서 이 플러그인을 사용해서 편했던 기억이 있어서 다시 시도 했는데 FireStore에 적용하기가 불편하더라.. 무엇보다 json형식으로 timestamp를 반환하지 못하는게 컷다.

```
json_serializable: ^6.4.0
```

## 메모장 기능 간단 소개

page 구성 : 

### home

(메모 내용 리스트로 간단 요약 출력

리스트 각 클릭 → 인자들 넘겨주고 수정 페이지 이동), 
(메모 추가 버튼 → memoAdd page 이동), 

새로고침(비동기로 받아서 필요없어서 테스트 페이지 만들고 썻다)

### addmemo

메모 추가 페이지 (문서 값 받아서 Create)

### editmeemo

메모 편집 페이지 (문서 값 받아서 수정 및 삭제)

![Untitled](FireStore%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%A9%E1%86%BC%E1%84%87%E1%85%AE%E1%84%92%E1%85%A2%E1%84%87%E1%85%A9%E1%84%8C%E1%85%A1%200d49c3a032754a4c9843ea18eaac7e71/Untitled%201.png)

![Untitled](FireStore%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%A9%E1%86%BC%E1%84%87%E1%85%AE%E1%84%92%E1%85%A2%E1%84%87%E1%85%A9%E1%84%8C%E1%85%A1%200d49c3a032754a4c9843ea18eaac7e71/Untitled%202.png)

![Untitled](FireStore%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%A9%E1%86%BC%E1%84%87%E1%85%AE%E1%84%92%E1%85%A2%E1%84%87%E1%85%A9%E1%84%8C%E1%85%A1%200d49c3a032754a4c9843ea18eaac7e71/Untitled%203.png)