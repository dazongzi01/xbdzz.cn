### Facebook第三方登录

### 注意事项

<font color=#FF0000 >Facebook直接在国内无法访问需要科学上网搭梯子。</font>

#### 前期准备

- [申请Facebook账号](https://www.facebook.com/ "申请Facebook账号")

![markdown](http://pic.xbdzz.cn/write/202203261652696.png "申请Facebook账号")

- [登录Facebook开发者创建应用](https://developers.facebook.com/ "登录Facebook开发者创建应用")

![markdown](http://pic.xbdzz.cn/write/202203261700045.png "登录Facebook开发者创建应用")

![markdown](http://pic.xbdzz.cn/write/202203261654037.png "登录Facebook开发者创建应用")

![markdown](http://pic.xbdzz.cn/write/202203261655521.png "登录Facebook开发者创建应用")

- 获取Facebook应用编号，配置授权域名应用图标

![markdown](http://pic.xbdzz.cn/write/202203261655397.png "获取Facebook应用编号，配置授权域名应用图标")

![markdown](http://pic.xbdzz.cn/write/202203261656847.png "获取Facebook应用编号，配置授权域名应用图标") 


#### 使用方法
在login页面 <code style="background:#1B1F230D;color:#C7254E">script</code> 中引入JS
```
import facebookWebLogin from '@/plugin/facebookWebLogin.js';
```
#### API
- 初始化Facebook授权登录相关配置信息

```
facebookWebLogin.init(appId,(status)=>{})
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| appId  | string | <center>[Facebook开发者中心](https://developers.facebook.com) 我的应用 应用编号</center> | 
| status  | function | <center>status 为 true 表示Facebook授权登录环境配置成功<br>status 为 false 表示Facebook授权登录环境配置失败</center> | 

- 检查用户登录状态

```
facebookWebLogin.getLoginStatus((response)=>{})
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| response | function | <center>response.status 为 connected 表示用户已登录 Facebook 和您的网页<br>response.status 为 not_authorized 用户已登录 Facebook，但未登录您的网页<br>response.status 为 unknown 表示用户未登录 Facebook，所以无法知道他们是否登录了您的网页。或者之前已调用 FB.logout()，因此无法连接至 Facebook</center>  | 

- 通过 Javascript SDK 调用对话框登录

```
facebookWebLogin.login((response)=>{},Permission)
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| response | function | <center>如果 response.status 为 connected 响应中会包含 authResponse 参数</center>  | 
| Permission | object | <center>此参数为登录权限详细信息请前往官方文档查看 [Parameters](https://developers.facebook.com/docs/reference/javascript/FB.login/v11.0)</center>  |

- 通过 Javascript SDK 退出登录

```
facebookWebLogin.logout((response)=>{})
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| response | function | <center>方法回调</center>  | 

- 通过 图谱 API 彻底取消授权应用或撤销登录

```
facebookWebLogin.revocationAuthorization((response)=>{})
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| response | function | <center>方法回调</center>  | 

- 通过 图谱 API 查询用户信息

```
facebookWebLogin.api(fields,(response)=>{})
```
| 变量名 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| fields | string | <center>此参数为查询已授权相关权限用户信息详细信息请前往官方文档查看 [fields](https://developers.facebook.com/docs/permissions/reference)</center>  | 
| response | function | <center>方法回调</center>  | 


### 代码实例


```js
import facebookWebLogin from '@/plugin/facebookWebLogin.js';
export default {
    onLoad(e) {
         const FACEBOOK_APPID = 'xxxxxxxxxxxxx';
         facebookWebLogin.init(FACEBOOK_APPID, status => {
            if (status) {
                console.log('Facebook授权登录环境配置成功');
            }
         });
    },
    methods:{
        fbLogin() {
                let that = this;
                facebookWebLogin.login(
                    response => {
                        facebookWebLogin.getLoginStatus(response => {
                            if (response['status'] == 'connected') {
                                uni.showLoading({
                                    title: '正在登录中'
                                });
                                facebookWebLogin.api('permissions', res => {
                                    let fields = 'id,name,picture,';
                                    fields += res.permissions.data
                                        .filter(item => {
                                            return item['status'] == 'granted' && item[
                                                'permission'] != 'public_profile';
                                        })
                                        .map(item => {
                                            return item['permission'];
                                        })
                                        .join(',');
                                    facebookWebLogin.api(fields, facebookInfo => {
                                        let dataInfo = JSON.parse(JSON.stringify(facebookInfo));
                                        let fbData = {
                                            email:dataInfo.email,
                                            id:dataInfo.id,
                                            name:dataInfo.name,
                                            picture:`https://graph.facebook.com/${dataInfo.id}/picture?type=large`
                                        };
                                        uni.showLoading({
                                            title: '获取中'
                                        });
                                        loginFacebook(fbData).then(res=>{
                                            this.$store.commit("LOGIN", {
                                                'token': res.data.token
                                            });
                                            that.getUserInfo(res.data);
                                        })
                                    });
                                });
                            } else {
                                uni.showToast({
                                    title: '当前用户未登录Facebook或者您的网页',
                                    icon: 'none'
                                });
                            }
                        });
                    }, {
                        scope: 'email', 
                        return_scopes: true,
                    }
                );

            },
    }
}

```