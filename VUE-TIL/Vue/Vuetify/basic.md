# Vuetify

부트스트랩처럼 Material Desig을 지원하는 프레임워크. 

## icon

### matrials icon

아래 사진에서 `import from` 부분에서 camel case를 kebab-case로 바꿔서 넣으면 된다. 

![icon](../../img/icon.png)

## 드롭다운 메뉴 

여기서 주의깊게 봐야할 점은 v-slot이다. 아마 내부적으로(나한테 보이지는 않지만...) `v-menu` 컴포넌트가 구성되어 있는 것으로 추정된다. 그 컴포넌트에 슬롯이 있고, 그 슬롯을 사용하는 것임.  
`v-slot:activator` 는 name 속성이 이 activator인 slot이다.  
그리고 `={ on }`은 하위의 컴포넌트에서 상위로 올려주는 `slot props`이다.  
즉, `<slot name="activator" v-bind:???="???"> </slot>` 와 같은 방식으로 구현을 한 것이다. 여기서 ???은 추정으로는 객체를 slot props로 보내주고, 위에서는 비구조화 할당으로 on만을 받은 것 같다. 

그리고 `v-on="on"` 이 부분은 slot props으로 받은 `on = { object }` 객체이고, 내부에 이벤트들을 구현했다고 볼 수 있다. 

___
**note**  
`@click="..."` === `v-on:click="..."`  `v-on="{click:...}"`
___

```html
<v-menu>
    <template v-slot:activator="{ on }">
    <v-btn
        color="primary"
        dark
        v-on="on"
    >
        Dropdown
    </v-btn>
    </template>
    <v-list>
    <v-list-item
        v-for="(item, index) in items"
        :key="index"
        @click=""
    >
        <v-list-item-title>{{ item.title }}</v-list-item-title>
    </v-list-item>
    </v-list>
</v-menu>
```