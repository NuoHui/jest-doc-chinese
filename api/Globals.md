# Globals

在你的测试文件中，Jest会为你提供如下的方法、对象到全局环境中，因此你不需要通过`require`或者`import`引用后再使用。当然，如果你更喜欢显示的声明，你也可以这么做`import {describe, expect, test} from '@jest/globals'`。

## Methods

### afterAll(fn, timeout)

在测试文件中，所有的测试套件执行完成后执行一个函数。如果该函数返回的是一个`Promise`或者`Generator`，Jest会等待`Promise`执行完(resolve)。

`afterAll`有提供一个默认参数`timeout`(单位是毫秒)，用于指定在中止前等待多长时间。注意：默认超时为5秒。

如果要清理测试的某些全局设置状态，这个方法通常是有用的。

比如：

```javascript
const globalDatabase = makeGlobalDatabase();

function cleanUpDatabase(db) {
  db.cleanUp();
}

afterAll(() => {
  cleanUpDatabase(globalDatabase);
});

test('can find things', () => {
  return globalDatabase.find('thing', {}, results => {
    expect(results.length).toBeGreaterThan(0);
  });
});

test('can insert a thing', () => {
  return globalDatabase.insert('thing', makeThing(), response => {
    expect(response.success).toBeTruthy();
  });
});
```

在这个例子中，`afterAll`会确保`cleanUpDatabase`在所有测试套件执行完毕后运行。

如果`afterAll`是在一个`describe`块里面，你可以理解为它也是有作用域的，会被限定在该`describe`内。

如果您想在每次测试执行之后运行一些清理逻辑，而不是在所有测试执行完毕之后，请使用`afterEach`。

### afterEach(fn, timeout)

在测试文件中，每执行完一个测试套件后执行一个函数。如果该函数返回的是一个`Promise`或者`Generator`，Jest会等待`Promise`执行完(resolve)。

`afterEach`有提供一个默认参数`timeout`(单位是毫秒)，用于指定在中止前等待多长时间。注意：默认超时为5秒。

如果你想要清理每个测试套件执行后产生的临时状态等，这个方法是非常有用的。

比如：

```javascript
const globalDatabase = makeGlobalDatabase();

function cleanUpDatabase(db) {
  db.cleanUp();
}

afterEach(() => {
  cleanUpDatabase(globalDatabase);
});

test('can find things', () => {
  return globalDatabase.find('thing', {}, results => {
    expect(results.length).toBeGreaterThan(0);
  });
});

test('can insert a thing', () => {
  return globalDatabase.insert('thing', makeThing(), response => {
    expect(response.success).toBeTruthy();
  });
});
```

在这个例子中，`afterEach`会确保`cleanUpDatabase`在所有测试套件执行完毕后运行。

如果`afterEach`是在一个`describe`块里面，你可以理解为它也是有作用域的，会被限定在该`describe`内。

如果你想在运行所有测试套件后，一次性的进行清理工作，那么你需要`afterAll`。

### beforeAll(fn, timeout)

在此文件运行任何测试套件之前运行一个函数。如果该函数返回的是一个`Promise`或者`Generator`，Jest会等待`Promise`执行完(resolve)。

`beforeAll`有提供一个默认参数`timeout`(单位是毫秒)，用于指定在中止前等待多长时间。注意：默认超时为5秒。

如果你需要在允许测试套件之前，做一些准备工作，比如设置全局状态或者对象等，那么这个方法是非常有用的。

例如：

```javascript
const globalDatabase = makeGlobalDatabase();

beforeAll(() => {
  // Clears the database and adds some testing data.
  // Jest will wait for this promise to resolve before running tests.
  return globalDatabase.clear().then(() => {
    return globalDatabase.insert({testData: 'foo'});
  });
});

// Since we only set up the database once in this example, it's important
// that our tests don't modify it.
test('can find things', () => {
  return globalDatabase.find('thing', {}, results => {
    expect(results.length).toBeGreaterThan(0);
  });
});
```
在这个例子中，`beforeAll`会确保在所有测试套件运行之前，`database` 已经设置好。此外还有重要的一点，`beforeAll`既支持同步，也支持异步逻辑(它会等待你的`Promise`执行完)。

如果`afterEach`是在一个`describe`块里面，你可以理解为它也是有作用域的，会被限定在该`describe`内。

如果您想在每个测试之前运行某些逻辑而不是在所有测试套件运行之前，请使用`beforeEach`。

### beforeEach(fn, timeout)

在文件运行每个测试套件之前运行一个函数。如果该函数返回的是一个`Promise`或者`Generator`，Jest会等待`Promise`执行完(resolve)。

`beforeEach`有提供一个默认参数`timeout`(单位是毫秒)，用于指定在中止前等待多长时间。注意：默认超时为5秒。

如果你想在每个测试运行之前重置一些全局状态，那么这个函数是很有用的。

比如：

```typescript
const globalDatabase = makeGlobalDatabase();

beforeEach(() => {
  // Clears the database and adds some testing data.
  // Jest will wait for this promise to resolve before running tests.
  return globalDatabase.clear().then(() => {
    return globalDatabase.insert({testData: 'foo'});
  });
});

test('can find things', () => {
  return globalDatabase.find('thing', {}, results => {
    expect(results.length).toBeGreaterThan(0);
  });
});

test('can insert a thing', () => {
  return globalDatabase.insert('thing', makeThing(), response => {
    expect(response.success).toBeTruthy();
  });
});
```
在这里，`beforeEach`会确保`database`在每次执行测试之前被重置。

如果`beforeEach`是在一个`describe`块里面，你可以理解为它也是有作用域的，会被限定在该`describe`内。


如果您想在任何测试套件运行之前只运行一次设置状态的代码，请使用`beforeAll`。

### describe(name, fn)

`describe(name, fn)`会创造一个块，在这个块内我们可以组合多个相关的测试套件。例如：

```typescript
const myBeverage = {
  delicious: true,
  sour: false,
};

describe('my beverage', () => {
  test('is delicious', () => {
    expect(myBeverage.delicious).toBeTruthy();
  });

  test('is not sour', () => {
    expect(myBeverage.sour).toBeFalsy();
  });
});
```

当然这也不是必须的，你也可以直接在顶级作用于编写`test block`。但是如果你更喜欢组合多个`test`，使用`describe`是非常方便的。

此外，`describe`也支持嵌套的。

```typescript
const binaryStringToNumber = binString => {
  if (!/^[01]+$/.test(binString)) {
    throw new CustomError('Not a binary number.');
  }

  return parseInt(binString, 2);
};

describe('binaryStringToNumber', () => {
  describe('given an invalid binary string', () => {
    test('composed of non-numbers throws CustomError', () => {
      expect(() => binaryStringToNumber('abc')).toThrowError(CustomError);
    });

    test('with extra whitespace throws CustomError', () => {
      expect(() => binaryStringToNumber('  100')).toThrowError(CustomError);
    });
  });

  describe('given a valid binary string', () => {
    test('returns the correct number', () => {
      expect(binaryStringToNumber('100')).toBe(4);
    });
  });
});
```

### describe.each(table)(name, fn, timeout)





