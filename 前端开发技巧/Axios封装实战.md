# Axios封装实战

## 引言

在现代Web应用开发中，客户端与服务器的通信是不可或缺的环节，往往通过HTTP请求来实现数据的传输与交互。Axios，作为一个流行的基于Promise的HTTP客户端库，提供丰富的接口供开发者进行网络请求。然而，在大型项目中直接使用Axios可能导致请求代码分散在各处，难以维护和统一处理公共逻辑。因此，对Axios进行封装以适应不同的业务需求和简化请求流程变得尤为重要。在本文中，我们将探讨如何封装Axios以实现一个结构清晰、易于维护的HTTP请求实例。

## 基础配置

Axios实例的创建始于基础配置的设定。`baseConfig` 对象中包含了实例的关键配置项：

```javascript
const { CancelToken } = axios;

// axios基础配置
const baseConfig = {
    API_BASE_URL: "",
    REQUEST_TIMEOUT: 5000,
    cancelTokenSource: CancelToken.source(),
};
```
`API_BASE_URL` 确定了所有请求的公共前缀，`REQUEST_TIMEOUT` 定义了请求的超时时间，而 `cancelTokenSource` 则用于取消请求。这些基础配置项的设置对于构建一个健壮的HTTP服务至关重要。

## 请求拦截器

请求拦截器 `requestInterceptor` 是对发出的HTTP请求进行预处理的先行官：

```javascript
// 请求拦截器
const requestInterceptor = (config) => {
    if (!navigator.onLine) {
        config.cancelToken = baseConfig.cancelTokenSource.token;
        baseConfig.cancelTokenSource.cancel('no network');
    }
    if (window.loginInfo) {
        const { token, access_token, token_type } = window.loginInfo;
        config.headers.Authorization = token || `${token_type} ${access_token}`;
    }
    config.metadata = { startTime: new Date().getTime() };
    return config;
};
```
拦截器检测当前浏览器网络状态，若无网络则直接取消请求。若用户已登录，则为请求添加认证header，便于后端验证用户身份。此外，请求开始的时间被记录以供后续性能分析之用。

## 响应拦截器

响应拦截器 `responseInterceptor` 主要用于处理服务器返回的响应数据：

```javascript
// 响应拦截器
const responseInterceptor = (response) => {
    return response.data;
};
```
该拦截器直接返回了响应中的 `data` ，省去了在数据处理环节中访问 `response.data` 的步骤，简化了后续的逻辑。

## 错误处理

请求失败处理 `requestHandleError` 和响应失败处理 `responseHandleError` 是遇到请求异常时的救生索：

```javascript
// 请求失败处理回调
const requestHandleError = (error) => {
    return Promise.reject(new Error(error.message, { cause: error }));
};

// 响应失败处理回调
const responseHandleError = (error) => {
	if (error.response) {
		const { status, data } = error.response;
		switch (status) {
			case 401:
				// call login again handle
				break;
			case 4010:
				Modal.clear();
				Modal.confirm({
					bodyClassName: "modal-confirm-body",
					content: 'please login first',
					cancelText: 'cancel',
					confirmText: 'login',
					onConfirm: () => void console.log('')
				})
				break;
			default:
				break;
		}
		return Promise.reject(new Error(data.msg || error.message, { cause: error }));
	} else if (error.code === "ECONNABORTED") {
		// 连接超时
		handleConnectError();
		return Promise.reject(new Error('', { cause: error }));
	}
	return Promise.reject(new Error('internael error', { cause: error }));
}
```
这两个函数负责捕捉请求失败和响应失败的异常情况，并生成包含详细错误信息的Promise对象，使得错误可以在调用链的任何一个步骤被捕获和处理。

## 超时和网络错误处理

超时或网络异常时触发 `handleConnectError` 函数，以向用户显示友好的错误信息：

```javascript
// 超时回调
const handleConnectError = () => {
	const customIcon = () => (
		<div style={{ textAlign: 'center' }}>
			<UndoOutline fontSize={24} />
			<p>{'loading failed'}</p>
		</div>
	);
	Toast.show({
		content: customIcon(),
		duration: 1500,
		afterClose: () => {console.log('close')}
	})
}
```

## 创建Axios实例

