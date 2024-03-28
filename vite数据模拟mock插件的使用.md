#### 前言

我们在从事前端开发的时候，经常遇到后端小伙伴还没开发好接口的情况。于是今天特意学习了mock模拟数据，可以用于前端的接口测试。

一般来说，前端mock数据最常见的方法就是使用**mockjs**，或者使用**在线的mock工具**，但今天我接触到的**vite-plugin-mock插件**，个人认为更加方便。

#### 安装

```
npm install mockjs vite-plugin-mock -D
```

#### 使用

1. 配置 **vite.config.ts**

```
//引入viteMockServe
import { viteMockServe } from 'vite-plugin-mock'
```

```
//配置viteMockServe
export default defineConfig(({ command }) => {
  return {
    plugins: [vue(),
    //和vue插件位于同一层级
    viteMockServe({
    //此处官方为localEnabled:command,但貌似已更改为下列的方式，采用enable
      enable: command === 'serve',
    })],

  }
})
```

完整代码：

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { viteMockServe } from 'vite-plugin-mock'


// https://vitejs.dev/config/
export default defineConfig(({ command }) => {
  return {

    plugins: [vue(),
    viteMockServe({
      enable: command === 'serve',
    })],

  }
})

```

2. 配置好之后，我们在项目根路径下**新建 mock 文件夹**，再在文件夹中**新建index.ts文件**。这里以我的index.ts为例：

```
//定义用户信息
function createUserList() {
    return [
        {
            userId: 1,
            avatar: '',
            username: 'admin',
            password: '111111',
            desc: '平台管理员',
            roles: ['平台管理员'],
            buttons: ['cuser.detail'],
            routes: ['home'],
            token: 'Admin token'
        },
        {
            userId: 2,
            avatar: '',
            username: 'system',
            password: '111111',
            desc: '系统管理员',
            roles: ['系统管理员'],
            buttons: ['cuser.detail', 'cuser.user'],
            routes: ['home'],
            token: 'Admin token'
        }
    ]
}


export default [
    {
        url: '/api/user/login',
        method: 'post',
        response: ({ body }) => {
            const { username, password } = body
            const checkUser = createUserList().find(item => item.username === username && item.password === password)
            if (!checkUser) {
                return {
                    code: 201,
                    message: '用户名或密码错误'
                }
            }
            const { token } = checkUser
            return {
                code: 200,
                message: '登录成功',
                data: {
                    token
                }
            }
        }
    },
    {
        url: '/api/user/info',
        method: 'get',
        response: (request) => {
            const token = request.headers.token
            const checkUser = createUserList().find(item => item.token === token)
            if (!checkUser) {
                return {
                    code: 201,
                    message: '用户不存在'
                }
            }
            return {
                code: 200,
                message: '获取用户信息成功',
                data: {
                    checkUser
                }
            }
        }
    }
]
```

​        此处我模拟了一个createUserList列表，同时定义并暴露出两个接口。无需任何配置，只要**请求的路径跟暴露出的路径相匹配**，就可以自动模拟后端返回的数据。