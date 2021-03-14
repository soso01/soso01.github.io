---
title: jest 강좌
date: 2021-01-14 14:01:41
category: study
thumbnail: { thumbnailSrc }
draft: false
---

# Jest 강좌

### [출처 - 유튜브 코딩앙마](https://www.youtube.com/watch?v=g4MdUjxA-S4&list=PLZKTXPmaJk8L1xCg_1cRjL5huINlP2JKt&index=1)

### 소개, 설치, 간단한 테스트
- JEST : 페북에서 만듬, Zero config 철학을 가지고 있어서 별개의 설정없이 빠르게 테스트할 수 있는 장점이 있다.
- js진영에서 테스트 도구로 제일 많이 쓴다.
- npm i jest -D 설치
- package.json에서 스크립트에 test : 'jest'로 수정.
- npm jest 명령어를 치면 *.test.js 파일이나 __tests__폴더에 테스트 파일들을 모두 테스트한다.

```javascript
const { add } = require("./fn")

test("2 + 3 = 5", () => {
  expect(add(2, 3)).toBe(5)
})
```

- 테스트할 함수를 불러오고 expect에 검증할 값을, toBe에 인자로 예상값을 넣고 테스트를 돌리면 expect값이 예상값과 일치한지 출력해준다.
- 여기에 쓰인 toBe를 Matcher 라고 한다.
- toBe는 숫자나 문자 같은 기본 타입형을 검사할때 쓰고, toBe 외에도 다양한 Matcher가 있음.


### 다양한 Matchers
#### toBe와 toEqual의 차이

```javascript
const { makeUser } = require("./fn")

test("Jake toBe", () => {
  expect(makeUser('Jake', 20)).toBe({name: 'Jake', age: 20})
}) // 실패

test("Jake toEqual", () => {
  expect(makeUser('Jake', 20)).toEqual({name: 'Jake', age: 20})
}) // 성공
```
- toBe는 객체나 배열의 내부를 재귀적으로 탐색하면서 비교하지 않지만, toEqual은 재귀적으로 돌면서 속성이 같은지 비교함.
```javascript
test("Jake toBe", () => {
  const jake = {name : 'Jake'}
  expect(jake).toBe(jake)
})
```
- 이건 통과한다. toBe는 딱 js에서 '===' 비교랑 같은듯하다. 객체나 배열은 레퍼런스값으로만 비교한다.

#### toStrictEqual
```javascript
const fn = {
  add : (a, b) => a + b,
  makeUser: (name, age) => ({name, age, gender: undefined})
}

test("Jake toStrictEqual", () => {
  expect(makeUser('Jake', 20)).toStrictEqual({name: 'Jake', age: 20})
}) // 실패

test("Jake toEqual", () => {
  expect(makeUser('Jake', 20)).toEqual({name: 'Jake', age: 20})
}) // 성공
```
- toStrictEqual은 toEqual보다 좀 더 엄격하게 비교연산을 수행한다.
- toEqual에서는 gender: undefined와 gender속성이 아예없는것을 같다고 판별하지만 toStrictEqual은 다르다고 봄.

#### 그외
- toBeNull
- toBeUndefined
- toBeDefined
- toBeTruthy 
- toBeFalsy
- toBeGreaterThan - 크다
- toBeGreaterThanOrEqual - 크거나같다
- toBeLessThan - 작다
- toBeLessThanOrEqual - 작거나같다
- toMatch - 정규표현식

```javascript
test("match", () => {
  expect("hello world").toMatch(/hello/)
}) //성공
```

- toContain - 배열에서 특정요소가 있는지 확인
- toThrow - 에러 발생 여부. 인자에 에러 메시지 넣어서 메시지검증까지도 됨.


