# 使用React Query代替useEffect获取数据的优势与对比

在构建现代React应用时，我们经常需要从后端API获取数据来渲染界面。传统的方式是使用React的`useEffect`钩子结合`fetch`或`axios`等HTTP请求库来完成数据获取和状态管理。然而，随着React Query的出现，获取和同步服务器状态的方式得到了显著的改进。本文将详细介绍使用React Query代替`useEffect`获取数据的原因，并通过示例对比两种方式在代码层面的不同，在最后总结React Query的优势。

## 传统方式：使用useEffect获取数据

在没有使用React Query之前，我们通常会这样获取数据：

```jsx
import React, { useState, useEffect } from 'react';

function MyComponent() {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      setIsLoading(true);
      setError(null);
      try {
        const response = await fetch('https://my-api/data');
        const result = await response.json();
        setData(result);
      } catch (error) {
        setError(error);
      }
      setIsLoading(false);
    };

    fetchData();
  }, []);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>{JSON.stringify(data)}</div>;
}
```

这段代码虽然能工作，但存在几个问题：缺乏缓存策略、复杂的错误处理、不自动的数据更新、重复的数据请求等。

## 使用React Query改进数据获取

接下来，看看React Query如何为我们解决上述问题和简化代码：

```jsx
import React from 'react';
import { useQuery } from 'react-query';

async function fetchData() {
  const response = await fetch('https://my-api/data');
  return response.json();
}

function MyComponent() {
  const { data, isLoading, error } = useQuery('data', fetchData);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>{JSON.stringify(data)}</div>;
}
```

在这个改进后的版本中，我们用`useQuery`钩子来代理数据加载。这行代码做了很多工作：它自动进行数据请求，处理加载状态和错误状态，还负责缓存和更新数据。

## 使用React Query的原因

### 简化的状态管理

React Query内部处理了数据的加载(`isLoading`)、数据更新(`isFetching`)、错误(`error`)状态管理，这使得开发者无需手动设置这些状态。

### 自动化的数据缓存和无效化

React Query还提供了出色的数据缓存策略。默认情况下，当组件卸载再重新挂载时，React Query会使用旧的缓存数据，同时在后台静默地为你刷新数据，保证数据的新鲜度。

### 更好的错误和重试处理

通过对错误状态的内部管理，React Query提供了错误捕获的机制并允许自动重试功能。这比手动实现要简单得多。

### 优化请求节省带宽

React Query会自动去重和合并并发的查询请求，减少不必要的网络请求，节省宽带。

## React Query的优势

总结来说，React Query的主要优势包括：

- 自动化：管理请求生命周期（查询、缓存、更新、重试）无需手动编写代码。
- 减少样板代码：少写很多状态处理的逻辑，让代码简洁易维护。
- 性能提升：智能缓存和数据更新策略，更少的重新渲染。
- 鲁棒性：更健壮的错误处理和重试逻辑。
- 开箱即用：丰富的功能如后台获取、分页、无限加载等。

在创建现代化的React应用程序时，React Query提供了一种更智能、更高效和简单的方法来处理数据获取和同步，这也是越来越多的React开发者选择它的原因。

# React Query
下面将详细介绍React Query的功能，以及它如何在一个实际的场景中被使用。我们将构建一个用户列表的应用，这个应用将展示用户数据、支持数据刷新、加载更多用户以及处理错误重试。

## 项目准备

首先，确保已经在React项目中安装了React Query：

```bash
npm install react-query
```

或者

```bash
yarn add react-query
```

## 功能概览

- **数据获取** (`useQuery`): 用于获取数据并提供状态管理，比如loading, error, data。
- **缓存与背景更新** (`staleTime` 和 `cacheTime`): 确定数据保持新鲜的时间，以及未被使用时保持在缓存中的时间。
- **自动重试** (`retry`): 当请求失败时，自动进行重试。
- **分页和加载更多** (页码或游标): 当我们需要分页或者无限加载数据时使用。
- **数据预加载** (`queryClient.prefetchQuery`): 加载关键数据以提升用户体验。
- **数据变异** (`useMutation`): 提交数据至服务器，并更新本地缓存。

## 示例应用

### 获取用户列表

我们使用`useQuery`钩子来获取用户数据。这个钩子会自动发起请求并监听数据状态。

