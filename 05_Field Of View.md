# Field Of View

* 카메라의 시야각을 의마
* `Camear.fieldOfView`로 값을 가져오거나 변경가능
* `perspective `모드에서만 적용
* `projectionMatrix`를 변경했을 경우, `ResetProjectionMatrix`를 호출하기 전까진 `fieldOfView`를 이용해서 렌더링하지 않음
* 카메라의 시야각을 줄이거나 늘린 상태에서 화면에 맞추면 자연스럽게 줌인, 줌아웃 효과를 얻을 수 있음
  * 카메라를 이동해서 보여지는 결과와는 다르게 보여짐

