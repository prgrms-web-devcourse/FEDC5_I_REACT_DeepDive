### 함수형 컴포넌트 Function Component

초기의 함수형 Component는 어떤 Element를 별다른 라이프사이클 메서드나 State가 필요없는

그저 별 다른 기능이 없이 정적으로 렌더링 하는 목적을 지님.

그렇지만!

이후 Hook이 등장하게 되며 `State` `LifeCycle Method` 와 비슷한 작업이 가능해지면서

보일러 플레이트가 복잡한 클래스 컴포넌트보다 함수형 컴포넌트를 사용하게됨

### 클래스형 컴포넌트 Class Component

- **예시 코드**

  ```tsx
  import React from "react";

  interface SampleProps {
    required?: boolean;
    text: string;
  }

  interface SampleState {
    count: number;
    isLimited?: boolean;
  }

  // 해당 component에 제네릭을 통해 props와 state를 순서대로 넣어줌
  class SampleComponent extends React.Component<SampleProps, SampleState> {
    // constructor내부에서 props를 넘겨주고, state의 기본값을 넣어줌
    private constructor(props: SampleProps) {
      super(props);
      this.state = {
        count: 0,
        isLimited: fasle,
      };
    }

    // render 내부에서 사용되어질 함수를 선언하는 곳
    private handleClick = () => {
      const newValue = this.state.count + 1;
      this.setState({ count: newValue, isLimited: newValue >= 10 });
    };

    // render에서 이 component가 렌더링 할 내용을 정의함
    public render() {
      // props 와 state 값을 해당 component 내부에서 꺼냄
      const {
        props: { requierd, text },
        state: { count, isLimited },
      } = this;

      return (
        <>
          <button
            onClick={this.handleClick}
            disabled={isLimited}
          />
          <h1>{text}</h1>
          <h1>{count}</h1>
        </>
      );
    }
  }
  ```

**내부**

- `constructor`
  컴포넌트가 초기화 되는 시점에 호출되어고, state의 초기화가 진행됨
  내부의 `super()`는 컴포넌트를 만들면서 상속받은 상위 컴포넌트 ( React.Component 의 생성자 함수)
  를 먼저 호출해 상위 컴포넌트에 접근하도록 도와줌
- `props`
  함수에 인수를 넣는 것과 동일하게 interface에서 정의된 타입대로 전달한 props를 속성으로 전달 해야함
- `state`
  component 내부에서 관리하는 값을 의미함
  해당 값은 항상 객체로 존재해야함
- `method`
  render 함수 내부에서 사용되어지는 함수 혹은 함수들 이며, 보통 DOM에서 발생하는 이벤트와 함께 사용됨
  일반 함수는 항상 화살표 함수를 사용하거나! 직접 this에 바인딩 해야함

### 클래스형 컴포넌트의 생명 주기

💡 크게 3가지로 생명 주기를 나눌 수 있음

- mount : component가 생성되어지는 시점
- update : 생성되어져 있는 component의 내용이 변경되어진 시점
- unmount : component가 더이상 존재하지 않는 시점

- `render()`
  클래스 컴포넌트에서 유일하게 필수로 사용되어지는 함수
  주로 UI를 렌더링하기 위해 사용되어지고, `mount` `update` 과정에서 발생함
  주의! `render()`함수 내부에서 상태의 변경 props의 변경등의 다른 의도의 기능은 동작해서는 안됨
- `componentDidMount()`
  `mount`가 완료되는 즉시 바로 그 다음에 호출되는 메서드
  `render()` 와 다르게 내부에서 setState로 state의 변경이 가능하고 state가 변경되어지면
  다시한번 렌더링이 시도되어지는데 UI의 update이전에 실행되어 눈치챌 수 없음
  🔥! but 생성자 함수에서 할수없는 API 호출 후 업데이트, DOM에 의존적인 작업 등을 하기 위해
  state의 변경이 허용됨. 일반적 state변경은 constructor에서 하는게 좋음
