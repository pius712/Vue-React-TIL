# Immutable

Redux의 store state는 `immutable`이다. 

immutable 객체를 state로 쓰는 이유는 무엇일까?  


그 이유는 store의 state를 참조하는 컴포넌트는 state가 변하면 Re-rendering 되는 것으로부터 시작한다.
state가 immutable하다면, 그 state를 업데이트 하는 방법은 새로운 객체를 만들어주는 것이다. 이렇게하면, 메모리가 참조하는 주소가 바뀌기 때문에 비교적 쉽게 state의 업데이트 유무를 알 수 있게 되는 것이다.  

이러한 성질때문에, redux의 state는 