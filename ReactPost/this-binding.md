# This binding

자바스크립트의 this는 참 독특한 특징을 가지고 있다.

아래와 같이 클래스를 만들고 객체를 생성한 뒤 a.boo()를 호출하면 에러가 난다.

```js
class Foo  {
    bar(){
        console.log("hello");
    }
    boo(){
        bar();
    }
}
var a = new Foo();
a.boo();
>>> Uncaught ReferenceError: foo is not defined
```

위 코드와 아래의 코드의 차이가 보이는가? 바로 this의 유무이다.

```js
class A  {
    foo(){
        console.log("hello");
    }
    boo(){
        this.foo();
    }
}
var a = new Foo();
a.boo();
>>> hello
```