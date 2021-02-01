<!-- # 开发小程序遇到的问题记录 -->

## 小程序登录鉴权

### 基础

[官网介绍](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/login.html)

[参考资料](https://segmentfault.com/a/1190000014234141)

### 总结

授权用户信息

提示授权，点击授权后，调用wx.login()函数发送code给自己的后端，后端返回获取openid和session_key，通过加密返回token给前端

wx.setStorageSync("token", token) 储存token。

得到token后，放到请求的Header中。调用接口时，后端通过token校验。

> 授权手机也同理

```js
const handleGetUserInfo = (e) => {
  const { userInfo } = e.detail;
  const { login } = props;
  if (userInfo) {
    // 获取用户信息成功，保存到redux的store中
    saveUserInfo(userInfo);
    wx.login({
      success: (res) => {
        // 用 code 换取 session_key、openId等信息
        login(res.code, userInfo);
      },
      fail: (res) => {
        console.log("login登录失败", res);
      },
    });
  }
};

// action
export const login = (js_code, userInfo) => (dispatch) => {
  console.log("js_code:", js_code);
  return request({
    url: `/api/v1/user/login`,
    data: { js_code: js_code },
    method: "POST",
    success: (res) => {
      console.log("请求成功：", res);
      if (res.data.statusCode === 200) {
        // token、userId信息保存至storage中
        wx.setStorageSync("token", res.data.data.skey);
        wx.setStorageSync("userId", res.data.data.user_id);
        // 保存更新用户信息至自己服务器
        dispatch(updateUserInfo(userInfo));
      } else {
        console.log(res.data.message);
      }
    },
  });
};
```

app.js 文件

```js
const loginFlag = wx.getStorageSync("token");
if (loginFlag) {
  // 检查 session_key 是否过期
  wx.checkSession({
    // session_key 有效(未过期)
    success: function () {
      // 业务逻辑处理
      // 设置dispatch userinfo信息
      wx.getUserInfo({
        success: (res) => {
          const { userInfo } = res;
          store.dispatch(saveUserInfoAction(userInfo));
          const app = wx.getApp();
          // 回调函数--当’我的页面’还未获取userInfo信息
          if (app.userInfoReadyCallback) {
            app.userInfoReadyCallback();
          }
        },
        fail: () => {
          console.log("用户还未授权");
        },
      });
    },
    // session_key 过期
    fail: function () {
      // session_key过期，重新登录
      wx.login({
        success: (res) => {
          console.log("App--login登录成功", res);
          store.dispatch(login(res.code));
        },
        fail: (res) => {
          console.log("login登录失败", res);
        },
      });
    },
  });
} else {
  // 无skey，作为首次登录
  console.log("首次登录，无loginFlag");
}
```

**注意**
- session_key和微信派发的code是一一对应的，同一code只能换取一次session_key。每次调用wx.login()，都会下发一个新的code和对应的session_key，为了保证用户体验和登录态的有效性，开发者需要清楚用户需要重新登录时才去调用wx.login()

- session_key是有失效性的，即便是不调用wx.login，session_key也会过期，过期时间跟用户使用小程序的频率成正相关，但具体的时间长短开发者和用户都是获取不到的


## 插件

- [图片裁剪 image-cropper](https://github.com/wx-plugin/image-cropper)
- [海报组件-生成朋友圈分享海报并生成图片 wxa-plugin-canvas](https://github.com/jasondu/wxa-plugin-canvas)
