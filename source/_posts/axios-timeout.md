---
title: axios-timeout
date: 2018-03-15 15:30:13
tags: middlewares
categories:
    - cate
---

## axios 使用拦截器全局处理请求重试

axios 提供了拦截器的接口能够全局处理请求和响应。Axios拦截器会在Promise的then和catch调用前拦截到。

- 请求拦截示例：

  ```js
  axios.interceptors.request.use(function (config) {
          // 发起请求前做一些业务处理
          return config
      }, function (error) {
          // 对请求失败做处理
          return Promise.reject(error);
      })
  ```

- 响应拦截示例：

  ```js
  axios.interceptors.response.use(function (response) {
          // 对响应数据做处理
          return response;
      }, function (error) {
          return Promise.reject(error);
      })
  ```

## Axios 实现请求重试

在某些情况（如请求超时），可能希望重新发起请求。可以再响应拦截器坐下处理，对请求发起重试。

### 请求重试需要考虑三个因素：

- 重试条件
- 重试次数
- 重试时延

### 配置

```js
axios.defaults.retry = 1; // 重试次数
axios.defaults.retryDelay = 1000; // 重试时延
axios.defaults.shouldretry = (error) => true; // 重试条件
```

### Demo

```js
axios.defaults.timeout = 5000; // 设置所有请求的超时时间
axios.defaults.retry = 1; // 重试次数
axios.defaults.retryDelay = 1000;// 重试延时
axios.defaults.shouldRetry = function (error) {
    if (error.code === 'ECONNABORTED' && error.message.indexOf('timeout') !== -1) {
        return true;
    }
}; // 重试条件，只要是超时错误都需要重试

axios.interceptors.response.use(undefined, function axiosRetryInterceptor(err) {
    var config = err.config;
    if (!config || !config.retry) { // 判断是否配置了重试
        return Promise.reject(err);
    }

    if (!config.shouldRetry || typeof config.shouldRetry !== 'function') {
        return Promise.reject(err);
    }

    // 判断是否满足重试条件
    if (!config.shouldRetry(err)) {
        return Promise.reject(err);
    }

    // 设置重置次数，默认为0
    config.__retryCount = config.__retryCount || 0;
    
    // 是否超过了重试次数
    if (config.__retryCount >= config.retry) {
        return Promise.reject(err);
    }

    config.__retryCount += 1;

    // 延时处理
    let backoff = new Promise(function (resolve) {
        setTimeout(function () {
            resolve();
        }, config.retryDelay || 1);
    });

    // 重新发起axios请求
    return backoff.then(function () {
        return axios(config);
    })

})
```