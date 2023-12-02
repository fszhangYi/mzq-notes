# 封装一个JS递归模板

在 JavaScript 的森林中，递归是那条弯曲蜿蜒的小径，引领着旅人深入数据结构的密林之中。递归的脚步轻盈，踏过一条条分支，每个节点都记录了一次探索的记忆。本文将以DOM元素的深度遍历为例——这是一个普遍而又具体的应用场景——带您一起撰写一个封装良好的递归模板。这个模板不仅会在遍历中标记每一个DOM元素，还将设法搜集所有的 `<a>` 标签，在深邃蔚蓝的编程海洋中，这份模板将成为一颗闪耀的星辰。

## 递归模板的设计理念

递归模板的设计需要考虑几个重要的维度：
1. 类型约束：确保只处理期望的数据类型。
2. 私有域：创建独立的作用域，防止变量冲突。
3. 出口条件：设定一个或多个递归结束的条件。
4. 递归方向：确定下一步递归的具体方向。
5. 操作执行：在每次递归中执行特定操作。

通过精心设计这些部分，可以确保递归不仅稳定可靠，还足够灵活，能应对不同的数据处理需求。

## 递归模板的构建

将目光投至一段暗藏玄机的代码——这里展现了如何通过递归沿着DOM树悄然前行，从根节点深入到每一个子节点，并在每一步中执行有意义的操作。

### 类型约束

起始之前，必须约束递归所处理的数据类型。在本例中，递归模板专门处理 `HTMLElement` 数组。

```typescript
// 唯一入口参数类型
type IIput = Array<HTMLElement>;
// 最终返回的数据结构
type IData = { max: number, aArray: IIput };
```

如同树木需脚踏坚实的土地，类型的明确为递归的执行提供了坚实基础。

### 创建递归私有域

在JavaScript的世界中，私有域允许我们在一个封闭的空间进行变量的声明和操作，把外界的喧嚣隔绝在外。

```typescript
function recurContainer(nodeArray: IIput): IData {
  // 这里初始化返回值，出口条件设置函数，递归方向确定函数及既定操作函数...
}
```

在函数 `recurContainer` 中，创建了一个递归的容器，其中包含了所有相关的私有方法和初始化数据。域中，`Symbol` 是遍历路径上的神秘符号，保护着函数名不被外部世界所知晓。

### 出口条件设置函数

一个明智的递归设计者，总是确立明确的归途。

```typescript
// 出口条件设置函数
const exit = function (): boolean {
  return this?.children.length === 0;
};
```

每一次递归的展开一定有着清晰的结束之路。在这里，如果当前遍历的DOM元素没有子元素，则意味着到达了递归的出口。

### 递归方向确定函数

递归的旅人需要方向，这便是由 `next` 函数指引的。

```typescript
// 递归方向确定函数
const next = function (): IIput {
  Reflect.deleteProperty(this, snext);
  return [...this?.children];
};
```

在这段代码中，`next` 函数确定了下一步的递归路径——子元素的集合。在每一步中，它以连贯的逻辑指出了向下深入的方向。

### 完成既定操作函数

递归的魅力，在于它访问每个节点时如同艺术家的细心，细致地在每个元素上留下痕迹。

```typescript
// 完成既定操作函数
const work = function (): void {
  this.setAttribute('ok', 'ok');
  Reflect.set(context, 'max', Math.max(this.childElementCount, context.max));
  if (this?.tagName === 'A') {
    Reflect.get(context, 'aArray').push(this);
  }
};
```

`work` 函数在DOM树中的每一个节点上执行操作，既留下了 `'ok'` 属性的印记，也收集了 `<a>` 标签，并记录了子元素的最大数量。

### 递归函数的精心雕琢

处心积虑，将所有零散的思考和构想织成一个完整的递归函数 `recursion`，将捕获每一个DOM元素。

```typescript
// 递归函数
function recursion(currents: IIput): void {
  // 此处为具体的递归算法...
}
```

在迷人的语言世界中，递归就像是叙述故事的叙事者，在逻辑的藤蔓上攀爬，不放过一个分支，不错过一个元素，直至故事走向终结，然后递归的美梦随着返回值醒来。

## 递归精髓的展示：既定操作

递归的真正魅力，在于它如何执行既定操作。这些操作就像艺术家的笔触，一次次涂抹在DOM的画布上。

```typescript
current[swork]();
```

每当递归访问到一个DOM元素，`work` 函数便被调用。它像是熟悉的老朋友，拜访每一道风景，留下足迹，也寻找宝藏。

## 结语：递归模板的应用

终于，这一段递归的旅程归于平静。设置适当的出口条件，确定递归的方向，执行既定的操作，融会贯通了这段复杂而优雅的代码。递归不再是始终莫测的秘境，而是一种可靠而高效的解决方法。

```typescript
recurContainer(Array.of(document.body));
```

在长篇叙述的终结，无需悬念的显示结果清晰在前：每一个DOM元素被赋予了新的属性，宛若恒星闪烁在浩瀚的文档之河，宝贵的`<a>`标签也一一被收入囊中。递归的魔力无处不在，回旋腾挪之间，展示出JavaScript深度遍历的无限可能。

## 附录：全部代码
```typescript
// 类型约束

// 唯一入口参数类型
type IIput = Array<HTMLElement>;
// 最终返回的数据结构
type IData = { max: number, aArray: IIput };

// 创建递归私有域
function recurContainer(nodeArray: IIput): IData {

  // 唯一值句柄
  const sexit = Symbol('exit');
  const snext = Symbol('next');
  const swork = Symbol('work');

  // 初始化返回值
  const context: IData = { max: 0, aArray: [] };

  // 出口条件设置函数
  const exit = function (): boolean {
    return this?.children.length === 0;
  };

  // 递归方向确定函数
  const next = function (): IIput {
    // 删除增强
    Reflect.deleteProperty(this, snext);
    return [...this?.children];
  };

  // 完成既定操作函数
  const work = function (): void {

    // 操作前数据存储

    // 遍历对象本身操作
    this.setAttribute('ok', 'ok');

    // 操作后数据存储
    Reflect.set(context, 'max', Math.max(this.childElementCount, context.max));
    if (this?.tagName === 'A') {
      Reflect.get(context, 'aArray').push(this);
    }

  };

  // 递归函数
  function recursion(currents: IIput): void {

    // 入参合法性判断
    if (Reflect.getPrototypeOf(currents) !== Array.prototype) { return; }

    // 开始遍历
    for (let i: number = 0; i < currents.length; i++) {

      // 递归+遍历
      let current = currents.at(i) as any;

      // 对象增强
      Reflect.set(current, sexit, exit);
      Reflect.set(current, snext, next);
      Reflect.set(current, swork, work);

      // 既定操作
      // Reflect.get(current,swork).call(current);
      current[swork]();

      // 出口判定
      // if (Reflect.get(current,sexit).call(current)) {
      if (current[sexit]()) {
        continue;
      } else {

        // 删除增强
        Reflect.deleteProperty(current, sexit);
        Reflect.deleteProperty(current, swork);

        // 向下递归
        // recursion(Reflect.get(current,snext).call(current));
        recursion(current[snext]());
      }
    }
  }

  // 开始递归
  recursion(nodeArray);

  // 返回结果
  return context;
}

// 获取结果
recurContainer(Array.of(document.body)); // 使用Array.of()保证传递的是一个数组！

/*上例是给每一个DOM增加了一个ok属性，并得到了所有的a标签。*/
```