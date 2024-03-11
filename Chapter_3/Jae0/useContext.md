> **Prop Drilling & Context**
>
> 만약 최 상위 Component 가 지니고 있는 상태를 깊이 존재하는 하위 Component에게 전달하기
>
> 위해서는 중간에 존재하는 Component들의 prop을 통해 전달 > 전달 > 전달 > 전달을 통해
>
> 주고 받아야하는 복잡한 문제가 발생! - 문제는 하위에서 최상위로 전달할 때도 동일한 문제가 발생함
>
> 이런 문제는 깊어지고 상태가 많아질수록 데이터 관리에 불편하고,
>
> 중간에 state가 의도와 다르게 변경될 수 있는 위험성을 초래함
>
> 이런 과정을 PropDrilling이라고함
>
> 그리고 이런 prop drilling을 극복하기 위해 등장한 개념이 Context 임

💡 이런 Prop Drilling을 개선하기 위해 Redux Recoil 등의 라이브러리가 존재하고,

Context API는 React 라이브러리에서 기본으로 제공되는 상태 주입 API로
상위 컴포넌트에서 만들어진 Context를 통해 하위 컴포넌트가 상태를 쉽게 사용가능하게 함

- 상태를 주입하는 역할을 할뿐 다른 라이브러리처럼 상태 관리를 해주는 API가 아님
  - 🔥 Context API는 특정 상태를 통해 다른 상태를 만들수 없으며,
    필요에 따라 이러한 상태 변화를 최적화 할 수 없기 때문에 상태관리 라이브러리가 아님
- 최상단에 Context provider를 지정하기보다는 범위를 좁히고 좁혀 사용하는것이 좋음

**사용 방법**

```jsx
// Context 생성

import { createContext, useState } from "react";

// createContext 를 통해 Context를 만들어줌
export const Context = createContext();

// component를 umbrella로 감싸줄 외부 Wrap component 함수 만들기.
export function ContextProvider({ children }) {
		// conText에서 사용할 기본 State 지정
    const [State, setState] = useState(false);

		// 상태를 변경할 Function
    const Toggle = () => setState((state) => !state);

    return (
        <Context.Provider
						{/* 기본 상태와 상태변경을 위한 함수를 객체로 전달한다 */}
            value={{ state, ToggleDarkMode }}>
            {/* 기본 상태와 상태변경을 위한 함수를 객체로 전달한다 */}
            {children}
        </Context.Provider>
		)
}

// Context 위치 선언
export default function App() {
		// 앞서 만든 해당 Context component로 감싸 주기
    return (
        <ContextProvider>
					<childComponent />
        </ContextProvider>
    )
}

// 하위 Component

export default function ChildComponent (){
	// useContext 를 통해 Context 불러오기
	const { State, Toggle } = useContext(Context);

  return (
	  <div className='Button'>
	    { state }
      <button onClick={() => ToggleDarkMode()}> Toggle!</button>
    </div>
	)
}
```

**ContextAPI 오류 검증**

- 만약 하위 컴포넌트에서 Context를 제대로 불러오지 못했을 때를 대비한 방어 코드

```tsx
export default function ChildComponent() {
  // useContext 를 통해 Context 불러오기

  function checkContext() {
    const context = useContext(Context);
    if (context === undefined) {
      throw new Error("context가 불려오지 못함");
    }
    return context;
  }

  const { state, toggle } = checkContext();

  return (
    <div className="Button">
      {state}
      <button onClick={() => ToggleDarkMode()}> Toggle!</button>
    </div>
  );
}
```
