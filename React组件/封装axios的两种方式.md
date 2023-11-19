作为前端工程师，经常需要对axios进行封装以满足复用的目的。在不同的前端项目中使用相同的axios封装有利于保持一致性，有利于数据之间的传递和处理。本文提供两种对axios进行封装的思路。
## 1. 将请求方式作为调用参数传递进来
1. 首先导入了`axios, AxiosInstance和AxiosResponse模块`，用于创建一个http请求的实例和处理响应结果。
2. 定义了一个`getBaseUrl函数`，用于获取请求的基础URL。
3. 创建了`httpProto实例`，使用`axios.create方法`进行创建。并配置了请求的超时时间为5000ms，不携带凭证，设置请求的`Content-Type为application/json;charset=UTF-8`，并允许跨域访问。
4. 添加了一个**请求拦截器**，通过`httpProto.interceptors.request.use`方法，对请求进行处理。首先使用`getBaseUrl`函数获取基础URL，并将其添加到请求的`baseURL`属性中。然后通过`getToken`函数获取凭证，如果凭证存在，则将其添加到请求的`Authorization`头部字段中。最后返回处理后的请求配置。
5. 添加了一个**响应拦截器**，通过`httpProto.interceptors.response.use`方法，对响应进行处理。首先获取响应的data字段，然后判断`data.result`的值，如果为0则表示请求成功，直接返回data。否则将返回一个失败的Promise，reject的值为data。
6. 定义了一个`http`函数，用于发送请求。这个函数接收一个`method参数和其他参数(rest)`，然后通过`httpProto[method](...rest)`的形式调用`httpProto`实例的对应方法发送请求。
7. 定义了一个`urls`对象，用于存储可供使用的URL路径，其中有一个示例路径example。
8. 定义了一个`methods`对象，用于存储常用的请求方法名称，包括`get、post和delete`。

完整的代码如下所示：
```ts
import axios, { AxiosInstance, AxiosResponse } from "axios";
import { getToken } from "./token";

// 获取请求的基础URL
const getBaseUrl = () => `http://${window.constant.serverIp}:8888}`;

// 创建http请求的实例对象
const httpProto: AxiosInstance = axios.create({
  timeout: 5000,
  withCredentials: false,
  headers: {
    'Content-Type': 'application/json;charset=UTF-8',
    'Access-Control-Allow-Origin': '*',
  }
});

// 添加请求拦截器
httpProto.interceptors.request.use((config: any) => {
  // 配置baseURL
  config.baseURL = getBaseUrl();
  // 获取凭证
  const token = getToken();
  if (token) {
    // 如果有凭证就加上此凭证
    config.headers.Authorization = `${token}`;
  }
  return config;
}, (error) => {
  return Promise.reject(error)
});

// 添加响应拦截器
httpProto.interceptors.response.use(
  (response: AxiosResponse) => {
    const { data } = response
    // 统一处理响应结果
    if (data.result === 0) {
      return data;
    } else {
      return Promise.reject(data);
    }
  },
  (error) => {
    // 统一处理错误信息
    return Promise.reject(error);
  }
);

// 将httpProto实例，也就是AxiosInstance实例对象封装起来
const http = (method: string, ...rest: any) => {
  return httpProto[method](...rest);
}

// 可供使用的urls
const urls = {
  example: `/prod/example`,
}

const methods = {
  get: 'get',
  post: 'post',
  delete: 'delete',
}

export { http, urls, methods };
```
## 2. 直接调用某请求方式对应的请求方法

1. 导入了`axios`模块的相关类型和函数。
2. 定义了`printLog`函数，用于处理**日志输出**。
3. 定义了`IResponse`接口，表示请求响应对象的格式。
4. 定义了`RequestParams`接口，表示发送请求的配置项的格式。
5. 定义了`IHttp`接口，表示封装对象支持的请求方式/方法。
6. 定义了`req`对象，用于向外暴露支持的请求方法。
7. 定义了`methods`数组，表示支持的请求方式类型。
8. **使用forEach方法遍历methods数组，逐步构造req对象上的各个方法**。
9. 在每个方法的构造过程中，进行以下步骤：
   - `参数合并`，将默认的responseType设置为'json'。
   - 从params对象中`解构`需要的参数。
   - 使用`axios.create`方法创建一个`AxiosInstance`实例对象。
   - 创建`请求头对象`，并设置一些常用的请求头信息。
   - 构造请求配置对象`AxiosRequestConfig`。
   - 根据请求方式对请求配置进行**修正**，主要是将data赋值到相应的字段中。
   - **添加请求拦截器**，并在成功和失败的情况下返回配置。
   - **添加响应拦截器**，并在成功和失败的情况下返回处理结果。
   - 构造请求成功的回调函数，对返回数据进行格式化的操作。
   - 构造请求失败的回调函数，处理错误日志和断网情况。
   - 发送请求并将请求结果作为函数的返回值。
10. 默认导出req对象。

以下是加上注释的完整代码：

```typescript
import axios, { AxiosRequestConfig, AxiosResponse, AxiosError } from 'axios';

