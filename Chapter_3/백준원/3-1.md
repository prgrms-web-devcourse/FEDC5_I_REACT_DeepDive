# 리액트의 모든 훅 파헤치기

리액트 함수형 컴포넌트에서 가장 중요한 개념은 **훅**

훅은 클래스형 컴포넌트에서만 가능했던 state, ref 등 리액트의 핵심적인 기능을 함수에서도 가능하게 만들었고, 무엇보다 클래스형 컴포넌트보다 간결하게 작성할 수 있다.

훅이 등장하면서 대부분의 리액트 컴포넌트는 함수형으로 작성되고 있다.

## useState

useState는 함수형 컴포넌트 내부에서 상태를 정의하고, 이 상태를 관리할 수 있게 해주는 훅

### useState 사용법

```JavaScript
import { useState } from 'react';

const [state, setState] = useState(initialState);
```

useState의 인수로는 사용할 state의 초깃값을 넘겨준다. 아무런 값을 넘겨주지 않으면 초깃값은 undefined다.

useState 훅의 반환 값은 배열, 배열의 첫 번째 원소로 state 값 자체를 사용할 수 있고, 두 번째 원소인 setState 함수를 사용해 해당 state의 값을 변경할 수 있다.

```JavaScript
function Component() {
    let state = 'hello';

    function handleButtonClick() {
        state = 'hi';
    }

    return (
        <>
         <h1>{state}</h1>
         <button onClick = {handleButtonClick}>hi</button>
        </>
    )
}
```

위의 코드는 동작하지 않는다

리액트의 렌더링은 함수형 컴포넌트에서 반환한 결과물인 return의 값을 비교해 실행되지 때문이다.

즉, 매번 렌더링이 발생될 때마다 함수는 다시 새롭게 실행되고, 새롭게 실행되는 함수에서 state는 매번 hello로 초기화되므로 아무리 state를 변경해도 다시 hello로 초기화되는 것이다.

### useState 내부의 모습을 구현

```JavaScript
function useState(initialState) {
    if(!global.states) {
        // 애플리케이션 전체의 states 배열을 초기화한다.
        // 최초 접근이라면 빈 배열로 초기화한다.
        global.states = [];
    }

    // states 정보를 조회해서 현재 상태값이 있는지 확인하고 없다면 초깃값으로 설정
    const currentState = global.states[index] || initialState;
    // states의 값을 위에서 조회한 현재 값으로 업데이트 한다.
    global.states[index] = currentState;

    // 즉시 실행 함수로 setter를 만든다.
    const setState = (function () {
        // 현재 index를 클로저로 가둬놔서 이후에도 계속해서 동일한 index에 접근할 수 있도록 한다.
        let currentIndex = index;
        return function (value) {
            global.states[currentIndex] = value;
            // 컴포넌트를 렌더링한다. 실제로 컴포넌트를 렌더링하는 코드는 생략
        }
    })()
    // useState를 쓸 때마다 index를 하나씩 추가한다. 이 index는 setState에서 사용된다.
    // 즉, 하나의 state마다 index가 할당돼 있어 그 index가 배열의 값(global.states)을
    index = index + 1;

    return [currentState, setState];
}
```

작동 자체만 구현했고 실제 구현체와는 차이가 있다. 실제 리액트 코드에서는 useReducer를 이용해 구현돼 있다.

여기서 함수의 실행이 끝났음에도 함수가 선언된 환경을 기억할 수 있는 방법은 바로 클로저다.

클로저를 사용함으로써 외부에 해당 값을 노출시키지 않고 오직 리액트에서만 쓸 수 있었고, 함수형 컴포넌트가 매번 실행되더라도 useState에서 이전의 값을 정확하게 꺼내 쓸 수 있게 됐다.

### 게으른 초기화

일반적으로 useState에서 기본값을 선언하기 위해 useState() 인수로 원시값을 넣는 경우가 대부분일 것이다. 그러나 이 useState의 인수로 특정한 값을 넘기는 함수를 인수로 넣어줄 수도 있다.

**게으른 초기화** : 변수 대신 함수를 넘기는 것

리액트 공식 문서에서 이러한 게으른 초기화는 useState의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용하라고 돼 있다.

localStorage나 sessionStorage에 대한 접근, map, filter, find 같은 배열에 대한 접근, 혹은 초깃값 계산을 위해 함수 호출이 필요할 때와 같이 무거운 연산을 포함해 실행 비용이 많이 드는 경우에 게으른 초기화를 사용하는 것이 좋다.
