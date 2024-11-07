# Passing Props

- 부모는 자식에게 데이터를 전달(Pass Props)하며, 자식은 자신에게 일어난 일을 부모에게 알림(Emit event)

## Props

- 부모 컴포넌트로부터 자식 컴포넌트로 데이터를 전달하는데 사용되는 속성
- 부모 속성이 업데이트 되면 자식으로 전달되지만 그 반대는 안됨
    - 즉, 자식 컴포넌트 내부에서 props를 변경하려고 시도해서는 안되며 불가능
- 부모 컴포넌트가 업데이트 될 때마다 이를 사용하는 자식 컴포넌트의 모든 `props`가 최신 값으로 업데이트 됨
- 부모 컴포넌트에서만 변경하고 이를 내려 받는 자식 컴포넌트는 자연스럽게 갱신(하향식 단방향 바인딩)
    - 하위 컴포넌트가 실수로 상위 컴포넌트의 상태를 변경하여 앱에서의 데이터 흐름을 이해하기 어렵게 만드는 것을 방지하기 위해 단방향

## Props 선언

### 사전 준비

1. vue 프로젝트 생성
2. 초기 생성된 컴포넌트 모두 삭제 (App.vue 제외)
3. src/assets 내부 파일 모두 삭제
4. main.js 해당 코드 삭제
    
    ```jsx
    import './assets/main.css'
    ```
    
5. App > Parent > ParentChild 컴포넌트 관계 작성
    - App 컴포넌트 작성
    
    ```html
    <template>
      <div>
        <Parent />
      </div>
    </template>
    
    <script setup>
    	import Parent from '@/components/Parent.vue'
    </script>
    
    <style scoped>
    
    </style>
    ```
    
    - Parent 컴포넌트 작성
    
    ```jsx
    <template>
      <div>
        <ParentChild />
      </div>
    </template>
    
    <script setup>
    	import ParentChild from '@/components/ParentChild .vue'
    </script>
    ```
    
    - ParentChild 컴포넌트 작성
    
    ```jsx
    <template>
      <div>
      </div>
    </template>
    
    <script setup>
    </script>
    ```
    

### Props 선언

- 부모 컴포넌트에서 내려 보낸 `props`를 사용하기 위해서는 자식 컴포넌트에서 `props` 선언 필요
- 부모 컴포넌트 Parent에서 자식 컴포넌트 ParentChild에 보낼 `props` 작성

```jsx
<!-- Parent.vue -->
<template>
  <div>
    <ParentChild **my-msg**="message"/>
  </div>
</template>
```

- `defineProps()`를 사용하여 props를 선언
- `defineProps()`에 작성하는 인자의 데이터 타입에 따라 선언 방식이 나뉨

### Props 선언 방식 (1) - “문자열 배열”을 사용한 선언

- 배열의 문자열 요소로 `props` 선언
- 문자열 요소의 이름은 전달된 `props`의 이름

```jsx
<!-- ParentChild.vue -->

<script setup>
	defineProps(['**myMsg**'])
</script>
```

### Props 선언 방식 (2) - “객체”를 사용한 선언 (권장)

- 각 객체 속성의 키가 전달 받은 `props` 이름이 되며, 객체 속성의 값은 값이 될 데이터의 타입에 해당하는 생성자 함수(Number, String …)여야 함

```jsx
<!-- ParentChild.vue -->

<script setup>
	defineProps({
		myMsg: String
	})
</script>
```

### props 데이터 사용

- `props` 선언 후 템플릿에서 반응형 변수와 같은 방식으로 활용

```jsx
<!-- ParentChild.vue -->

<div>
  <p>{{ myMsg }}</p>
</div>
```

- `props`를 객체로 반환하므로 필요한 경우 JavaScript에서 접근 가능

```jsx
// props 데이터를 활용해야하는 경우
const props = defineProps({ myMsg: String })
console.log(props) // {myMsg: 'message'}
console.log(props.myMsg) // 'message'
```

## Props 세부 사항