主函数 `createAxiosInstance` 将配置和拦截器绑定到新创建的Axios实例：

```javascript
// 主函数
const createAxiosInstance = (config) => {
	// 设置baseURL 请求头 超时时间等
	const instance = new axios.create({
		baseURL: baseConfig.API_BASE_URL,
		headers: {
			"Content-Type": "application/json"
		},
		timeout: baseConfig.REQUEST_TIMEOUT,
		...config
	})

	// 设置请求头
	instance.defaults.headers.pragma = "no-cache";
	instance.defaults.headers.get["Content-Type"] = "application/json";

	// 设置请求拦截器
	instance.interceptors.request.use(requestInterceptor, requestHandleError);

	// 设置响应拦截器
	instance.interceptors.response.use(responseInterceptor, responseHandleError);

	return instance;
}
```
通过集成前面提到的基础配置、拦截器和错误处理，实现了对Axios的全面封装。随着该实例的返回，开发者可以使用它进行后续的API请求，享受到统一处理请求和响应的便捷。

## 完整代码
```js
import React from "react";
import axios from "axios";
import { Modal, Toast } from "antd-mobile";
import { UndoOutline } from 'antd-mobile-icons'

const { CancelToken } = axios;

// axios基础配置
const baseConfig = {
	API_BASE_URL: "",
	REQUEST_TIMEOUT: 5000,
	cancelTokenSource: CancelToken.source(),
}

// 请求拦截器
const requestInterceptor = (config) => {
	if (!navigator.onLine) {
		config.cancelToken = baseConfig.cancelTokenSource.token;
		baseConfig.cancelTokenSource.cancel('no network');
	}
	if (window.loginInfo) {
		const { token, access_token, token_type } = window.loginInfo;
		config.headers.Authorization = token || `${token_type} ${access_token}`;
	}
	config.metadata = { startTime: new Date().getTime() };
	return config;
}

// 响应拦截器
const responseInterceptor = (response) => {
	return response.data;
}

// 请求失败处理回调
const requestHandleError = (error) => {
	return Promise.reject(new Error(error.message, { cause: error }));
}

// 响应失败处理回调
const responseHandleError = (error) => {
	if (error.response) {
		const { status, data } = error.response;
		switch (status) {
			case 401:
				// call login again handle
				break;
			case 4010:
				Modal.clear();
				Modal.confirm({
					bodyClassName: "modal-confirm-body",
					content: 'please login first',
					cancelText: 'cancel',
					confirmText: 'login',
					onConfirm: () => void console.log('')
				})
				break;
			default:
				break;
		}
		return Promise.reject(new Error(data.msg || error.message, { cause: error }));
	} else if (error.code === "ECONNABORTED") {
		// 连接超时
		handleConnectError();
		return Promise.reject(new Error('', { cause: error }));
	}
	return Promise.reject(new Error('internael error', { cause: error }));
}

// 超时回调
const handleConnectError = () => {
	const customIcon = () => (
		<div style={{ textAlign: 'center' }}>
			<UndoOutline fontSize={24} />
			<p>{'loading failed'}</p>
		</div>
	);
	Toast.show({
		content: customIcon(),
		duration: 1500,
		afterClose: () => {console.log('close')}
	})
}

// 主函数
const createAxiosInstance = (config) => {
	// 设置baseURL 请求头 超时时间等
	const instance = new axios.create({
		baseURL: baseConfig.API_BASE_URL,
		headers: {
			"Content-Type": "application/json"
		},
		timeout: baseConfig.REQUEST_TIMEOUT,
		...config
	})

	// 设置请求头
	instance.defaults.headers.pragma = "no-cache";
	instance.defaults.headers.get["Content-Type"] = "application/json";

	// 设置请求拦截器
	instance.interceptors.request.use(requestInterceptor, requestHandleError);

	// 设置响应拦截器
	instance.interceptors.response.use(responseInterceptor, responseHandleError);

	return instance;
}

export default createAxiosInstance;
```

## 结语

本文展示了如何将Axios库封装成一个易于维护和使用的HTTP请求工具。通过配置、拦截器和错误处理的合理使用，开发者可以构建出一个强大的，能够提升整个项目开发效率和用户体验的HTTP服务。

