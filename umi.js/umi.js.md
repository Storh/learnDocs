# 路由
https://umijs.org/zh-CN/docs/routing
## 关于子路由
一个路由可以配置子路由，但是如果该路由填写了component内容，那么就需要给这个组件添加props.children来渲染子路由。
因为访问子路由的时候，会带上父组件的内容。也就是路径改变=>子路由指向组件路径的子组件改变，父组件不变。
## 能做到的事
路由跳转
组件封装wrappers，比如给路由添加权限校验
页面跳转（history或link）
获取信息,在prpps中获取：{
match:当前路由和 url match 后的对象，包含 params、path、url 和 isExact 属性
location:表示应用当前处于哪个位置，包含 pathname、search、query 等属性
history:同 api#history 接口
route:当前路由配置，包含 path、exact、component、routes 等
routes:全部路由信息}
传递参数
## 菜单显示
name:string 配置菜单的 name，如果配置了国际化，name 为国际化的 key。
icon:string 配置菜单的图表，默认使用 antd 的 icon 名，默认不适用二级菜单的 icon。
access:string 权限配置，需要预先配置权限
hideChildrenInMenu:true 用于隐藏不需要在菜单中展示的子路由。
hideInMenu:true 可以在菜单中不展示这个路由，包括子路由。
hideInBreadcrumb:true 可以在面包屑中不展示这个路由，包括子路由。
headerRender:false 当前路由不展示顶栏
footerRender:false 当前路由不展示页脚
menuRender: false 当前路由不展示菜单
menuHeaderRender: false 当前路由不展示菜单顶栏
flatMenu 子项往上提，只是不展示父菜单
# 布局
## 一点理解
antd pro的新增页面中的新增布局（https://pro.ant.design/zh-CN/docs/new-page#%E6%96%B0%E5%A2%9E%E5%B8%83%E5%B1%80），其中提到的“config.ts 是一个数组，其中第一级数据就是我们的布局”，指的应该是路由配置。及，通过路由配置component属性来给一批子路由配置布局。

## getInitialState
```javascript
/**
 * @see  https://umijs.org/zh-CN/plugins/plugin-initial-state
 * */
//  getInitialState会在整个应用最开始执行，返回值会作为全局共享的数据。所以下面的是否在首页涉及到了首次打开时登录的地方

//  getInitialState的函数返回值（函数后面的:代表了函数返回值，变量后面的:代表了变量的类型）是一个Promise泛型（通过泛型来使得对类型的约束可以复用）
//  https://jkchao.github.io/typescript-book-chinese/typings/generices.html
// 同时利用可选参数和可选属性，使得返回的数据也可以全是undefined
// ?: 表示ts的可选参数和可选属性 如果使用了?:，可选参数会被自动地加上 | undefined 
// https://www.tslang.cn/docs/handbook/advanced-types.html

// 另：async的返回值是一个promise，这个promise要么会通过一个由async函数返回的值被解决，要么会通过一个从async函数中抛出的异常被拒绝
// async function foo() {
//   return 1
// }
// 等价于
// function foo() {
//   return Promise.resolve(1)
// }
// 没有return，就是promise的.then方法返回了一个undefined，但是是在await的promise的.then方法之后（注意promise的链式调用）
// async function foo() {
//   await 1
// }
// 等价于
// function foo() {
//   return Promise.resolve(1).then(() => undefined)
// }
// https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function
// https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise
export async function getInitialState(): Promise<{
  settings?: Partial<LayoutSettings>;
  currentUser?: API.CurrentUser;
  // fetchUserInfo是一个promise，这个promise的内容可以是queryCurrentUser()之后返回的data，要符合API.CurrentUser，或者promise返回了undefined
  fetchUserInfo?: () => Promise<API.CurrentUser | undefined>;
}> {
  const fetchUserInfo = async () => {
    try {
      const msg = await queryCurrentUser();
      return msg.data;
    } catch (error) {
      history.push(loginPath);
    }
    return undefined;
  };
  // 非登录界面
  if (history.location.pathname !== loginPath) {
    const currentUser = await fetchUserInfo();
    return {
      fetchUserInfo,
      currentUser,
      settings: {},
    };
  }
  // 如果是登录页面，不执行fetchUserInfo，currentUser为undefined
  return {
    fetchUserInfo,
    settings: {},
  };
}
```
## 布局修改
布局修改个人感觉主要涉及了两个文件，一个是config\config.ts，另一个是src\app.tsx中的layout部分，需要分别结合https://umijs.org/zh-CN/plugins/plugin-layout、https://ant.design/docs/react/customize-theme-cn和https://procomponents.ant.design/components/layout去理解修改。
在目前的工作中，主要是删除了config中的layout，然后配合antd的组件文档进行修改。然后因为页面的布局的具体的标签在app.tsx中所以在其中根据业务需求进行修改
?.的意思基本和 && 是一样的 a?.b 相当于 a && a.b ? a.b : undefined

# 网络请求
网络请求的写法参考ant-d-pro的写法
src\services\ant-design-pro\api.ts
src\app.tsx
src\services\ant-design-pro\typings.d.ts
.d.ts为typescript的类型声明
同时，网络请求采用的是umi-request，后续可以考虑学习和使用ahooks
https://github.com/umijs/umi-request/blob/master/README_zh-CN.md
https://ahooks.js.org/zh-CN/hooks/async
https://pro.ant.design/zh-CN/docs/request/#%E4%BD%BF%E7%94%A8-request
因为开发的时候，往往后端还没有开发完成或者有时候是远程mock或者前后分离开发，所有会有跨域问题，那么设置proxy来实现，但是要注意一个问题就是proxy在部署时不会生效，需要自己采用prefix前缀的方式访问后端。
针对采用自带mock的情况

# model
https://dvajs.com/guide/introduce-class.html#%E6%95%B0%E6%8D%AE%E6%B5%81%E5%9B%BE
umijs采用的是dvajs，整体的逻辑和构架如下：
主要由3个部分和2个方法构成

State：一个对象，保存整个应用状态
View：React 组件构成的视图层
Action：一个对象，描述事件
connect 方法：一个函数，绑定 State 到 View
dispatch 方法：一个函数，发送 Action 到 State

action里写了要进行哪些操作时间来改变state，而state和视图的绑定是通过connect实现的，将一个函数和组件绑定。在绑定组件的同时，组件会自动在 props 中拥有 dispatch 方法，而这个方法就是action中设置的方法。
具体实现时是通过model的形式。

namespace: 当前 Model 的名称。整个应用的 State，由多个小的 Model 的 State 以 namespace 为 key 合成
state: 该 Model 当前的状态。数据保存在这里，直接决定了视图层的输出
reducers: Action 处理器，处理同步动作，用来算出最新的 State
effects：Action 处理器，处理异步动作

