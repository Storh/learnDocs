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
一点理解
antd pro的新增页面中的新增布局（https://pro.ant.design/zh-CN/docs/new-page#%E6%96%B0%E5%A2%9E%E5%B8%83%E5%B1%80），其中提到的“config.ts 是一个数组，其中第一级数据就是我们的布局”，指的应该是路由配置。及，通过路由配置component属性来给一批子路由配置布局。
