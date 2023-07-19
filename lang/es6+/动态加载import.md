## ES6 动态加载 import

以react为例，在React项目中尝试运行下面的代码：

### Brainwash.jsx

```jsx
export default () => {
  return (
    <div className="brain-wash">
      <h2>Brain Washed</h2>
    </div>
  );
};
```

### App.jsx

```jsx
import { useState } from 'react';

export default () => {
  const [Brainwash, setBrainwash] = useState<any>(null);

  const loadBrainwash = async () => {
    const { default: Brainwash }: any = await import('./Brainwash');
    setBrainwash(Brainwash);
  };

  return (
    <div className="app">
      <button onClick={loadBrainwash}>Wash</button>
      {Brainwash && Brainwash}
    </div>
  );
};
```
注：`Brainwash.default` 是JSX片段，因此显示时是`Brainwash`不是`<BrainWash />`