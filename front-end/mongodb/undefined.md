# 몽고디비는 데이터를 어떤식으로 저장하는가?

### How does MongoDB store data?

MongoDB 셀에서 문서를 보거나 업데이트 할 때 \
JavaScript Standard Object Notation 을 나타내는 JSON 으로 나타냅니다. \
문서가 JSON 형식으로 따르도록 하려면 문서를 중괄호로 시작하고 끝내며,\
각 키와 값을 콜론으로 구분하고, 각 키와 값 쌍을 쉼표로 구분하고, 키 주위에 따옴표를 포함해야 합니다.\
키는 MongoDB 에서 필드라고 부릅니다.



```json
// 예를 들어, 이것은 중괄호로 시작하고 끝나며, 쉼표로 구분된 키:값 쌍이 있고,
// 키가 모두 인용 부호로 둘러싸여 있고, 키와 값 사이에 콜론이 있기 때문에 유효한 JSON 입니다.

{
  "_id" : "10021-2015-ENFO",
  "result : "No Violation Issued",
}

// 그러나 아래에 있는 데이터는 그렇지 않습니다.
// 문서는 대괄호로 둘러싸여 있으며, 키는 따옴표로 묶이지 않습니다.
// 일부 코드 편집기 및 소프트웨어는 매우 관대하며, 키에 따욤표가 없을 때, 오류가 발생하지 않습니다.
// 이는 소프트웨어에 따라 다르며 올바른 JSON 이 어떻게 보이는지 100% 일치하지는 않습니다.
[
  _id : "10021-2015-ENFO",
  result : "No Violation Issued",  
]
```



### JSON vs BSON

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-28 오후 4.47.48 (1).png" alt=""><figcaption></figcaption></figure>

JSON 은 텍스트 기반 형식이며, 텍스트 구문 분석이 매우 느립니다.\
그리고 데이터베이스 문제인 공간 효율성과는 거리가 멀습니다.\
JSON 은 제한된 수의 기본 데이터 유형만 지원합니다.\
그래서 MongoDB 는 이러한 단점을 해결하기로 결정했습니다.\
데이터가 메모리에 저장되는 방식을 보면 다음과 같은 내용을 볼 수 있습니다.

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-28 오후 4.51.02.png" alt=""><figcaption><p>이런 형식을 BSON 이라고 하며 Binary JSON 이라고 부릅니다.</p></figcaption></figure>

BSON 은 속도, 공간 및 유연성에 최적화된 \
JSON 형식으로 데이터를 저장하는 이진 표현인 간격을 매우기 위해 발명되었습니다. \
목표는 고성능 및 범용 초점을 달성하는 것이었습니다.



추후 업데이트 예정



MongoDB는 내부 및 네트워크를 통해 BSON 형식으로 데이터를 저장하지\
그렇다고 해서 MongoDB 를 JSON 데이터베이스로 생각할 수 없는 것은 아닙니다.\
JSON 으로 표현할 수 있는 모든 것은 기본적으로 MongoDB 에 저장되고, JSON 에서 처럼 쉽게 검색할 수 있습니다.문서를 값으로 포함하는 필드 (Sub-Document)문서를 값으로 포함하는 필드 (Sub-Document){  "address": {    "city" : "RIDGEWOOD"