// 日志处理，可定制
const printLog = console;

// 作为被Promise包裹的请求响应对象的格式
export interface IResponse {
  code: number;
  msg: string;
  result: {
    lastOperaTime: number;
    data: any;
  };
}

// 发送请求的配置项的格式
export interface RequestParams {
  url: string;
  baseUrl?: string;
  data?: object;
  filter?: boolean;
  responseType?: 'arraybuffer' | 'blob' | 'document' | 'json' | 'text' | 'stream';
  headers?: any;
  timeout?: number;
}

// 封装对象支持的请求方式/方法
interface IHttp {
  get?: (params: RequestParams) => Promise<any>;
  post?: (params: RequestParams) => Promise<any>;
  put?: (params: RequestParams) => Promise<any>;
  patch?: (params: RequestParams) => Promise<any>;
  delete?: (params: RequestParams) => Promise<any>;
}

// 支持的请求方式类型
export type HttpMethod = 'get' | 'post' | 'put' | 'patch' | 'delete';

// 向外暴露出去的对象
const req: IHttp = {};

// 支持的请求类型
const methods: HttpMethod[] = ['get', 'post', 'put', 'patch', 'delete'];

// 遍历methods数组，逐步构造req对象
methods.forEach((_m: HttpMethod) => {
  // 使用遍历的方式对req对象上的各个方法进行构造
  req[_m] = (params: RequestParams) => {
    // 1. 构造参数合并
    params = {
      ...params,
      responseType: params.responseType || 'json',
    };

    // 2. 从使用对象方法的形参上结构出必要的参数
    const {
      url, // 服务器地址
      data, // 有效载荷
      filter = true, // 过滤器
      responseType, // 返回类型
      timeout, // 超时时间
    } = params;

    // 3. 使用axios创建AxiosInstance实例对象
    const instance = axios.create({
      baseURL: params.baseUrl ?? `http://${window.location.hostname}`,
      timeout,
    });

    // 4. 创建请求头对象
    const headers = {
      lastOperaTime: Date.now(), // 时间戳
      token: getToken(), // 凭证
      lang: getLocalLocale(), // 语言
      Accept: 'application/json', // 接受返回数据的类型
      'Content-Type': 'application/json; charset=utf-8', // 内容格式
    };

    // 5. 请求配置
    const axiosConfig: AxiosRequestConfig = {
      method: _m, // 请求方法
      url, // 服务器地址
      headers: {
        // 合并请求头
        ...headers,
        ...(params.headers || {}),
      },
      responseType, // 返回值类型
    };

    // 6. 针对不同的请求类型需要对请求配置进行修正
    if (data) {
      // 对于有效载荷，不同的请求方式携带信息的方式是不同的，在这里做了区分
      if (_m === 'get') {
        axiosConfig.params = data;
      } else if (data instanceof FormData) {
        axiosConfig.data = data;
      } else {
        axiosConfig.data = data;
      }
    }

    // 添加请求拦截器
    instance.interceptors.request.use(
      // 占位
      (config: any) => {
        return config;
      },
      // 失败则返回失败
      (error: any) => {
        return Promise.reject(error);
      }
    );

    // 7. 添加响应拦截器
    instance.interceptors.response.use(
      // 成功的回调，将发起请求的参数作为第二参数回传
      (res: any) => handleSuccess(res, params),
      // 失败的回调，将发起请求的参数作为第二参数回传
      (err: any) => handleError(err, params)
    );

    // 8. 构造请求成功的回调函数 -- 主要是对返回数据进行格式化的操作
    function handleSuccess(response: AxiosResponse<IResponse>, requestParams: RequestParams) {
      if (response.data) {
        // 解构数据
        const { code, msg, result } = response.data;
        if (code !== 0) {
          printLog.error(msg);
        }

        return filter ? result?.data ?? result : response.data;
      } else {
        printLog.error('incorrect data format');
        return response.data;
      }
    }

    // 9. 构造请求失败的回调函数
    function handleError(err: AxiosError, requestParams: RequestParams) {
      if (err.response) {
        printLog.error(`api: ${requestParams.url}: ${err.response.status}`);
      }
      if (err instanceof Error) {
        if (err.message) {
          printLog.error(err.message);
        }
      }
      if (!window.navigator.onLine) {
        // 处理断网情况
        printLog.error('netwrok error');
      }
      return Promise.reject(err);
    }

    // 10. 发送请求并将请求结果（Promise对象）作为函数的返回值
    return instance.request(axiosConfig);
  };
});

export default req;
```