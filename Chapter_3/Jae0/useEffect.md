💡 무엇인가 변화가 생겨날 대 변화를 감지하고 반응하는 Hook

주로 화면에 처음 렌더링될때 , 다시 렌더링되는순간 , 사라지는순간 에 특정 작업을 처리하고 싶을때 사용함

```jsx
useEffect(() => {
  // code
}, [value]);
```

**첫 번째 인자**

Callback 함수를 전달받고,

Callback함수 내부에 실행하고 싶은 코드를 작성하면 됨

**두 번째 인자**

dependency Arr 를 전달받고 내부에는 값(value)을 전달 할 수 있음

- 전달받은 값(value)이 변화 되어질 때 마다 useEffect 내부의 Callback 함수가 실행되어짐

🔥 useEffect는 특별한 기능을 통해 값을 감시하지 않고,

렌더링이 새롭게 될 때마다 의존성에 존재하는 값을 보며 의존성의 값이 이전 값과 다를때

전달받은 Callback 함수를 실행하게 된다.

- 일부 외부 데이터와 동기화 하려는것이 아니라면 사용하지 않음
- Component의 최상위 위치에서만 선언할 수 있음
- strict 모드에서는 useEffect가 2번 실행 되어짐

### 의존성

``

의존성 배열 조차 전달하지 않는 경우 의존성을 비교할 필요 없이 렌더링 될 때마다 callback이 실행됨

⇒ 주로 component가 렌더링 된것을 확인하고싶을 때 사용함

```jsx
// 렌더링 테스트 코드
useEffect(()=>{
	console.log("렌더링 발생"
})
```

🔥 그렇다면 왜 아무것도 전달하지 않고 사용할까?

1. useEffect는 component가 렌더링이 완료된 후 실행되어지고, 일반 함수는 렌더링 도중 실행되어짐
2. 서버 사이드 렌더링 관점에서 useEffect는 클라이언트 사이드에서 실행되는것을 보장함

`[]`

빈 배열을 전달한 경우 React에서는 비교할 의존성이 없다고 판단하고 최초 렌더링 직후에만 실행되어짐

### 주의, 권장 사항

- useEffect가 전달 받는 callback에 기명 함수를 사용하는것을 권장함

  ⇒ 많은 useEffect들이 생겨나고 이를 구분하기위해 사용하기 좋음

  ```jsx
  useEffect(function 이름() {}, []);
  ```

- useEffect는 가능한 작고 가볍게 만들수록 좋음

- dependency에는 외부에서 생성된 객체, 함수와 같은 참조값을 사용하면 안된다

  ```jsx
  function ChatRoom({ roomId }) {
    const [message, setMessage] = useState('');

    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };

    useEffect(() => {
      const connection = createConnection(options);
      connection.connect();
      return () => connection.disconnect();
    }, [options]);
    // ...
  ```

  위의 예시처럼 options 를 감시하고 있다면,
  만약 options의 데이터가 변하지 않은 상태에서 다른 이유로 rerender가 발생했을때 원하는 의도는
  useEffect가 다시 실행되지 않는 것을 의도함.
  하지만 🔥 options는 참조 데이터 이기때문에 rerender시에 값의 변화가 없어도 참조 주소가 변경됨
  따라서 useEffect는 참조 주소가 변경된것을 변화로 감지하고 useEffect가 실행되어짐

### Clean Up

💡 이전 값을 기준으로 실행되며, 이전 상태를 청소해주는 역할

```jsx
useEffect(() => {
  element.addEventListener();

  return () => {
    element.removeEventListener();
    // clean up zone
  };
});
```

clean up 작업은 useEffect 에 전달한 callback 함수의 return 함수 내부에서 실행되어짐

🔥 즉 useEffect 내부의 callback이 실행될 때 이전 clean up 함수가 존재한다면

clean up 함수를 실행 한 뒤 callback 함수를 실행함
