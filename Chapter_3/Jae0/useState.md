💡 State ? 리액트에서 component 가 지니고 있는 상태를 의미함

```jsx
const [state, setState] = useState(initailState);
const [num, setNum] = useState(0);
```

내부의 state는 원하는 이름으로 변경 시켜 줄 수 있음

state

현재의 상태값은 배열 첫번 째 아이템인 state 내부에 들어있음

setState

상태를 변경 시켜주고싶을때 사용할 수 있는 함수

initailState

해당 state의 초기값을 전달할 수 있음

만약 전달된 초기값이 없다면 `undefined`

- setState 를 이용해 state를 변경시켜주면 해당 component 는 다시 한 번 렌더링이 진행됨
- state를 사용하지 않고 내부에 변수를 통해 상태를 관리하게된다면, 매번 렌더링이 다시 이루어질때
  해당 함수형 component는 매번 실행되어지고 내부 변수의 변화된 값은 매번 초기화됨
- state가 자신의 state를 렌더링이 되어도 유지할 수 있는 이유는 closer

### 게으른 초기화

💡 `useState`에 변수 대신 함수를 넘기는 방법을 게으른 초기화 라고 함

```jsx
// 일반적 사용 방법
const [state,setState] = useState(Number.parseInt(window.localStorage ... ))
// 해당 로직은 state가 처음 실행되는 순간에도 복잡한 연산을 실행함
// 그리고 값이 변경되어 다시 리렌더링 되는 순간에도 다시 복잡한 연산을 실행하게됨

// 게으른 초기화
const [state,setState] = useState(()=>{
	return Number.parseInt(window.localStorage.getItem(key))
})
```

- 기본적으로 게으른 초기화를 사용하는 순간은 주로 무거운 연산을 포함하고 있을때 사용을 권장함
  ⇒ 렌더링이 다시 발생하면 인수로 전달한 실행 값이 다시 실행되기 때문
  ⇒ 배열에 대한 접근 `map` `filter` `find` 등 / Storage에 접근
- 게으른 초기화 함수는 오로지 state가 처음 만들어지는 순간에만 사용됨
