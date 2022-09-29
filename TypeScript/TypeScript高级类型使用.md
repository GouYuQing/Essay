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

```ts
/**
 * Exclude from T those types that are assignable to U
 */
type Exclude<T, U> = T extends U ? never : T;
```

