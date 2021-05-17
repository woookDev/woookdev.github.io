## [iOS] UIView animation cancel ( 애니메이션 중간에 취소하기 )

UIView에서 animation을 구현하다 보면 특정 interaction에 따라 애니메이션이 취소되어야 하는 까다로운 요구사항을 마주하게 될 수 있다. 그런 경우 Property Animation을 쓰는 경우가 아니라면 너무나 쉽게 closure block을 사용하여 animation을 구현해왔기에 어떻게 취소를 해야 할 지 떠오르지 않을 수 있다.