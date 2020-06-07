---
path: blog
date: 2020-06-07T06:14:28.358Z
title: Typescript Union & Intersection
description: >-
  Union and Intersection is an important feature in Typescript. Let's figure it
  out!
---
## Union & Intersection

---

Let's say we have two type `A` , `B`

```tsx
type A = {
  prop1: number;
  porp2: number;
  prop3: number;
}

type B = {
  prop3: number;
  porp4: number;
  prop5: number;
}
```

- Union: `U = A | B`
    - `U` must includes all the members in `A` or all the members in `B`

    ```tsx
    type AOrB = A | B;

    // valid
    const aOrb: AOrB = {
      prop1: 1,
      porp2: 1,
      prop3: 1,
    };

    // valid
    const aOrb: AOrB = {
      prop3: 1,
      porp4: 1,
      prop5: 1,
    };

    // valid
    const aOrb: AOrB = {
      prop1: 1,
      porp2: 1,
      prop3: 1,
      porp4: 1,
    };

    // valid
    const aOrb: AOrB = {
      prop1: 1,
      porp2: 1,
      prop3: 1,
      porp4: 1,
      prop5: 1,
    };

    // inValid
    const aOrb: AOrB = {
      prop1: 1,
      // porp2: 1,
      prop3: 1,
      porp4: 1,
    };
    ```

    - `U` can only access the member which is guarantee will exist in both `A` and `B`

    ```tsx
    //valid
    aOrb.prop3

    // inValid
    aOrb.prop1
    ```
- Intersection: `I = A & B`
    - `I` must includes all the member in `A` and `B`

```tsx
type AAndB = A & B;

// valid
const aAndB: AAndB = {
  prop1: 1,
  porp2: 1,
  prop3: 1,
  porp4: 1,
  prop5: 1
}

// inValid
const aAndB: AAndB = {
  prop1: 1,
  porp2: 1,
  prop3: 1,
  porp4: 1,
}
```

## Never

---

No Intersection

```tsx
type A = string & number
// A will be never
```

## Reference

---

[Day 17. 機動藍圖・複合型別 X 型別複合 - TypeScript Union & Intersection - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10216794)

[Conditional Types #](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)
