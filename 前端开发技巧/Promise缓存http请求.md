# 使用Promise缓存网络请求

网络请求是现代Web应用中的常见操作，很多时候需要获取服务器上的数据。在进行网络请求时，为了减轻服务器的压力和提高客户端的响应速度，缓存策略常被用来避免对同一数据的重复请求。本文将探讨如何使用Promise结合缓存来高效处理网络请求。

## 背景与概念

在进行网络请求的应用中，尤其是数据变化不频繁的场景下，缓存是一个常用而有效的优化手段。缓存可以减少网络延迟，避免不必要的数据传输，节省带宽，提高用户体验。但是，不当的缓存策略可能导致用户看到过时的数据。因此，实现一个合理的缓存策略是非常重要的。

Promise是JavaScript中处理异步操作的一种模式，它代表一个尚未完成但预计将来会完成的操作。通过Promise，可以将异步操作队列化，链式处理，并在适当的时候进行错误处理。

## 缓存网络请求的需求与挑战

在许多情况下，当多个组件或者页面需要相同的数据时，每个组件各自发起网络请求显得非常低效。这需要一种机制能够“共享”已经发起的请求，当请求完成后，所有需要这份数据的组件都能获得到。同时，若数据已经在本地缓存，则无需重新发起网络请求，直接使用缓存数据即可。

实现这样的功能，有如下几个挑战：

1. **请求的复用性**：避免同一数据的重复请求。
2. **请求的并发管理**：针对相同的数据来源同一时间只发送一次请求。
3. **缓存的合理性**：缓存有效数据，但要避免过时数据的问题。
4. **缓存的容错性**：当请求失败时，如何处理以及如何通知依赖这份数据的其他部分。

## Promise缓存网络请求的实现

具体到实现，可以创建一个缓存对象，用于存储已经发起的请求的Promise对象。在请求发起前，先检查缓存中是否已存在相应的Promise；如果存在，直接返回该Promise；如果不存在，则发起新的请求，并将请求的Promise存储到缓存中。

以下是一个简化的实现示例，逐一解释核心代码：

```typescript
import utils from '@/utils';

export const reqThenCacheData = (
  name: string
): Promise<void | Promise<utils.ICachedData>> => {
  // 尝试获取缓存数据
  const cache = utils.cacheForData.get(name);

  // 如果存在缓存，直接返回缓存中的Promise对象 
  if (cache) return cache;

  // 没有缓存，构造请求URL，发起网络请求
  const url = `http://*******?name=${name}`;
  const currentHandler = (
    request.get({ url }) as unknown as Promise<Array<utils.ICachedListData>>
  )
    .then((data: Array<utils.ICachedListData>) => {
      // 对返回的数据进行处理，得到一个子请求列表
      const subList: Array<string> = data.map((sub) => {
        if (sub.is_default === 1) return sub.subName;
      });
      return subList.filter((item) => item);
    })
    .then((subNames: Array<string>) => {
      // 根据第一次请求的结果，构建第二次请求的Promise数组
      const subRequestPromises: Array<Promise<utils.ICachedData>> = subNames.map((subName: string) => {
        const subUrl = `http://*******?subName=${subName}`;
        return request.get({ url: subUrl }) as unknown as Promise<utils.ICachedData>;
      });
      // 使用Promise.all等待所有子请求完成
      return Promise.all(subRequestPromises);
    })
    .then((subData: Array<utils.ICachedData>) => {
      // 可以进一步处理所有子请求的结果
      // 示例仅返回其中一个子请求结果
      return subData[0];
    })
    .catch((error) => {
      console.log('请求数据失败！', error);
      // 错误处理：可以决定是否从缓存中移除错误的Promise
      utils.cacheForData.delete(name);
      // 根据需要，可以在这里抛出错误或返回一个默认值
      throw error;
    });

  // 存入缓存，便于下次直接使用
  utils.cacheForData.set(name, currentHandler);
  return currentHandler;
};
```

### 缓存逻辑概述

上述代码演示了基于Promise的网络请求缓存机制。首先，通过调用`cacheForData.get(name)`尝试获取缓存中已存在的请求Promise，如果找到，则直接返回，避免重复请求。

如果缓存中没有找到请求Promise，那么将发起一个新的网络请求。请求返回的Promise通过链式的`.then()`方法进行处理。这些`.then()`方法负责对返回的数据进行处理和转化，使用者可以根据实际情况添加具体的数据处理逻辑。

最后，使用`catch()`方法处理可能发生的错误，并且将当前请求的Promise对象使用`cacheForData.set(name, currentHandler)`方法存储到缓存中，以便后续重用。

### 多阶段请求处理

上述代码展示了如何处理一个需要多次网络请求的场景。系统首先检查缓存中是否有目标数据的Promise，在缓存未命中时，发起一个初始网络请求。取得初始数据后，提取必要的信息组成新的请求数组。

利用`Promise.all()`方法，可以并行处理多个网络请求，这个方法返回一个新的Promise，它将在所有请求完成时解析。这一过程是Promise缓存实现的核心，它使得各个请求之间可以共享状态，并在全部请求都完成后统一返回结果。

这种缓存策略的一个关键优势在于它的复用性和并发管理能力。即使有多个请求同时要求相同的资源，由于缓存机制的存在，真实的网络请求只会发生一次。每个后续尝试访问此数据的操作都会得到一个挂起的Promise，而非触发新的网络请求。

### 缓存及请求细节处理

在网络请求Promise后串联多个`.then()`方法时，是在处理与转换原始请求返回的数据。示例中，首先处理的是将返回的列表按照特定条件筛选，然后对筛选后的结果进行进一步的请求。这里的关键是`Promise.all()`，它确保了同时处理多个异步操作。

处理器`currentHandler`的最后结果是一个新的Promise，它代表了一系列依赖于原始请求并经过一定处理的数据。这个Promise作为最终结果，将被缓存并返回给调用者。

缓存和请求的一个重要细节是错误处理。当请求出现错误时，通常不应该将错误的Promise存储到缓存中，因为这可能导致后续所有对该数据的请求都会立即返回错误。相反，可以选择在错误处理逻辑中移除缓存项，或者采取其他的恢复机制。

## 缓存策略的考虑

实现缓存时，还需要考虑它的有效性和过时机制。只有当数据不频繁变化或者对即时性要求不高的情况下，缓存才是合理的。尤其是在一些数据更新周期较长的场景，例如一些配置数据、用户信息等，使用缓存能带来显著的性能提升。

有时候，需要设立缓存过期机制，即数据在缓存中存储一段时间后失效，之后的请求将强制发起新的网络请求以获取最新数据。缓存过期机制可以通过定时器或者请求次数等方式来实现。

## 结语

合理地使用Promise和缓存可以显著提升应用的性能，减轻服务器的负担，并提供更加流畅的用户体验。通过在正确的地方引入缓存，可以有效地避免不必要的网络请求，加快数据的加载速度。在此基础上，合理设置缓存失效机制，能够确保用户始终获取到最新的数据，避免缓存导致的数据过时问题。

通过上述示例的实践，开发者可以根据自身应用的需要，定制适合自己业务场景的网络请求缓存方案。这不仅可以提高程序性能，还可以增加程序的鲁棒性，优化用户的交互体验。