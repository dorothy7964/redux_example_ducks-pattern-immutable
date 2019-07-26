# immutable

### Map

Immutable의 Map은 **객체** 대신 사용하는 데이터 구조  

내부에서 Map을 사용하지 않으면 추 후 setIn, getIn을 활용 할 수 없다.



```javascript
const {Map} = Immutable;

const data = Map({
  a:1,
  b:2,
  c: Map({
    d:3,
    e:4,
    f:5
  })
});
```

객체 내용을 네트워크에서 받아 오거나 전달받는 객체가 너무 복잡한 상태라면 일일이 그 내부까지 Map으로 만들기 힘들수도 있다.

이때는 fromJS를 사용할 수 있다.

### fromJS

fromJS를 사용하면 이 코드처럼 내부에 있는 객체들은 Map을 쓰지 않아도 된다.

```javascript
const {Map, fromJS} = Immutable;

const data = fromJS({
  a:1,
  b:2,
  c: {
    d:3,
    e:4,
    f:5
  }
});
```

해당 데이터를 실제로 활용하거나 업데이트를 해야 할 때는 내장 함수를 사용해야 한다.

예를 들어 data내부의 a값을 참조하고 싶다면

data.a로 작성하는 것이 아니라, **data.get('a')** 를 해야 한다.

#### - 자바스크립트 객체로 변환

immutable 객체를 일반 객체 형태로 변형하는 방법은 다음과 같다.

```javascript
const {Map, fromJS} = Immutable;

const data = Map({
  a:1,
  b:2,
  c: Map({
    d:3,
    e:4,
    f:5
  })
});

const deserialized = data.toJS();
console.log(deserialized);
//{a: 1, b: 2, c: { d :3, e :4 }}
```

특정 키의 값 불러오기  
특정 키의 값을 불러올 때는 get 함수를 사용합니다.

```javascript
data.get('a'); //1
```

깊숙이 위치하는 값 불러오기  
Map 내부에 또 Map이 존재하고, 그 Map 안에 있는 키 값을 불러올 때는 getIn 함수를 사용한다.

```javascript
data.getIn(['c' , 'd' ]); //3
```

#### - 값 설정

새 값을 설정할 때는 get 대신 set을 사용한다.

```javascript
const newData = data.set('a', 4);
```

set을 한다고 해서 데이터가 실제로 변하는 것은 아닌다.

주어진 변화를 적용한 새 Map을 만드는 것이다.

```javascript
console.log(newData === data);
```


서로 다른 Map이기 때문에 false를 프린트한다.

기존 data값은 그대로 남아 있고, 변화가 적용 된 데이터를 newData에 저장하는 것이다.


#### - 깊숙이 위치하는 값 수정 ( setIn )

깊숙이 위차하는 값을 수정할 때는 setIn을 사용한다.

이때 내부에 있는 객체들도 Map 형태여야만 사용할 수 있다는 점에 주의해야 한다.

```javascript
const newData = data.setIn(['c','d'],10);
```

#### - 여러 값 동시에 설정 ( marge )

값 여러 개를 동시에 설정해야 할 때는 mergeIn를 사용한다.

예를 들어 c값과 d값, c값과 e값을 동시에 바꾸어야 할 때는 코드를 다음과 같이 입력한다.

**[ 방법 1 ]**

```javascript
const newData = data.mergeIn(['c'], { d : 10, e : 10})
```

이렇게 mergeIn를 사용하면 c안에 들어 있는 f값은 그대로 유지하면서 d값과 e값만 변경한다.

또는 코드를 다음과 같이 입력 할 수도 있다.

**[ 방법 2 ]**

```javascript
const newData = data.setIn(['c', 'd'], 10)
                    .setIn(['c', 'e'], 10);
```

그리고 최상위에서 merge를 해야 할 때는 코드를 다음과 같이 입력한다.

```javascript
const newData = data.marge({a : 10 , b : 10});
```
즉, set을 여러번 할지, 아니면 merge를 할지는 그때그때 상황에 맞춰 주면 되지만,

성능상으로 set을 여러번 하는것이 빠르다

( 하지만 애초에 오래 걸리는 작업이 아니므로 실제 처리 시간의 차이는 매우 미미하다.)
