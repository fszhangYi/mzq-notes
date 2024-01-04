Umi 是一个可插拔的企业级前端应用框架，它内置了基于 `react-router` 的路由系统，并对 `history` 对象进行了封装，以便更方便地进行路由管理和导航控制。本文将具体介绍 Umi 框架中 `history` 对象的常用方法及使用示例。

## 🔄 `history.push(path, [state])`

**`push`** 方法用于在历史堆栈中添加一个新的条目，并导航到指定的路径。

```javascript
import { history } from 'umi';

function navigateToHome() {
  history.push('/home');
}

function navigateWithState() {
  history.push('/home', { fromDashboard: true });
}
```

上述代码演示了如何使用 **`push`** 方法，第一个例子简单地导航到 `/home` 路径，而第二个例子在跳转时传递了一个状态对象。

## 🔄 `history.replace(path, [state])`

**`replace`** 方法用于替换历史堆栈中当前的条目，并导航到指定路径。

```javascript
import { history } from 'umi';

function replaceToHome() {
  history.replace('/home');
}

function replaceWithState() {
  history.replace('/home', { fromDashboard: true });
}
```

与 **`push`** 方法相比，**`replace`** 不会在历史记录中添加新条目，而是替换掉当前页的记录。

## ⏪ `history.goBack()`

**`goBack`** 方法用于模拟浏览器的后退按钮，让用户返回到历史记录的上一个页面。

```javascript
import { history } from 'umi';

function goBack() {
  history.goBack();
}
```

当用户需要返回到之前浏览的页面时，可以调用此方法。

## ⏩ `history.goForward()`

**`goForward`** 方法用来模拟浏览器的前进按钮，使用户前进到历史记录的下一个页面。

```javascript
import { history } from 'umi';

function goForward() {
  history.goForward();
}
```

当我们希望用户前进到下一个页面时使用此方法。

## 🎧 `history.listen(listener)`

**`listen`** 方法用于监听路由变化，当路由变化时，会调用此监听函数。

```javascript
import { history } from 'umi';

function handleLocationChange(location, action) {
  console.log(action, location.pathname, location.state);
}

const unlisten = history.listen(handleLocationChange);

// 在适当的时候，取消监听
// unlisten();
```

监听函数接收新的 `location` 对象和 `action`（如 "PUSH" 或 "POP"）作为参数。

## 🚫 `history.block(prompt)`

**`block`** 方法用于阻止导航，通常用在用户离开当前页面前进行确认。

```javascript
import { history } from 'umi';

const unblock = history.block('Are you sure you want to leave this page?');

// 在适当的时候，取消阻塞
// unblock();
```

**`block`** 方法返回一个函数，当不再需要阻止导航时，调用该函数可取消阻塞。

以上介绍了 Umi 框架中 **`history`** 对象的几个常用方法及其使用示例。熟练掌握这些方法有助于更好地管理路由状态和控制应用导航逻辑。
