## Promise

### 定义

ECMAScript 6 新增引用类型Promise。可以通过new操作符来实例化，存在三种状态：

待定（`pending`）

兑现（`fullfilled`,有时候也称为“解决”，`resolved`）

拒绝（`rejected`）

状态不可逆，一旦状态确定就不再改变。

### resolve()和reject()

调用`resolve()`把状态转为兑现，调用`reject()`把状态切换为拒绝。由于Promise的状态是i私有的，所以只能在内部进行操作。

