---
date: 2019-11-19
tag:
  - frontend
  - javascript
  - 链式调用Api方法
author: Kimlon.zeng
location: Shenzhen
---

# 前端 API 链式调用封装

第一次写博客，难免会出不少问题。有不对地方，可以提 issue 给我。

## 首先封装 axios,设置调用的方法

得到封装方法`apiGet apiPost`，代码如下：

```javascript
// api/request.js
import axios from 'axios';
import qs from 'qs';

const axiosOptions = {
  baseURL: 'localhost'
};

const services = axios.create(axiosOptions);

const request = (url, options) => {
  services.defaults.headers.offset = new Date().getTimezoneOffset() / 60;
  return services(url, options)
    .then(data => data)
    .catch(err => ({
      statusCode: 500,
      err,
      message: err.toString()
    }));
};

export function apiGet(url, params) {
  if (params) {
    params.__t = Date.now();
  } else {
    params = {
      __t: Date.now()
    };
  }
  const queryString = qs.stringify(params, {
    arrayFormat: 'repeat',
    skipNulls: true,
    addQueryPrefix: true
  });
  return request(`${url}${queryString}`);
}

export function apiPost(url, params) {
  return request(url, {
    method: 'POST',
    data: params
  });
}
```

## 配置 api 调用对象

代码如下：

```javascript
// api/config.js
const Api = {
  base: {
    // 获取登录信息
    getLoginUser: {
      url: 'base/getLoginUser',
      method: 'get'
    },
    // 登录接口
    login: {
      url: 'base/login',
      method: 'post'
    }
  }
};

export default Api;
```

## 扩展 api 对象链式调用能力

代码如下：

```javascript
// api/index.js
import Api from './Api';
import { apiGet, apiPost } from './request';

// 请求方法列表
const methodsMap = {
  get: apiGet,
  post: apiPost
};

// 设置请求方法原型
const requestPrototype = { request };

// 设置请求方法
function SetRequest(obj) {
  Object.setPrototypeOf(obj, requestPrototype);
}

/**
 * data 默认值为空对象
 * success 和 error 方法都没传的话，就不执行对应方法, 如果执行方法成功后，有return的话，会把结果返回到promise中
 * @param {*} { data = {}, success, error }
 */
function request({ data = {}, success, error }) {
  const send = methodsMap[this.method];
  const dataKeys = Object.keys(data);
  const url = this.url;

  // 发送对应请求
  if (send) {
    return send(url, data).then(res => {
      const { statusCode, message } = res;
      if (statusCode === 200) {
        if (success && typeof success === 'function') {
          const result = success(res);
          return result || res;
        }
      } else if (statusCode !== 200) {
        if (error && typeof error === 'function') {
          const result = error(res);
          return result || res;
        } else {
          console.log('error', message);
        }
      }
      return res;
    });
  }
}

// 扩展Api对象请求能力
function extendApiRequest(api) {
  const keys = Object.keys(api);
  for (const key of keys) {
    // 判断存在url属性，则添加请求方法
    if (key === 'url') {
      SetRequest(api);
    }
    if (typeof api[key] === 'object') {
      extendApiRequest(api[key]);
    }
  }
}

// 扩展请求对象 链式调用能力
extendApiRequest(Api);

export default Api;
```

## 最后调用实践

代码如下：

```javascript
import Api from 'api/index.js';

// 获取登录信息
Api.base.getLoginUser.request({
  success: res => {
    // ...
  },
  error: res => {
    // ...
  }
});

// 请求登录
Api.base.login.request({
  success: res => {
    // ...
  },
  error: res => {
    // ...
  }
});
```

## 总结

最后总结一下，时间有限，这个封装还可以扩展添加动态参数，字段加密等操作。如果发现有更好的方法，也请大家提出。感谢您的阅读