### Props Name Casing(Props 이름 컨벤션)

- 자식 컴포넌트로 전달 시 `kebab-case`
    - HTML 자리에 있으니까
- 선언 및 템플릿 참조 시 `camelCase`
    - script자리니까 js 영역

### Static Props와 Dynamic Props

- 지금까지 작성한 것은 `static(정적)props`
- `v-bind`를 사용하여 동적으로 할당된 `props`를 사용할 수 있음
    1. Dynamic props 정의
    
    ```jsx
    <!-- Parent.vue -->
    <script setup>
    	import { ref } from 'vue'
    	const name = ref('Alice')
    </script>
    
    <template>
      <div>
        <ParentChild my-msg="message" :dynamic-props="name"/>
      </div>
    </template>
    ```
    
    1. Dynamic props 선언 및 출력
    
    ```jsx
    <!-- Parentchild.vue -->
    
    defineProps({
    	myMsg: String,
    	dynamicProps: String,
    })
    
    <p>{{ dynamicProps }}</p>
    ```
    

## Props 활용

### 다른 디렉티브와 함께 사용

- `v-for`와 함께 사용하여 반복되는 요소를 `props`로 전달하기
- ParentItem 컴포넌트 생성 및 Parent의 하위 컴포넌트로 등록

```jsx
<!-- ParentItem.vue -->

<template>
  <div>
  </div>
</template>

<script setup>
</script>

<!-- Parent.vue -->
<template>
  <div>
    <ParentItem />
  </div>
</template>

<script setup>
	import ParentItem from '@/components/ParentItem .vue'
</script>
```

- 데이터 정의 및 `v-for` 디렉티브의 반복 요소로 활용
- 각 반복 요소를 `props`로 내려 보내기

```jsx
<!-- Parent.vue -->

const items = ref([
  { id: 1, name: '사과' },
  { id: 2, name: '바나나' },
  { id: 3, name: '딸기' }
])
```

```html
<!-- Parent.vue -->

<ParentItem 
	v-for "item in items"
	:key="item.id"
	:my-prop="item"/>

```

- `props` 선언 및 출력 결과 확인

```html
<!-- ParentItem.vue -->

<template>
  <div>
    <p>{{ myProp.id }}</p>
    <p>{{ myProp.name }}</p>
  </div>
</template>

<script setup>
	defineProps({
	  myProp: Object
	})
</script>
```

# Component Events

## $emit()

- 자식 컴포넌트가 이벤트를 발생시켜 부모 컴포넌트로 데이터를 전달하는 역할의 메서드
- 부모가 `props` 데이터를 변경하도록 소리쳐야 한다.
    - `$` 표기는 Vue 인스턴스의 내부 변수들을 가리킴
    - Life cycle hooks, 인스턴스 메서드 등 내부 특정 속성에 접근할 때 사용

```jsx
$emit(event, ...args)
```

- event : 커스텀 이벤트 이름
- args : 추가 인자

## 이벤트 발신 및 수신

### 발신

- ParentChild에서 someEvent라는 이름의 사용자 정의 이벤트를 발신
    - 클릭 이벤트가 발생했을 때 someEvent를 발생시킬 것이다.

```jsx
<!-- ParentChild.vue -->

<button @click="$emit('someEvent')">클릭</button>
```

### 수신

- ParentChild의 부모 Parent는 `v-on`을 사용하여 발신된 이벤트를 수신
    - 소리치는 `emit` 이벤트 (some-event)를 듣게 되면 someCallback 실행하겠다.
- 수신 후 처리할 로직 및 콜백함수 호출

```jsx
<!-- Parent.vue -->

<ParentChild @some-event="someCallback"/>
```

## emit 이벤트 선언

