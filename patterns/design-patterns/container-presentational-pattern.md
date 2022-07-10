# Container/Presentational Pattern

> 애플리케이션 로직에서 뷰를 분리하여 관심사를 분리합니다.

React에서 관심사를 분리하는 한 가지 방법은 **Container/Presentational pattern**을 사용하는 것입니다. 이 패턴을 사용하면 애플리케이션 로직에서 뷰를 분리할 수 있습니다.

---

강아지 사진 6개를 가져와서 화면에 렌더링하는 애플리케이션을 만든다고 가정해 보겠습니다.

이상적으로 이 과정을 2부분으로 분리하여 관심사 분리를 시행햐고자 합니다:

1. **Presentational Components**: 데이터가 **어떻게** 사용자에게 보여지는지에 관심을 갖는 컴포넌트입니다. 이 예시에서는 *강아지 사진 목록을 렌더링하는 것*입니다.
2. **Container Components**: **어떤** 데이터가 사용자에게 보여지는지에 관심을 갖는 컴포넌트입니다. 이 예시에서는 *강아지 사진을 가져오는 것*입니다.

![](https://user-images.githubusercontent.com/22341362/176662655-514329f3-932e-4b66-9e57-1bd965ed4bcb.png)
![](https://user-images.githubusercontent.com/22341362/176662666-5d3fa698-160a-403e-b17f-2cbe488469e8.png)
![](https://user-images.githubusercontent.com/22341362/176662677-8630e09a-db39-4665-a809-5977aa1bb631.png)

강아지 사진을 가져오는 것은 **애플리케이션 로직**을 다루는 반면 사진을 보여주는 것은 **뷰**만 다룹니다.

---

## Presentational Component

Presentational component는 `props`를 통해 데이터를 받습니다. 이 컴포넌트의 주요 기능은 받은 데이터를 _수정하지 않고_ 단순히 **보여주는 것**입니다.

강아지 사진을 보여주는 예시를 살펴보겠습니다. 강아지 사진을 렌더링할 때, 우리는 단순히 API를 통해 가져온 강아지 사진을 매핑하여 렌더링하고 싶어합니다. 이를 위해 `props`를 통해 데이터를 받고 받은 데이터를 렌더링하는 함수형 컴포넌트를 생성합니다.

```jsx
// DogImages.js

import React from 'react';

export default function DogImages({ dogs }) {
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
}
```

`DogImages` 컴포넌트는 presentational component입니다. Presentational component는 _일반적으로_ 상태가 없습니다: UI를 위해서가 아니라면 자체적인 React state를 포함하지 않습니다. 컴포넌트가 받은 데이터는 presentational component 자체에 의해 변경되지 않습니다.

Presentational component는 **container component**로부터 데이터를 받습니다.

---

## Container Components

Container component의 주요 기능은 container component가 _포함하고 있는_ presentational component로 **데이터를 전달**하는 것입니다. Container component는 일반적으로 데이터를 처리하는 presentational component를 제외하고 다른 컴포넌트를 렌더링하지 않습니다. Container component는 자체적으로 아무 것도 렌더링하지 않기 때문에 일반적으로 스타일도 포함하지 않습니다.

이 예에서는 `DogsImages` 컴포넌트로 강아지 사진을 전달하려고 합니다. 그렇게 하기 전에 외부 API에서 강아지 사진을 가져와야 합니다. 그리고 데이터를 가져와서 `DogImages` 컴포넌트로 데이터를 전달하는 **container component**를 생성해야 합니다.

```jsx
// DogImagesContainer.js
import React from 'react';
import DogImages from './DogImages';

export default class DogImagesContainer extends React.Component {
  constructor() {
    super();
    this.state = {
      dogs: [],
    };
  }

  componentDidMount() {
    fetch('https://dog.eco/api/breed/labrador/images/random/6')
      .then((res) => res.json())
      .then(({ message }) => this.setState({ dogs: message }));
  }

  render() {
    return <DogImages dogs={this.state.dogs} />;
  }
}
```

이 두 컴포넌트를 합치면 애플리케이션 로직과 뷰를 분리할 수 있습니다.

---

## Hooks

많은 경우에 Container/Presentational pattern은 React Hooks로 대체할 수 있습니다. Hooks의 도입으로 상태를 제공해주는 container component 업이도 상태를 쉽게 추가할 수 있습니다.

데이터를 가져오는 로직을 `DogImagesContainer` 컴포넌트 안에 작성하는 대신, 사진을 가져오고 강아지 배열을 반환하는 커스텀 훅을 만들 수 있습니다.

```jsx
export default function useDogImages() {
  const [dogs, setDogs] = useState([]);

  useEffect(() => {
    fetch('https://dog.eco/api/breed/labrador/images/random/6')
      .then((res) => res.json())
      .then(({ message }) => setDogs(message));
  }, []);

  return dogs;
}
```

이 훅을 사용하면 더 이상 데이터를 가져오고 `DogImages` 컴포넌트로 전달하는 `DogImagesContainer` 컴포넌트가 필요하지 않습니다. 대신 이 훅을 `DogImages` 컴포넌트에서 직접 사용할 수 있습니다!

```jsx
// useDogImages.js
import React from 'react';
import useDogImages from './useDogImages';

export default function DogImages() {
  const dogs = useDogImages();

  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
}
```

`useDogImages` 훅을 사용해도 여전히 애플리케이션 로직과 뷰를 분리할 수 있습니다. 우리는 `DogImages` 컴포넌트 안에서 데이터를 수정하지 않고 단순히 `useDogImages` 훅이 반환하는 데이터를 사용합니다.

![](https://user-images.githubusercontent.com/22341362/176672485-0cd9a195-0f20-4f2a-869b-4d4cfdc0706e.png)
![](https://user-images.githubusercontent.com/22341362/176672545-13a3f3bc-975a-4591-b582-5fba9d45ca08.png)

Hooks는 Container/Presentational pattern과 마찬가지로 컴포넌트에서 로직과 뷰를 쉽게 분리할 수 있습니다. 훅을 사용하면 container component 안에 presentational component를 래핑하는 데 필요한 추가적인 레이어를 절약할 수 있습니다.

---

### 장점

Container/Presentational pattern을 사용하면 많은 이점이 있습니다.

Container/Presentational pattern은 **관심사의 분리**를 장려합니다. Presentational component는 UI를 담당하는 순수 함수이고 container component는 애플리케이션의 상태와 데이터를 담당합니다. 이렇게 하면 **관심사의 분리**를 쉽게 적용할 수 있습니다.

Presentational component는 데이터를 변경하지 않고 단순히 보여주기 때문에 쉽게 재사용할 수 있습니다. 다양한 목적을 위해 애플리케이션 전체에서 presentational component를 재사용할 수 있습니다.

Presentational component는 애플리케이션 로직을 변경하지 않기 때문에 디자이너와 같이 코드베이스에 대한 지식이 없는 사람에 의해서도 쉽게 변경될 수 있습니다. Presentational component가 애플리케이션의 많은 부분에서 재사용된 경우, 앱 전체에서 변경 사항이 일관될 수 있습니다.

Presentational component는 일반적으로 순수 함수이기 때문에 테스트하기도 쉽습니다. 데이터 스토어를 모킹할 필요 없이 전달하는 데이터를 기반으로 컴포넌트가 무엇을 렌더링할지 알고 있습니다.

---

### 단점

Container/Presentational pattern을 사용하면 애플리케이션 로직과 렌더링 로직을 쉽게 분리할 수 있습니다. 그러나 Hooks를 사용하면 Container/Presentational pattern을 사용하지 않고 클래스 컴포넌트 안에 상태가 없는 함수형 컴포넌트를 다시 작성하지 않고도 동일한 결과를 얻을 수 있습니다. 더 이상 상태를 사용하기 위해 클래스 컴포넌트를 생성할 필요가 없습니다.

여전히 Container/Presentational pattern을 사용할 수는 있지만 (React Hooks를 사용하더라도) 규모가 작은 애플리케이션에서는 과할 수 있습니다.