- `componentDidUpdate()`
  component의 업데이트가 일어난 직후 바로 실행되어짐
  주로 `props` `state`의 변화를 DOM에 업데이트 하기위해 사용되어짐
  🔥 내부에서 setState를 통한 state 변화가 가능하지만 적절한 조건문을 사용하지 못하면 무한 루프 발생
- `componentWillUnmount()`

  component가 `unmount` 혹은 사용되지 않기 직전 호출되어지는 함수

  주로 클린업 함수를 호출하기 위한 장소로 사용되어지고, Event 제거 , API 호출 취소 등에 사용됨

- `shouldComponentUpdate()`
  `state` `props` 의 변경에 의한 리렌더링을 막고싶을때 사용되어짐
- `static getDerivedStateFromProps()`

  `render()`가 호출되어지기 바로 직전에 호출되어지며, `static`으로 선언되어 this에 접근할 수 없음

  🔥 해당 함수에서 객체를 반환하면 반환한 객체의 모든 내용은 state내부로 들어가게됨

  그리고 `null`을 반환하게되면 아무 변화도 일어나지 않음

- `getSnapShotBeforeUpdate()`
  DOM이 업데이트되어지기 바로 직전에 호출됨, 해당 함수의 반환값은 모두 `componentDidUpdate`에 전달
  주로, DOM 렌더링 이전 윈도우의 크기 조절, 스크롤 위치 조정 등의 작업에 이용
- `getDerviedStateFromError()`

  자식 Component에서 에러가 발생했을 때 호출되어지는 에러 메서드

  static 메서드로 자식 component의 error를 인수로 전달받게되어짐

  🔥 무조건 반환 값으로 state값을 반환해야함 - 절대로 내부에서 state가 변경되는 작업은 불가능

- `componentDidCatch()`

💡 `getSnapShotBeforeUpdate` `getDerviedStateFromError` `componentDidCatch` 3개의 메서드는 아직 Hook으로 구현되어지지 않음.
따라서 사용해야한다면 클래스형 component로 구현해야함
( 2023 - 12 기준 )

### 클래스 컴포넌트의 한계

- 데이터 흐름의 추적이 매우 어려움
  여러 생명 주기 메서드들이 존재고 실행의 순서는 존재하지만 작성의 순서가 정해져 있지 않다보니
  순서와 상관없이 작성하는 등의 state추적이 어려움
- 애플리케이션 내부의 로직에대한 재사용이 어려움
  애플리케이션의 규모가 클수록, 그리고 공통되는 로직이 많아질 수록 래퍼 지옥(wrapper hell)에
  빠질 수 있고, extends PureComponent와 같이 컴포넌트를 상속해 사용하더라도
  클래스의 흐름을 찾기 어려움
- 기능이 많아질 수록 컴포넌트의 크기가 쉽게 커짐
- 함수에 비해 매우 어려움
  JS에서의 클래스는 사용이 비교적 어렵고 일반적이지 않음
  ( 다른 언어와 다르게 동작하는 this 등 )
- 최종 작업 이후 번들 크기를 최적화 하기가 매우 어려움

- 핫 로딩이 어려움
  핫로딩 ?
  코드에 변경 사항이 발생했을때 앱을 다시 실행하지 않고 해당 변경된 코드만 업데이트해 변경 사항을
  빠르게 적용할 수 있는 방법으로 주고 개발단계에서 적용됨 ⇒ 리액트 작업중 세이브 하면 바로 적용되는 기능

### 함수형 컴포넌트에 생명 주기 메서드가 없는 이유

이유는

함수형 컴포넌트는 단순히 props를 전달받아 React의 요소만을 반환하는 함수이고,

클래스형은 컴포넌트내부에 render메서드가 존재하여 React.Component 를 상속받아 구현하는 방법임

🔥 즉 React의 생명주기는 React.Component에서 오는 것이기 때문에 함수형에는 없음
