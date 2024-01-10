React Query 是一个强大的数据同步库，使得在 React 应用中处理异步请求、缓存和更新状态变得简单高效。本文将介绍如何在 React 应用中使用 React Query 的两个主要功能：`useQuery` 和 `useMutation`。

## 1. 如何使用 useQuery 进行可变参数请求

在 React 应用中，我们经常需要根据用户输入或其他状态变量来发起请求。通过 `useQuery`，我们可以方便地实现这种可变参数的请求。

```jsx
import { useQuery } from 'react-query';
import axios from 'axios';

const fetchProjects = async (searchTerm) => {
  const { data } = await axios.get('/api/projects', { params: { searchTerm } });
  return data;
};

function Projects({ searchTerm }) {
  const { data, isLoading, error } = useQuery(['projects', searchTerm], () => fetchProjects(searchTerm), { 
    enabled: !!searchTerm // 只有 searchTerm 存在时才发起请求
  });

  if (isLoading) return '加载中...';
  if (error) return `发生错误: ${error.message}`;

  return (
    <ul>
      {data.map(project => (
        <li key={project.id}>{project.name}</li>
      ))}
    </ul>
  );
}
```

在上面的代码中，我们使用 `searchTerm` 作为第二个参数传递给 `useQuery` 的 `queryKey`。React Query 会自动监测 `searchTerm` 的变化，当它改变时，重新执行 `fetchProjects` 函数发起新的请求。

## 2. useMutation 介绍和用法

`useMutation` 是 React Query 中用于执行造成数据变化的异步逻辑（如增加、删除、更新数据等）的 Hook。它可以帮助你管理异步逻辑的状态，并提供了取消、失败重试等功能。

```jsx
import { useMutation } from 'react-query';
import axios from 'axios';

const addProject = async (newProject) => {
  const { data } = await axios.post('/api/projects', newProject);
  return data;
};

function AddProjectForm() {
  const { mutate, isLoading, error } = useMutation(addProject, {
    onSuccess: (data) => {
      console.log('项目添加成功', data);
      // ... 可能需要更新 UI 或缓存
    }
  });

  const handleSubmit = (event) => {
    event.preventDefault();
    const newProject = { name: '新项目' };
    mutate(newProject);
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit" disabled={isLoading}>
        {isLoading ? '提交中...' : '添加项目'}
      </button>
      {error && <p>发生错误: {error.message}</p >}
    </form>
  );
}
```

在这个示例中，我们创建了一个表单来添加新项目。当表单提交时，我们使用 `mutate` 来调用 `addProject` 函数执行异步请求。`useMutation` 提供的 `onSuccess` 和 `onError` 回调让我们可以在请求成功或失败时执行相应的逻辑。

## 3. 请求参数变化时重新发起请求

当使用 `useQuery` 进行请求时，如果你想要在请求参数发生变化后重新发起请求，可以将参数作为 `queryKey` 的一部分，并确保当参数更新时组件能够重新渲染。

```jsx
const { data } = useQuery(['project', projectId], () => fetchProject(projectId), {
  enabled: projectId !== null
});
```

这里的 `projectId` 是一个动态的参数，当 `projectId` 更新时，React Query 将自动重新加载数据。

## 4. 使用 react-query 完成指定次数的重试和设置超时取消自动更新

React Query 允许你设置失败重试的次数，以及请求超时时间。

```jsx
const { data } = useQuery('projects', fetchProjects, {
  retry: 3, // 指定重试次数
  retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
  staleTime: 5000, // 数据保持新鲜的时间
  cacheTime: 10000, // 缓存数据保留时间
  refetchInterval: false, // 设置为 false 取消自动重新获取
  refetchOnWindowFocus: false // 取消窗口聚焦时的自动重新获取
});
```

在这个配置中，我们设置了最多重试三次，重试间隔为指数增长策略，数据在 5 秒内视为最新（`staleTime`），同时保持缓存时间为 10 秒，而 `refetchInterval` 和 `refetchOnWindowFocus` 配置为 `false` 可以阻止自动的更新行为。

