## 4. GobalFetch

[Reference: GlobalFetch](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch)

_**重要**_：因为Firefox 52中，这个方法定义已经从GlobalFetch中移到了[WindowOrWorkerGlobalScope](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope)的mixin里，其他的浏览器则继续沿用GlobalFetch的mixin

GlobalFetch的mixin的Fetch API包含了[GlobalFetch.fetch\(\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch)方法用来启动资源获取的过程。GlobalFetch同时被[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)和[WorkerGlobalScope](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope)实现了，也就是说，它在您所期望的很多资源读取的纯粹上下文环境中是可用的。

#### Properties

None.

### Methods





