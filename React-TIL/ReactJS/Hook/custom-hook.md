# Custom-hook

custom hook을 만들어서 컴포넌트의 로직을 재사용 가능한 함수로 만들 수 있다. 

예를 들어 input에 관한 코드는 비슷한 로직이 반복된다. 

아래는 custum-hook의 예이다. 

```js
// src/hooks/useInput.js

import { useState, useCallback } from 'react';

export default initValue => {
	const [value, setValue] = useState(initValue);
	const handler = useCallback(
		e => {
			e.preventDefault();
			setValue(e.target.value);
		},
		[value],
	);
	return [value, handler];
};
```

커스텀 Hooks  보통`use` 라는 키워드로 시작하는 파일을 만들고 그 안에 함수를 작성하는데, 그 내부에는 `useState`, `useEffect`, `useReducer`, `useCallback` 등의 hook을 사용하여 기능을 구현하고, 컴포넌트에 필요한 것들을 반환하면 된다. 

위의 custom hook의 사용법은 아래와 같다. 

```js
// components/LoginForm.js
import useInput from '../hooks/useInput.js'
const LoginForm = () =>{
  const [email, onChangleEmail] = useInput('');
  const [password, onChanglePassword] = useInput('');
  
  return {
    ...
  }
}
export default LoginForm;
```

