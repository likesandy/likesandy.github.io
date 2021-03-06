axios 提供了拦截器,用于我们在发送每次请求或者得到相应的东西后,进行对应的处理

axios 拦截器配置了**请求**和**响应**两种拦截

那么拦截的意义是什么喃

1.  比如我们请求的数据中的 config 的一些信息不符合服务器的要求,我们就可以拦截下来然后修改,最后返回出去
2.  比如当我们请求的数据很多的时候,界面上就会等待,出现白屏的效果,用户体验就很差,我们可以把数据拦截下来然后界面显示一个 Loding 的组件,当数据请求完毕之后,再把数据返回出去,然后把这个 Loding 组件取消掉
3.  比如我们在某些网络请求的时候,如果用户没有登录,没有携带对应的 token,那么就拦截这个请求,然后提示用户登录或者跳转到登录界面

有一点**很重要**,我们拦截了之后一定要把数据给 return 出去,不然外面无法接收到数据

```js
// 请求拦截
instance.interceptors.request.use(
  (config) => {
    return config;
  },
  (err) => {
    console.log(err);
  }
);
// 响应拦截
instance.interceptors.response.use(
  (res) => {
    return res;
  },
  (err) => {
    console.log(err);
  }
);
```
> [!TIP]
> axios 的整个流程：发送网络请求->拦截请求->响应拦截->处理数据