至此，你已经了解了如何在 React 应用中使用 React Query 的基本功能。这些功能有效地简化了数据获取和数据突变的过程，并且提供了强大的缓存和更新机制。在实际项目中，合理地使用 React Query 可以在很大程度上提高开发效率和用户体验。

---

# English version
## Practical Guide to React Query

React Query is a powerful data synchronization library that simplifies the process of handling asynchronous requests, caching, and updating state in React applications. This article will introduce how to use two main features of React Query in React applications: `useQuery` and `useMutation`.

## 1. How to Use useQuery for Variable Parameter Requests

In React applications, we often need to initiate requests based on user input or other state variables. Through `useQuery`, we can easily implement requests with variable parameters.

```jsx
import { useQuery } from 'react-query';
import axios from 'axios';

const fetchProjects = async (searchTerm) => {
  const { data } = await axios.get('/api/projects', { params: { searchTerm } });
  return data;
};

function Projects({ searchTerm }) {
  const { data, isLoading, error } = useQuery(['projects', searchTerm], () => fetchProjects(searchTerm), { 
    enabled: !!searchTerm // Only initiate the request if searchTerm exists
  });

  if (isLoading) return 'Loading...';
  if (error) return `An error occurred: ${error.message}`;

  return (
    <ul>
      {data.map(project => (
        <li key={project.id}>{project.name}</li>
      ))}
    </ul>
  );
}
```

In the code above, we use `searchTerm` as the second argument to pass to `useQuery`'s `queryKey`. React Query automatically monitors changes to `searchTerm`, and when it changes, it re-executes the `fetchProjects` function to initiate a new request.

## 2. Introduction and Usage of useMutation

`useMutation` is a hook in React Query used for executing asynchronous logic that causes data changes (such as adding, deleting, updating data, etc.). It helps you manage the state of asynchronous logic and offers features like cancellation and failure retry.

```jsx
import { useMutation } from 'react-query';
import axios from 'axios';

const addProject = async (newProject) => {
  const { data } = await axios.post('/api/projects', newProject);
  return data;
};

function AddProjectForm() {
  const { mutate, isLoading, error } = useMutation(addProject, {
    onSuccess: (data) => {
      console.log('Project added successfully', data);
      // ... might need to update UI or cache
    }
  });

  const handleSubmit = (event) => {
    event.preventDefault();
    const newProject = { name: 'New Project' };
    mutate(newProject);
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Submitting...' : 'Add Project'}
      </button>
      {error && <p>An error occurred: {error.message}</p >}
    </form>
  );
}
```

In this example, we created a form to add new projects. When the form is submitted, we use `mutate` to call the `addProject` function to execute the asynchronous request. The `onSuccess` and `onError` callbacks provided by `useMutation` allow us to execute the appropriate logic when the request is successful or fails.

## 3. Re-initiating Requests When Request Parameters Change

When using `useQuery` to make requests, if you want to re-initiate the request after the request parameters change, include the parameters as part of the `queryKey` and ensure that the component can re-render when the parameters update.

```jsx
const { data } = useQuery(['project', projectId], () => fetchProject(projectId), {
  enabled: projectId !== null
});
```

Here, `projectId` is a dynamic parameter. When `projectId` updates, React Query will automatically reload the data.

## 4. Using react-query to Perform Specified Number of Retries and Set Timeout to Cancel Automatic Updates

React Query allows you to set the number of retries for failures, as well as the request timeout.

```jsx
const { data } = useQuery('projects', fetchProjects, {
  retry: 3, // Specify the number of retries
  retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
  staleTime: 5000, // Time for data to stay fresh
  cacheTime: 10000, // Data cache retention time
  refetchInterval: false, // Set to false to cancel automatic refetch
  refetchOnWindowFocus: false // Cancel automatic refetch when window is focused
});
```

In this configuration, we set a maximum of three retries, with retry intervals following an exponential growth strategy, data considered fresh within 5 seconds (`staleTime`), while maintaining cache time for 10 seconds. Setting `refetchInterval` and `refetchOnWindowFocus` to `false` prevents automatic update behavior.

With this, you now understand how to use the basic features of React Query in React applications. These features effectively simplify the processes of data fetching and data mutation, and provide powerful caching and updating mechanisms. Using React Query appropriately in real projects can significantly improve development efficiency and user experience.