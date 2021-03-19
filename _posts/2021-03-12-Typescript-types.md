---
layout: post
---

Firstly, I was developing code in Java, and I was happy, but then I moved to Javascript, nightmares came. Looking for a promising future, 
my next move was to Typescript, and I have to say, it is not that promising, but it improves my sleep during the nights. 

I always getting problems when I add types, I know it's because I'm not that expert in Typescript, so here there is a list of several
things I learn of some how-tos add types:

# If you want to return an object of a defined type or an empty object inside a function

```typescript
interface test {
    a: string,
    b: int
}

function createTest(exists: boolean): test {
    // Create test
    const test = new Test();
    if (exists) {
        return test;
    }
    else {
        return {} as test;
    }
}
```