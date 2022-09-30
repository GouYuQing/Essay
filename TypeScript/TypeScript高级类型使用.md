# 一些TypeScript高级类型使用记录

## 1.Partial

参数可选，它会构造一个新的类型，这个类型会将之前类型中的所有属性都变为可选。

```ts
/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

```ts
interface Person {
   name: string
   age: number
   sex: string
}
type personInfo = Partial<Person>
//结果
type personInfo = {
    name?: string | undefined
    age?: number | undefined
    sex?: string | undefined
}
```

## 2.Omit

去除类型中的某些属性

```ts
/**
 * Construct a type with the properties of T except for those in type K.
 */
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

```ts
type personInfo = Omit<Person, "name" | "age">
//结果
type personInfo = {
    sex: string
}
```

## 3.Pick

选取类型中的某些属性

```ts
/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

```ts
type personInfo = Pick<Person, "age" | "sex">
//结果
type personInfo = {
    age: number
    sex: string
}
```

## 4.Record

定义一个对象的key和value

```ts
/**
 * Construct a type with a set of properties K of type T
 */
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

```ts
const enum SortEnum {
  ASCEND = "ascend",
  DESCEND = "descend",
}
  const sortMap: Record<SortEnum, string>={
      [SortEnum.ASCEND]: "升序",
      [SortEnum.DESCEND]: "降序"
  }
```

## 5.Required

类型中所有的属性变成必选的，和Partial相反

```ts
/**
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

```ts
interface Person {
   name？: string
   age？: number
   sex？: string
}
type personInfo = Required<Person>
//结果
type personInfo = {
    name: string
    age: number
    sex: string
}
```

## 6.Exclude

从T中排除可以分配给U的类型

```ts
/**
 * Exclude from T those types that are assignable to U
 */
type Exclude<T, U> = T extends U ? never : T;
```

```ts
type A = number | string | boolean
type B = number | string
type C = Exclude<A, B>
//结果
type C = boolean
```

## 7.Extract

从T中提取那些可赋值给U的类型

```ts
/**
 * Extract from T those types that are assignable to U
 */
type Extract<T, U> = T extends U ? T : never;
```

```ts
type A = number | string | boolean
type B = number | string
type C = Extract<A, B>
//结果
type C = number | string
```

## 8.keyof

`keyof` 可以用来取得一个对象接口的所有 key 值

```js
interface Person {
  name: string;
  age: number;
  gender: "male" | "female";
}
type PersonKey = keyof Person;
//结果
type PersonKey = 'name'|'age'|'gender';


//当获取any的keyof时，得到的数据是？
type Keys = keyof any
// Keys 类型为 string | number | symbol 组成的联合类型,因为any可以是任何类型
```



```ts
//假如要获取一个对象的value，传入这个对象和key值，获取到value
//普通方法写
function getValueByKey(p: Person, key: PersonKey) {
  return p[key];
}
//ts实现
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key]; 
}

let val = getValueByKey({ name: "hello", age: 10, gender: "male" }, "name");
console.log(val);
```