### 비동기 코드 테스트
```javascript
getName: (callback) => {
    const name = "Mike"
    setTimeout(() => {
      callback(name)
    }, 3000)
  }

test("3초 후에 받아온 이름은 Mike", () => {
  const callback = (name) => {
    expect(name).toBe("Tom")
  }
  fn.getName(callback)
})//성공
```
- 위 코드는 성공한다.
- 테스트를 돌려보면 3초를 기다리지 않고 success를 출력하는데, jest는 test함수의 끝에 도달하면 그대로 종료된다.
- 비동기의 콜백을 실행되기 전에 종료되서 success를 출력하는 것임.
- 이럴때는 테스트함수에 done이라고 하는 콜백함수를 전달해주면된다.

```javascript
test("3초 후에 받아온 이름은 Mike", (done) => {
  const callback = (name) => {
    expect(name).toBe("Tom")
    done()
  }
  fn.getName(callback)
}) //3초 후 실패
```
- 콜백내의 done함수가 실행될 때까지 테스트가 종료되지 않아 비동기함수를 수행할 수 있음.
- done을 전달은 받았는데 호출하지 않으면 테스트가 실패한다.

```javascript
getAge: (age) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(age)
      }, 3000)
    })
  }
test("3초 후에 받아온 age는 30", () => {
  return getAge(30).then((age) => {
    expect(age).toBe(30)
  })
})
```
- promise의 경우 done을 안써도 된다. 프로미스를 return 시켜주면 됨.
```javascript
test("3초 후에 받아온 age는 30", () => {
  return expect(getAge(30)).resolves.toBe(30)
})
```
- 이렇게 사용할 수도 있음.
- expect(getAge(30)).rejects.toMatch("error")이런식으로 reject도 테스트가능함.

```javascript
test("async await",async () => {
  const age = await getAge(30)
  expect(age).toBe(30)
})
```
- async await은 그냥 평소에 쓰던대로 하면 됨.


```javascript
test("3초 후에 받아온 age는 30", async () => {
  await getAge(30).then((age) => {
    expect(age).toBe(30)
  })
})

test("3초 후에 받아온 age는 30", async () => {
  await expect(getAge(30)).resolves.toBe(30)
})
```

- 위에 코드중 return 대신 await을 써도 돌아간다.


### 테스트 전 후 작업
- beforeEach(() => {}) : 각 테스트 실행전에 이 함수가 실행됨. 값을 초기화하기 좋음
- afterEach : 이건 각 테스트가 끝날때마다 실행된다.
- beforeAll / afterAll : 각 테스트 케이스마다가 실행되는게 아니라 전체 테스트가 시작하기 전 / 끝난 후에 실행됨.
- describe를 써서 여러 테스트를 한 블록에 묶을 수 있다. 
- 이때 describe내의 before / after 함수들은 describe 내부에서만 실행 됨.
- 근데 describe내의 테스트들은 describe 밖에서 정의한 before/after함수들도 적용된다.
- describe 밖에있는 beforeEach가 내부의 beforeEach보다 먼저 실행됨.
- describe 안에있는 afterEach가 외부의 afterEach보다 먼저 실행됨.
- 밖 beforeEach -> 안 beforeEach -> 테스트 -> 안 afterEach -> 밖 afterEach
```javascript
test.only("0 + 1 = 1", () => {
  expect(add(0, 1)).toBe(1)
})
```
- test.only는 해당 테스트만 실행되도록 한다.
- 여러 테스트가 있는데 하나만 실패한 경우 외부의 요인인지 코드자체의 문제인지 파악할때 유용
- test.skip은 해당 테스트만 건너뛰도록 한다.


### mock 함수
- 테스트 하기 위해 흉내만 내는 함수.
- 유저 db에 접근해서 user list를 select 해오는 작업이 필요하다면 작성해야할 코드가 상당히 많아진다.
- 외부요인의 영향을 받기도 함(네트워크나 db환경)
- 직접 코드를 짜는 대신 목함수로 대체함.

#### calls
```javascript
const mockFn = jest.fn()

mockFn()
mockFn(1)

test('dd', () => {
  console.log(mockFn.mock.calls) // [ [], [ 1 ] ] 출력
  expect('dd').toBe('dd')
})
```
- jest.fn()으로 목함수 생성
- mockFn.mock.calls는 목함수가 실행되었을 때의 인자를 가진다.
- calls.length로 호출된 횟수를 테스트하거나 인자들의 값으로 테스트함

