# 原生API-FileReader扩展-toPromise

为了跳出回调地狱，尽量把所有异步函数用`async`及`await`关键字来写，也就是需要把异步函数包装成返回会`promise`对象的函数，本文档是给FileReader API添加`toPromise`函数。

```typescript
// index.d.ts

interface FileReader {
  toPromise(): Promise<ProgressEvent<FileReader>>
}
```

```typescript
// file-reader.fill.ts

FileReader.prototype.toPromise = function<T>(): Promise<T> {
  return new Promise<T>((resolve, reject) => {
    this.onload = (...rest: any) => {
      resolve(...rest)
    }

    this.onerror = (...rest: any) => {
      reject(...rest)
    }
    
    // 这里只重写了load和error，如果需要重写其他的事件，请自行添加
  })
}
```

