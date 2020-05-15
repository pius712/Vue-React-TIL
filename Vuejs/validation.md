# validation

`input form`의 경우에 validation이 필요한 경우가 있다.

## email

```js
function validateEmail(email) {
    var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return re.test(String(email).toLowerCase());
}
```

## vue js 적용

```js
import validateEmail from '@/utils/validation.js'

computed: {
  isEmailValid(){
    return validateEmail(this.email)
  }
}
```