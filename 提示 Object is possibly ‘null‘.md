# 提示 Object is possibly ‘null‘

### 方案

```tsx
let table = document.querySelector("body");
      if (table) {
        table.setAttribute("style", "background-color:#f7f7f7");
      }
      // document
      //   .querySelector("body")
      //   .setAttribute("style", "background-color:#f7f7f7");
```

