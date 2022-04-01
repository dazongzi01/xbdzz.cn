### Google登录



#### 开发者平台配置

1. 进入开发者平台，首先前往Google API 控制台选择或者创建一个项目 [谷歌开发者平台](https://link.juejin.cn/?target=https%3A%2F%2Fconsole.developers.google.com/)![image-20220401172801324](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011728360.png)
   ![image-20220401172828632](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011728660.png)

2. 申请一个OAuth 2.0 客户端 ID，点击创建凭据选择图中标注的OAuth客户端ID

![image-20220401172849310](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011728335.png)
选择web应用
![image-20220401172911543](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011729568.png)
按照要求填写你项目的类型、名称以及来源url
![image-20220401172937505](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011729531.png)
填写完保存应该就创建成功了,标注的哪个客户端ID就是我们在登陆需要的
![image-20220401172953969](https://cdn.jsdelivr.net/gh/xbdazz/mypic/img/202204011729998.png)


#### 按照上面的步骤成功申请到客户端ID以后，就可以在代码中测试了，下面是代码实例


```js
<template>
    <view>
        <button v-google-signin-button="ClientId">谷歌登录</button>
    </view>
</template>
```
---
```js
//用自定义指令方式实现
import GoogleSignInButton from '@/plugin/googleSign.js'
export default {
    data(){
        return {
            ClientId:'' //刚才申请的客户端ID
        }
    },
    directives: {
        GoogleSignInButton
    },
    methods:{
        OnGoogleAuthSuccess(idToken){
            cosnole.log(idToken);
            //成功打印出来idToken就可以传给后端解析了
        },
        OnGoogleAuthFail(error) {
            console.log(error)
        },
    }
}
```
