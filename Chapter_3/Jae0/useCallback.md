## useCallback

`useMemo` 에서는 전달 받은 `Callback` 의 `return` 값을 메모이제이션 한것과 유사하게

💡 `useCallback` 은 전달받은 `Callback` 함수 자체를 저장하여 함수를 메모이제이션 함
⇒ 즉 특정 함수를 새로 만들지 않고 다시 재사용한다는 의미를 가짐

useMemo와의 차이점은 저장하고자 하는 값이 변수냐 함수냐의 차이 일뿐

`const memoFuncName = useCallback( Callback , [ ] )`

- **첫 번째 인자**

  변수는 인자로 전달받은 `Function`을 메모이제이션해 값을 가지고 있게 됨

- **두 번째 인자**
  Array로 Array 안의 내부의 값이 변경되지 않는 이상 변경되지 않음!

**주의점**

🔥 함수 내부의 전역 변수를 받아 사용하는 로직이 존재한다면 해당 변수를 디펜던시 안에 넣어줘야함!
