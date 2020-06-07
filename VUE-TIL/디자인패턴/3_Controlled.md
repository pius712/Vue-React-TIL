# Controlled

prop을 아래로 내렸을 때, 그것을 v-model과 같은 것으로 값을 바꾸려고 하면 에러가 난다. (아래의 코드)

```html
<template>
	<check-box :checked="checked"></check-box>
</template>

<script>
	import CheckBox from './components/CheckBox.vue';
	export default {
	  data(){
	    return {
	      checked: true,
	    }
	  }
	  components: {
	    CheckBox
	  },
	}
</script>
```

```html
<template>
	<input type="checkbox" value="value" />
</template>

<script>
	export default {
		props: ['value'],
	};
</script>
```

## 참고

```html
<input v-model="searchText" />
<!-- same as -->
<input
	v-bind:value="searchText"
	v-on:input="searchText = $event.target.value"
/>
```

## 해결 방법

[공식 문서 참고, Using v-model on Components](https://vuejs.org/v2/guide/components.html#Using-v-model-on-Components)

```html
<template>
	<check-box v-model="checked"></check-box>
	<!-- <check-box :value="checked" @input="checked"></check-box> -->
</template>

<script>
	import CheckBox from './components/CheckBox.vue';
	export default {
	  data(){
	    return {
	      checked: true,
	    }
	  }
	  components: {
	    CheckBox
	  },
	}
</script>
```

```html
<template>
	<input type="checkbox" :value="value" @click="toggleCheckBox" />
</template>

<script>
	export default {
		props: ['value'],
		methods: {
			toggleCheckBox() {
				this.$emit('input', !this.value);
			},
		},
	};
</script>
```
