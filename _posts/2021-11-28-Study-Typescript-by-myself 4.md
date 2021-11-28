---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: Typescript

---

### Studying Typescript by myself 4

#### 유틸리티 타입 Advanced Type

유틸리티 타입은 이미 정의해놓은 타입을 변환할 때 사용하기 좋은 타입의 문법. API 성 타입이다. 조금 더 간결한 코드를 구현할 수 있다.

<br/>

**Pick**

```tsx
interface Product {
    id: number; 
    name: string;
    price: number;
    brand: string; 
    stock: number;
}
function displayProductDetail(productItem: {id: number, name: string, price: number}) {
	//..
}
```

위와 같은 interface 와 아래와 같은 함수가 있다고 가정을 해 본다. 아래의 함수는 Product 라는 interface 의 부분을 가지고 오지만 전체에 해당되지는 않는다. 그럴 때엔 어떻게 해야 할까.

```tsx
interface ProductDetail {
    id: number;
    name: string;
    price: number;
}
```

이런 식으로 displayProductDetail 함수에 꼭 맞게 새로운 interface 를 만들어내도 되지만, 그런 경우에는 코드가 불필요하게 길어지고 가독성 또한 떨어질 수 있다. Product 와 ProductDetail 에 중복되는 정보도 발생한다.

유틸리티 타입을 활용하면 이미 정의되어 있는 interface 의 일부만 가져올 수 있다.

```tsx
type productItem = Pick<Product, 'id' | 'name' | 'price'>
function displayProductDetail(productItem: Pick<Product, 'id' | 'name' | 'price'>) {

}
```

Pick 키워드를 넣고 < 대상 타입, 어떤 키를 가지고 올 건지. > 를 작성해 주면 된다. 위처럼 type 자체를 선언하여도 되고 아래처럼 작성하여도 된다. 

<br/>

**Omit 타입**

오밋 Omit 타입은 특정 타입에서 지정된 속성만 제거한 타입을 정의해 준다. 특정 타입에서 안 쓸 것만 빼고 나머지만 쓰고 싶을 때 사용한다.

```tsx
type productItem = Omit<Product, 'brand' | 'stock'>
function displayProductDetail(productItem: Omit<Product, 'brand' | 'stock'>) {
	// ... 
}
```

라고 작성하는 것은 결국 id, name, price 만 가지고 와서 쓰겠다는 것과 동일한 것이다. 

<br/>

**Partial 타입**

파셜 타입은 특정 타입의 부분 집합을 만족하는 타입을 정의할 수 있다. 특정 정보를 갱신(업데이트) 하려고 한다고 가정할 때, 

```tsx
type optional = Partial<Product>
function updateProductItem(productItem: Partial<Product>) {

}
```

![image](https://user-images.githubusercontent.com/89691274/143724860-fe962d4d-4300-4981-aee2-365cee16d1d6.png)

Partial 은 타입을 만족하는 모든 부분, 모든 조건에 대해 Optional 처리를 하는 것이다. 넣어도 되고 안 넣어도 되고 선택적 처리를 가능할 수 있게 하는 것.

다른 예제도 알아 보자.

```tsx
interface User {
    username: string;
    email: string;
    photoUrl: string;
}
```

위와 같은 interface 가 있다고 생각할 때, User 의 정보는 얼마든지 바뀔 수 있다. 그럴 때마다 

```tsx
interface User {
    username: string;
    email: string;
    photoUrl: string;
}
interface UserUpdate { 
    username?: string;
    email?: string;
    photoUrl?: string;
}
```

새로운 interface 를 생성해 주어야 할까? 

```tsx
type UserUpdate = {
    username?: User['username']
    email?: User['email']
    photoUrl?: User['photoUrl']
}
```

새로운 타입을 정의해서 User 안에 있는 것을 인덱스 형태로 가져와 Optional 로 처리하는 방법이 있다. 혹은,

```tsx
type UserUpdate1 = {
    [p in 'username'| 'email' | 'photoUrl']?: User[p]
}
```

이렇게 작성을 할 수도 있다. User 안에 있는 키들을 반복문처럼 돌린다고 생각하면 된다. p 는 User 의 키가 될 것이고, 그것이 Optional 처리가 될 수 있게끔 물음표를 넣어 준다. in 뒤에 Union Type 으로 되어 있는 것은 또한 key of 로 대체할 수가 있다.

```tsx
type UserUpdate1 = {
    [p in keyof User]?: User[p]
}
```

위는 User 에 국한된 형태로 작성한 것이다. 이렇게 된 것을 Partial 의 문법으로 다시 정확하게 써 보자면 제네릭을 이용하여 아래처럼 코드 작성을 해 볼 수 있다.

```tsx
type Partial<T> = {
    [p in keyof T]-?: T[p]
}
```

<br/>

<br/>

#### Mapped Type

기존에 정의되어 있는 타입을 새로운 타입으로 변환해 주는 문법. 자바스크립트의 map() 함수를 타입에 적용하였다고 생각하면 이해가 쉽다.

```tsx
type Person = 'givvmee' | 'givvy' | 'me'
type PersonAge = {[K in Person]:number }
```

![image](https://user-images.githubusercontent.com/89691274/143732029-cf7ffe17-e530-406d-9139-8369d2bd28f2.png)

이 타입을 가져와서 써 본다면,

```tsx
const ages:PersonAge = {
    givvmee: 20,
		givvy: 17,
  	me: 17
}
```

이렇게 나타낼 수 있다. 만약 모든 요소를 써 주지 않는다면

![image-20211128152655665](/Users/givvemee/Library/Application Support/typora-user-images/image-20211128152655665.png)

오류가 발생한다.

<br/>

<br/>

#### 자바스크립트에 타입스크립트를 적용하기

주의해야 할 점 

- 기능적인 변경은 하지 말 것
- 테스트 코드가 있는 상태에서 변환을 할 것. 테스트 소스가 없을 땐 가급적 하지 않을 것
- 처음부터 타입을 엄격하게 적용하지 않을 것

<br/>

<br/>

이제 이론적인 부분은 끝났고, 프로젝트를 하나 작성해 볼 것이다! 