예시
```javascript

const mockFn = jest.fn()

const forEachAdd1 = (arr) => {
  arr.forEach(num => {
    //fn(num + 1)
    mockFn(num + 1)
  })
}

forEachAdd1([10, 20, 30]);
test('함수 호출은 3번 됩니다.', () => {
  expect(mockFn.mock.calls.length).toBe(3)
})
test('전달된 값은 11, 21, 31 입니다.', () => {
  expect(mockFn.mock.calls[0][0]).toBe(11)
  expect(mockFn.mock.calls[1][0]).toBe(21)
  expect(mockFn.mock.calls[2][0]).toBe(31)
})
```
- 배열을 순회하면서 배열값 + 1을 인자로 함수 fn을 실행시키는 forEachAdd1이라는 함수가 있고, 아직 fn이라는 함수는 만들지 않은 상태이다.
- fn을 만들기 전에 목함수로 호출 횟수와 전달한 인자를 테스트해서 


#### results
```javascript

const mockFn = jest.fn(num => num + 1)

mockFn(10)
mockFn(20)
mockFn(30)
console.log(mockFn.mock.results)
/*[
      { type: 'return', value: 11 },
      { type: 'return', value: 21 },
      { type: 'return', value: 31 }
    ]
*/
```

- results는 결과값을 리턴한다.

```javascript
const mockFn = jest.fn()
mockFn
  .mockReturnValueOnce(true)
  .mockReturnValueOnce(false)
  .mockReturnValueOnce(true)
  .mockReturnValueOnce(false)
  .mockReturnValue(true)

const result = [1, 2, 3, 4, 5].filter((num) => mockFn(num))

test("홀수는 1, 3, 5", () => {
  expect(result).toStrictEqual([1, 3, 5])
})
```

- mockReturnValueOnce는 목함수의 리턴값을 테스트 중간에 주입 가능하다.
- 연속적으로 값을 전달하는 형태의 코드에서 효율적으로 사용 가능
- mockResolvedValue는 비동기의 resolve를 흉내냄

#### 목킹 모듈
```javascript
const { createUser } = require("./fn")

test('유저를 만든다.', () => {
  const user = createUser('Mike')
  expect(user.name).toBe('Mike')
})
```
- 실제로 db에 유저를 생성하는 함수인 createUser 함수가 있다고 하자.
- 테스트를 할 때마다 테스트유저를 db에 만들면 이후 직접 삭제해줘야하는 귀찮음이 있다.
- createUser를 mocking module로 만들어서 실제 함수를 대체한다.

```javascript
const { createUser } = require("./fn")

jest.mock('./fn')
createUser.mockReturnValue({name : 'Mike'})

test('유저를 만든다.', () => {
  const user = createUser()
  expect(user.name).toBe('Mike')
})
```
- 위와 같이 createUser를 모킹 모듈로 만들면 실제 createUser코드는 실행되지 않고 mockReturnValue에 지정한 리턴값을 반환한다.


### 리액트 컴포넌트 + 스냅샷 테스트
- @testing-library/react 라이브러리로 테스트내에서 렌더링하고, 해당 스크린에 문자열을 검색하는 방식으로 테스트 할 수 있다.
- 스냅샷은 성공하는 케이스를 저장해두고 비교하면서 테스트하는 방식이다.
- toMatchSnapshot() 메소드로 스냅샷을 생성하고 비교함
- 스냅샷 업데이트는 npm test후 터미널에서 업데이트 명령어를 치면 됨
- 만약 버그가 있어서 실패한 것인데 꼼꼼이 살펴보지않고 스냅샷을 업데이트해버리면 다음부턴 버그가 있는 스냅샷으로 테스트를 하게 되니 신중하게 선택해야 한다
- 타이머 같은 컴포넌트는 매번 출력되는 시간이 바뀌므로 스냅샷 테스트를 할때마다 fail이 뜰 것이다.
- Date.now를 목함수를 써서 고정된 값으로 만든다.