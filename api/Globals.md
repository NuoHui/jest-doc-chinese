# Globals

在你的测试文件中，Jest会为你提供如下的方法、对象到全局环境中，因此你不需要通过`require`或者`import`引用后再使用。当然，如果你更喜欢显示的声明，你也可以这么做`import {describe, expect, test} from '@jest/globals'`。

## Methods

### afterAll(fn, timeout)

当你的文件中所有的测试套件执行完成后执行一个函数。如果该函数返回的是一个`Promise`或者`Generator`，Jest会等待`Promise`执行完(resolve)。

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