```jsx
import { useQuery } from 'react-query';

const fetchUsers = async (page = 0) => {
  const response = await fetch(`https://my-api/users?page=${page}`);
  if (!response.ok) {
    throw new Error('Network response was not ok');
  }
  return response.json();
};

function Users() {
  const { data, error, isLoading, isFetching } = useQuery('users', fetchUsers);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return (
    <>
      {data.users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
      {isFetching ? <span>Updating...</span> : null}
    </>
  );
}
```

### 自动刷新和背景更新

React Query可以配置数据自动刷新的时间，我们可以设置`staleTime`来避免不必要的后台更新，同时让我们的数据保持最新。

```jsx
const { data } = useQuery('users', fetchUsers, {
  staleTime: 5 * 60 * 1000 // 每5分钟更新一次数据
});
```

### 自动重试

如果请求失败，React Query可以自动尝试重新获取数据：

```jsx
const { data } = useQuery('users', fetchUsers, {
  retry: 2 // 请求失败会尝试2次重试
});
```

### 分页和加载更多

对于需要加载更多数据的情况，我们可以使用React Query的页码或游标方法来实现：

```jsx
const { data, fetchNextPage, hasNextPage } = useInfiniteQuery(
  'users',
  ({ pageParam = 0 }) => fetchUsers(pageParam),
  {
    getNextPageParam: (lastPage, allPages) => lastPage.nextPage,
  }
);

// ...

<button onClick={() => fetchNextPage()} disabled={!hasNextPage}>
  Load More
</button>
```

加载更多的按钮会根据`hasNextPage`来判断是否还有更多数据可以加载。

### 数据预加载

我们可以在用户的鼠标悬浮到某个按钮上时提前获取数据：

```jsx
const queryClient = useQueryClient();

// ...

<button
  onMouseEnter={() => queryClient.prefetchQuery('more-users-data', fetchAdditionalUsers)}
>
  Show More Users
</button>
```

### 数据变异

当需要提交数据到服务端时，我们可以使用`useMutation`来处理：

```jsx
import { useMutation, useQueryClient } from 'react-query';

const addUser = async (newUser) => {
  const response = await fetch(`https://my-api/users`, {
    method: 'POST',
    body: JSON.stringify(newUser)
  });
  if (!response.ok) {
    throw new Error('Could not add user');
  }
  return response.json();
};

function AddUser() {
  const queryClient = useQueryClient();
  const mutation = useMutation(addUser, {
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries('users');
    }
  });
  
  return (
    <button
      onClick={() => {
        const newUser = { name: 'New User' };
        mutation.mutate(newUser);
      }}
    >
      Add User
    </button>
  );
}
```

当我们向服务端增加一个新用户时，使用`useMutation`并提供一个成功的回调，该回调通过调用`queryClient.invalidateQueries`来标记用户列表的缓存为无效，以便它可以自动重新获取最新的用户列表。

## 总结React Query的优势

通过上述示例，我们可以看到React Query提供了强大且灵活的功能来处理数据的获取、缓存、更新、预加载、变异等操作。它大大简化了数据同步和状态管理的复杂性，使开发者可以专注于构建交互式的用户界面，而不必担心数据操作的底层细节。此外，React Query的自动重试和智能缓存策略可以提高应用的健壮性和用户的体验。

最后，简要地复习一下React Query的优势:

1. 内置缓存功能：React Query 为获取的数据提供缓存机制，这意味着当组件重新渲染或者同用户交互时，相同的数据正在加载，不需要再次发起网络请求，可以直接从缓存中获取数据。这减少了不必要的网络请求，提高了应用的效率。

2. 错误处理和错误重试：在处理异常数据时，错误处理和错误和错误重试在其他较繁琐。React Query 提供了强化的方式来处理这些状态，简化了开发者的工作。

3. 优化数据获取：React Query 会自动合并重复的查询请求，并将它们批量处理。这意味着如果多个组件请求相同的数据，React Query 只会发送一次网络请求，并且将数据分发给所有请求的组件。

4. 简洁高效和提高内存性能：通过减少不必要的网络请求和优化数据处理，React Query 可以帮助节省带宽并提高应用的响应性能。

5. 数据同步：在复杂的应用中，保持组件间数据的同步是一个挑战。React Query 通过其高层机制，帮助保持不同组件间数据的一致性。