- `defineEmits()`를 사용하여 발신할 이벤트를 선언
- props와 마찬가지로 `defineEmits()`에 작성하는 인자의 데이터 타입에 따라 선언 방식이 나뉨(배열, 객체)
- `defineEmits()`는 `$emit` 대신 사용할 수 있는 동등한 함수를 반환
    - script에서는 `$emit` 메서드를 접근할 수 없기 때문
    
    ```jsx
    <!-- ParentChild.vue -->
    
    <script setup>	
    	const emit = defineEmits(['someEvent'])
    	const buttonClick = function () {
    		emit('someEvent')
    	}
    </script>
    ```
    
- 이벤트 선언 방식으로 추가 버튼 작성 및 결과 확인

```jsx
<!-- ParentChild.vue -->
<button @click="buttonClick">클릭</button>
```

## 이벤트 전달

### 이벤트 인자 (Event Arguments)

- 이벤트 발신 시 추가 인자를 전달하여 값을 제공할 수 있음

### 이벤트 전달 활용

- ParentChild에서 이벤트를 발신하여 Parent로 추가 인자 전달하기

```jsx
<!-- ParentChild.vue -->
<button @click="emitArgs">추가 인자 전달</button>

const emit = defineEmits(['someEvent', 'emitArgs'])
const emitArgs = function () {
  emit('emitArgs', 1, 2, 3)
}
```

- ParentChild에서 발신한 이벤트를 Parent에서 수신

```jsx
<!-- Parent.vue -->
<ParentChild @emit-args="getNumbers"/>

const getNumbers = function (...args) {
  console.log(args)
  console.log(`자식 컴포넌트로 부터 데이터 ${args}를 받았습니다.`)
}
```

## emit 이벤트 활용

📝 최하단 컴포넌트 ParentGrandChild에서 Parent 컴포넌트의 name 변수 변경 요청하기

[App > Parent > ParentChild > ParentGrandChild]

- ParentGrandChild에서 이름 변경을 요청하는 이벤트 발신

```jsx
<!-- ParentGrandChild.vue -->
const emit = defineEmits(['updateName'])

const updateName = function () {
	emit('updateName')
}

<button @click="updateName">이름 변경</button>
```

- 이벤트 수신 후 이름 변경을 요청하는 이벤트 발신

```jsx
<!-- ParentChild.vue -->
<ParentGrandChild @update-name="updateName" />

const emit = defineEmits(['updateName'])

const updateName = function () {
	emit('updateName')
}
```

- 이벤트 수신 후 이름 변수 변경 메서드 호출
- 해당 변수를 `props`로 받는 모든 곳에서 자동 업데이트

```jsx
<!-- Parent.vue -->
<ParentChild @update-name="updateName" />

const updateName = function () {
	name.value = 'Bella'
}
```

# 참고

## 정적 & 동적 props 주의사항

- 첫 번째는 정적 `props`로 문자열 1을 전달
- 두 번째는 동적 `props`로 숫자 1을 전달

```jsx
<!-- 1 -->
<SomeComponent num-props="1" />

<!-- 2 -->
<SomeComponent :num-props="1" />
```

## Props & Emit 객체 선언 문법

- `Props` 선언 시 “객체 선언 문법”을 권장하는 이유
    - 컴포넌트를 가독성 좋게 문서화할 수 있음
    - 다른 개발자가 잘못된 유형을 전달할 때 브라우저 콘솔에 경고 출력하도록 함
    - props에 대한 유효성 검사로서 활용 가능

```jsx
defineProps({
	// 여러 타입 허용
	propB: [String, Number],
	// 문자열 필수
	propC: {
		type:String,
		required: true
	},
	// 기본 값을 가지는 숫자형
	propD : {
		type: Number,
		default: 10
	},
...
```

- `emit` 이벤트도 객체 구문으로 선언된 경우 유효성 검사 가능

```jsx
const emit = defineProps({
	// 유효성 검사 없음
	click: null,
	// submit 이벤트 유효성 검사
	submit: ({ email, password }) => {
		if (email && password) {
			return true
		} else {
			console.warn('submit 이벤트가 옳지 않음')
			return false
		}
	}
})
const submitForm = function (email, password) {
	emit('submit', { email, password })
}
```