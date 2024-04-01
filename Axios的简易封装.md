### Axios的简易封装

#### 前言

在开发过程中，我们总是需要对axios进行简易封装，简易封装的目的是为了使用其请求和响应拦截器，本文就对axios进行简易封装。



#### 步骤

1. 首先，**安装axios**,这里我用的是**npm**

```
npm install axios
```

2. 在项目 src 文件夹下，新建工具类文件夹 utils ，并在 utils 中创建文件 request.ts

![image-20240401112823585](C:\Users\Juliant\AppData\Roaming\Typora\typora-user-images\image-20240401112823585.png)

3. 在 request.ts 中，引入axios

```
import axios from 'axios'
```

4. 调用 **axios.create** 方法，创建一个配置对象，用于设置路由基本URL**(baseURL)**和超时时间**(timeout)**

```
let request = axios.create({
    //基础路径
    baseURL: import.meta.env.VITE_APP_BASE_API,
    timeout: 5000, //超时时间
})
```

5. 配置请求拦截器

```
request.interceptors.request.use(
    (config) => {
        // 请求拦截器
        return config;
    });
```

6. 配置响应拦截器

```
request.interceptors.response.use(
    (response) => {
        // 响应拦截器
        return response.data;
    }, (error) => {
        //失败回调
        //定义一个变量
        let message = '';
        let status = error.response.status;
        switch (status) {
            case 401:
                message = 'token失效，请重新登录';
                break;
            case 403:
                message = '没有权限，请联系管理员';
                break;
            case 404:
                message = '请求资源不存在';
                break;
            case 500:
                message = '服务器内部错误';
                break;
            default:
                message = '网络异常，请稍后重试';
                break;
        }
        //此处我引入ElMessage来显示失败请求，如果使用，需要在代码中引入。
        ElMessage({
            type: 'error',
            message: message,
        })

        return Promise.reject(error);
    });
```

7. 最后不要忘了暴露出去。

```
export default request;
```



