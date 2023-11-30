# Dexie项目实战

在现代Web应用开发中，浏览器端的本地数据存储变得日益重要。Dexie.js是一个强大的IndexedDB封装库，它通过简化IndexedDB的使用难度，提供了更为直观和友好的API来管理浏览器数据库。在实际项目中，Dexie.js可以用于存储应用的配置数据、缓存请求数据等，以提高应用的性能和用户体验。以下是一个使用Dexie.js在项目中实现数据缓存和读取的实践案例。

## Dexie.js概述

Dexie.js为IndexedDB提供了一系列的简化操作，开发者可以通过它很方便地定义数据库、添加记录、检索数据等。它的使用遵循Promise风格，能够很好地融入现代的异步编程环境。

## 设计库结构

在实践中，首先需要定义数据库和其架构。以下是`CommonPanelDB`类的结构，它为面板类型(`panelTypes`)和面板数据(`panelData`)定义了两张表：

```typescript
import Dexie from 'dexie';

export interface CommonPanel {
  id?: number;
  typeName?: string;
  panelId?: string;
}

export interface CommonPanelData {
  id?: number;
  panelId?: string;
  content?: string;
}

export class CommonPanelDB {
  panelDB: Dexie | null;
  panelTypes: Dexie.Table<CommonPanel, number> | undefined;
  panelData: Dexie.Table<CommonPanelData, number> | undefined;

  constructor() {
    this.panelDB = new Dexie('commonPanel');
    this.panelDB!.version(1).stores({
      panelTypes: '++id,typeName,panelId',
      panelData: '++id,panelId,content',
    });

    this.panelTypes = this.panelDB!.table('panelTypes');
    this.panelData = this.panelDB!.table('panelData');
  }
  // ...
}
```

通过Dexie库的`version`方法，定义了数据库的版本和表的结构。`++id`表示主键`id`是自增的。`panelTypes`表将存储面板类型信息，而`panelData`则存储具体的面板内容。

## 数据库初始化

定义好数据库架构后，接下来是初始化过程。初始化主要做两件事情：清空已有数据和从远端API填充数据。

```typescript
init = async () => {
  // 清空
  await this.panelTypes?.clear();
  await this.panelData?.clear();

  // 向模型服务请求变量面板
  try {
    const { modelServer } = window.constant;
    const panelInfoUrl = `http://*******`;

    // 请求数据并处理
    const rsp = await axios.get({ url: panelInfoUrl });
    // ...
  } catch (error) {
    console.error('处理面板缓存异常。', error);
  }
};
```

数据库初始化函数先调用`clear`方法清空表数据，然后使用axios请求远端API获取新的数据。这里假设`modelServer`是一个从`window.constant`获取的远端API地址，根据这个地址请求得到了一系列面板数据。

接下来，将这些数据添加到对应的Dexie表中：

```typescript
// 遍历响应数据填充到Dexie数据表
for (const [tagName, tagPanelVal] of Object.entries(rsp)) {
  const panelType: CommonPanel = { /* ... */ };
  await this.panelTypes!.add(panelType);

  const panelsIdList = Object.keys((tagPanelVal as any).panels);
  if (panelsIdList.length !== 0) {
    panelsIdList.forEach(async (panelId) => {
      const panelData: CommonPanelData = { /* ... */ };
      await this.panelData!.add(panelData);
    });
  }
}
```

循环遍历从API获得的数据。每个tagName和tagPanelVal代表不同的面板类型和面板数据，在数据库不同的表中分别存储面板类型和面板数据。

## 数据的读取

在Dexie中，可以很方便地通过表的查询API获取需要的数据：

```typescript
export const getPanelData = async (panelId: string) => {
  let db = new CommonPanelDB();
  const graphPanelData = await db.panelData?.where('panelId').equalsIgnoreCase(panelId).first();
  db.close();
  return JSON.parse(graphPanelData?.content ?? '');
};
```

这个`getPanelData`函数接受一个`panelId`参数，然后通过`where`方法结合`equalsIgnoreCase`函数完成对大小写不敏感的查询。`first`表示我们只需要第一条匹配的记录。

## 收尾工作

在数据库操作完成后，应关闭数据库连接并清理资源：

```typescript
close = () => {
  delete this.panelData;
  this.panelDB?.close();
  this.panelDB = null;
};
```

`CommonPanelDB`类的`close`方法卸载了Dexie表，并调用Dexie的`close`方法关闭数据库连接。调用`close`方法确保在对象不再使用时释放了数据库资源。

## 小结

本文通过一个实际案例展示了如何在Web应用中使用Dexie.js管理本地数据库。通过Dexie.js，开发者能够以更友好的API轻松实现数据的CRUD操作，不必直接面对底层的IndexedDB复杂的API。结合异步函数和Promise，可以构建出可读性强、结构清晰的数据存储逻辑。

在实战中，Dexie.js发挥了它在简化IndexedDB使用方面的优势，提升了开发效率并确保了代码的可读性。通过合理设计数据库表结构，并配合有效的初始化和收尾策略，可以保证数据的及时性和准确性，使应用具备更好的响应速度和用户体验。