# Typescript 3.7.2新特性

## 新特性

### 可选链 <a id="optional-chaining"></a>

```typescript
let x = foo?.bar.baz();
```

等效于：

```typescript
let x = (foo === null || foo === undefined) ?
    undefined :
    foo.bar.baz();
```

{% hint style="warning" %}
注意：当`baz`为`undefined`时，仍然会报错，这时需要再次判断`baz`是否为空：

```typescript
let x = foo?.bar.baz?.call(null);
```
{% endhint %}


