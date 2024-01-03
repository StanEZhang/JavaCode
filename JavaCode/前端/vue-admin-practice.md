视频链接：https://www.bilibili.com/video/BV1QU4y1E7qo?p=8&vd_source=ce274e8f826988d000d436d695d52071

# 搭建vue脚手架

1.安装node node -v

2.安装npm npm -v

3.安装脚手架 vue-cli

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

验证 vue -V

4.创建项目 vue create my-project

5.运行npm run serve 运行项目



# ElementUI框架

## CDN

1.打开官网，复制helloworld到index.html打开页面看效果

优化：

进入https://cdnjs.com/网站，搜索vue,选择对应版本，下载到本地。

https://cdnjs.cloudflare.com/ajax/libs/vue/2.7.15/vue.min.js

````html
<script src="./vue.js"></script>
````

## npm安装

````
npm i element-ui -S
````

引入 Element分为全局引入和按需引入，这里不做赘述。

https://element.eleme.cn/#/zh-CN/component/quickstart

我直接全局引入。





以上Vue框架+UI框架，可以快速搭建网站。



# Vue-router

有单独的官方文档可参考：https://router.vuejs.org/zh/

vue2通常对应v3.x版本，记得切换文档

## npm安装

````
npm install vue-router@3
````

@3直接下载3的最新版本不需要指定具体小版本

新建./router/index.js

在index.js中进行路由配置，添加如下代码：

````javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
````

**补充：**

import是导入，对应export导出，模块化编程

## JavaScript

````javascript
// 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

// 1. 定义 (路由) 组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')

// 现在，应用已经启动了！
````

在App.vue中

````html
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
````

## 嵌套路由

````javascript
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
````

别忘了路由出口

````html
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
````

# 选择布局

alt + shift + F 格式化代码

## 左侧导航菜单

这里要学会组件化

把ElementUI里面抄的代码放在单独的component里

然后

````javascript
<script>
    //导入
import CommonAside from "../components/CommonAside.vue"
export default {
    // 导出
  components: { CommonAside },
  data() {
    return {}
  },
}
</script>
````

然后把放在它该放的位置。

````
<common-aside />
````

isCollapse: false改成false，默认不折叠



菜单数据：

````javascript
menuData: [
        {
          path: "/",
          name: "home",
          label: "首页",
          icon: "s-home",
          url: "Home/Home",
        },
        {
          path: "/mall",
          name: "mall",
          label: "商品管理",
          icon: "video-play",
          url: "MallManage/MallManage",
        },
        {
          path: "/user",
          name: "user",
          label: "用户管理",
          icon: "user",
          url: "UserManage/UserManage",
        },
        {
          label: "其他",
          icon: "location",
          children: [
            {
              path: "/page1",
              name: "page1",
              label: "页面1",
              icon: "setting",
              url: "Other/PageOne",
            },
            {
              path: "/page2",
              name: "page2",
              label: "页面2",
              icon: "setting",
              url: "Other/PageTwo",
            },
          ],
        },
      ],
````

### 一级菜单实现

在CommonAside.vue中

computed用于计算属性

````javascript
  computed: {
    // 没有子菜单
    noChildren() {
      return this.menuData.filter((item) => !item.children)
    },
    // 有子菜单
    hasChildren() {
      return this.menuData.filter((item) => item.children)
    },
  },
````

然后遍历，把noChildren的菜单放在单个菜单中，反之放在多层菜单中。

使用ES6模板字符串语法，动态绑定图标

```javascript
<i :class="`el-icon-${item.icon}`"></i>
```

绑定菜单名

```javascript
<span slot="title">{{item.label}}</span>
```



### 二级Menu的实现

一级菜单和上面一样

二级菜单有一点点不同，不过也差不多

```javascript
<el-menu-item-group v-for="subItem in item.children" :key="subItem.path">
    <el-menu-item :index="subItem.path">{{subItem.label}}</el-menu-item>
</el-menu-item-group>
```

### 菜单样式与less引入

改变菜单颜色

````javascript
  <el-menu
    default-active="1-4-1"
    class="el-menu-vertical-demo"
    @open="handleOpen"
    @close="handleClose"
    :collapse="isCollapse"
    background-color="#545c64"
    text-color="#fff"
    active-text-color="#ffd04b"
  >
````

菜单栏没有占满全部，怎么去掉滚动条？可以在浏览器里直接调试，找到css样式直接输入就行了。

要使用less语法，就要下载less.

```
npm i less@4.1.2 less-loader@5.0.0
```

```html
<style lang="less" scoped>
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}
.el-menu{
  height: 100vh;
  h3 {
    color: #fff;
  }
}
</style>
```

tips: ctrl + Enter跳转下一行

去除自带的一些样式，在App.vue中添加css样式：

```html
<style lang="less">
    html, body, h3 {
      margin: 0;
      padding: 0;
    }
</style>
```

```html
.el-menu {
  height: 100vh;
  h3 {
    color: #fff;
    text-align: center;
    line-height: 48px;
    font-size: 16px;
    font-weight: 400;
  }
}
```

### 菜单点击跳转功能实现

先新增路由页面

然后导入，然后定义路由

```javascript
import Mall from '../views/Mall.vue'
import PageOne from '../views/PageOne.vue'
import PageTwo from '../views/PageTwo.vue'

Vue.use(VueRouter)
// 2.定义路由
const routes = [
    {
        path: '/',
        component: Main,
        redirect: '/home',
        children: [
            {path: 'home', component: Home},
            {path: 'user', component: User},
            {path: 'mall', component: Mall},
            {path: 'page1', component: PageOne},
            {path: 'page2', component: PageTwo}
        ]
    }

]
```

添加点击事件：

```javascript
    clickMenu(item){
        this.$router.push(item.path)
    },
    clickSubMenu(subItem){
        this.$router.push(subItem.path)
    }
```

this就是vue实例，我们在前边已经在main.js把router加进去了

```javascript
new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```



## Header组件搭配与样式调整

重复路由报错问题解决，添加路由判断：

```javascript
    clickMenu(item){
      // 当页面的路由与跳转页面的路由不一致时才允许跳转，不然会报错
      if(this.$route.path !== item.path 
          && !(this.$route.path === '/home'
          &&(item.path === '/'))){
            this.$router.push(item.path)
          }
    },
```

新增header组件

```html
<template>
    <div class="header-container">
        <div class="l-content"></div>
        <div class="r-content"></div>
    </div>
</template>


<script>

export default {
    data(){
        return {}
    }
}
</script>

<style lang="less" scoped>
.header-container{
    background-color: #333;
    height: 60px;
}
</style>
```

导入Main.vue

```html
<script>
import CommonAside from "../components/CommonAside.vue"
import CommonHeader from "../components/CommonHeader.vue"
export default {
  components: { 
    CommonAside,
    CommonHeader
   },
  data() {
    return {}
  },
}
</script>
```

会发现header里面还有padding,直接在main.vue里面加上padding=0的style

```html
<style scoped>
.el-header{
  padding: 0;
}
</style>
```

header左侧第一个按钮：

```html
<style lang="less" scoped>
.header-container{
    background-color: #333;
    height: 60px;
    // 弹性布局，加了这个之后justify-content和align-items才会生效
    display: flex;
    // 使子元素平均分布在容器的水平方向上
    justify-content: space-between;
    // 垂直方向上居中对齐
    align-items: center;
    // 内边距。0 指的是上下没有内边距，而 20px 是指左右两侧各有20像素的内边距
    padding: 0 20px;
}
</style>
```

按钮旁边面包屑

`<span>` 是 HTML 中的一个内联元素，通常用于对文档中的小块内容应用样式或进行标记,而`<div>`是块级元素。

```html
<!-- 面包屑 -->
            <span class="text">首页</span>
```



```html
    .text{
        color: #fff;
        font-size: 14px;
        margin-left: 10px;
    }
```

右侧头像下拉菜单—elementUI Dropdown 下拉菜单

```html
  .r-content{
    .profile{
        width: 40px;
        height: 40px;
        border-radius: 50%;
    }
  }
```

## vuex实现左侧折叠

使用Vuex3版本，vuex是什么？

https://v3.vuex.vuejs.org/zh/

> Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

引入vuex@3版本：

```text
npm i vuex@3.6.2
```

创建src/store/index.js

```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import tab from './tab'

Vue.use(Vuex)

// 创建vuex的实例
export default new Vuex.Store({
    modules: {
        tab
    }
})
```

这里modules是为了让不同的状态分模块区分开来，目前的tab状态专门管左侧菜单栏的隐藏展开状态。

创建src/store/tab.js

```javascript
export default{
    state: {
        isCollapse: false // 控制菜单的展开和隐藏
    },
    mutations: {
        // 修改菜单展开收起的方法
        collapseMenu(state){
            // 这里就是说，如果你是展开，我们就隐藏，如果是你隐藏，我们就展开
            state.isCollapse = !state.isCollapse
        }
    }
}
```

store实例挂载到vue实例（在main.js中挂载）

绑定aside中的关联菜单是否展开的属性 至 state中的属性，通过computed方法

:collapse="isCollapse"绑定的这个isCollapse可以是data里面的值，也可以是computed里面的方法

```javascript
  computed: {
    isCollapse(){
      return this.$store.state.tab.isCollapse
    }
  },
```

有点乱，从头捋一遍：

aside里的侧边菜单栏通过isCollapse属性里的true和false来确定是否展开，我们把这个属性本来是固定的，放在data里的，现在要绑定按钮实现自由展开折叠。所以把这个属性就放在store里，怎么获取呢？

通过computed中的isCollapse()方法

```javascript
  computed: {
    isCollapse(){
      return this.$store.state.tab.isCollapse
    }
  },
```

然后绑定按钮事件@click=“handleMenu”

```javascript
  methods:{
    handleMenu(state){
        // 这里能调动是因为挂载到vue实例上了，collapseMenu方法也是我们导入到实例中的方法
        this.$store.commit("collapseMenu")
    }
  }
```

这里没有传参是因为vuex给自己把state传进去了。

然后来到collapseMenu方法,在这个方法中修改了isCollapse值，这个值一修改，computed方法就会把aside里的isCollapse值改了，然后页面就会展开折叠，就实现了这个功能。

```
export default{
    state: {
        isCollapse: false // 控制菜单的展开和隐藏
    },
    mutations: {
        // 修改菜单展开收起的方法
        collapseMenu(state){
            // 这里就是说，如果你是展开，我们就隐藏，如果是你隐藏，我们就展开
            state.isCollapse = !state.isCollapse
        }
    }
}
```

### 左侧折叠遗留问题解决

自动填充而不是固定200px

```html
<el-aside width="auto">
<common-aside />
</el-aside>
```

```html
<h3>{{isCollapse ? '后台' :'通用后台管理系统' }}</h3>
```

缝隙消除

```html
.el-menu {
  height: 100vh;
  h3 {
    color: #fff;
    text-align: center;
    line-height: 48px;
    font-size: 16px;
    font-weight: 400;
  }
// 缝隙消除
  border-right: none;
}
```

# home组件布局

1.加布局

```
<el-row>
  <el-col :span="12"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="12"><div class="grid-content bg-purple-light"></div></el-col>
</el-row>
```

2.加卡片

```
      <!-- 卡片 -->
      <el-card class="box-card">
    
      </el-card>
```

3.卡片里面2个div块

```html
<el-col :span="8">
      <!-- 卡片 -->
      <el-card class="box-card">
        <!-- 上半部分头像+简介 -->
        <div class="user">
            <!-- 头像 -->
          <img src="../assets/images/avatar.jpg" alt="" />
          <!-- 简介 -->
          <div class="userInfo">
            <p class="name">Admin</p>
            <p class="access">超级管理员</p>
          </div>
        </div>

        <!-- 下半部分登录信息 -->
        <div class="login-info">
            <p>上次登录时间：<span>2023-12-30</span></p>
            <p>上次登录地点：<span>北京</span></p>

        </div>
      </el-card>
    </el-col>
```

# home购买统计TABLE表

先加card,再放table,card上加上style

```html
<el-card style="margin-top: 20px; height: 460px;">
    <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="name" label="课程"> </el-table-column>
        <el-table-column prop="todayBuy" label="今日购买"> </el-table-column>
        <el-table-column prop="monthBuy" label="本月购买"> </el-table-column>
        <el-table-column prop="totalBuy" label="总购买"> </el-table-column>
    </el-table>
</el-card>
```

通过遍历数组的方式决定列名，而不是写死

报错：Elements in iteration expect to have 'v-bind:key' directives.eslint-plugin-vue

解决：设置搜索：eslint-plugin-vue 关闭检查

```
<el-table :data="tableData" style="width: 100%">
	<el-table-column v-for="(val, key) in tableLabel" :prop="key" :label="val"/>
</el-table>
```

```
tableLabel: {
    name: '课程',
    todayBuy: '今日购买',
    monthBuy: '本月购买',
    totalBuy: '总购买'
},
```

修正一个小问题：左侧菜单往下拉有空白没填充满：

```
.el-menu {
	// 修正100vh改成100%
  height: 100%;
  h3 {
    color: #fff;
    text-align: center;
    line-height: 48px;
    font-size: 16px;
    font-weight: 400;
  }
  border-right: none;
}
```

# home订单统计实现

数据：

```
countData: [
        {
          name: "今日支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "今日收藏订单",
          value: 1234,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "今日未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
        {
          name: "本月支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "本月收藏订单",
          value: 1234,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "本月未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
      ]
```

tips: shift + alt + ↑ 复制上一行

左边icon样式设置：

```
.num{
  .icon{
    width: 80px;
    height: 80px;
    font-size: 30px;
    text-align: center;
    line-height: 80px;
    color: #fff;
  }
}
```

```html
<div class="num">
    <el-card v-for="item in countData" :key="item.name" :body-style="{display: 'flex'}">
    	<i class="icon" :class="`el-icon-${item.icon}`" :style="{background: item.color}"></i>
        <div>
            <p>{{ item.value }}</p>
            <p>{{ item.name }}</p>
        </div>
    </el-card>
</div>
```

右边：

```
<div class="detail">
    <p class="price">¥ {{ item.value }}</p>
    <p class="desc">{{ item.name }}</p>
</div>
```

```
  .detail{
    margin-left: 15px;
    // 弹性布局
    display: flex;
    // 垂直方向从上到下排列
    flex-direction: column;
    // 垂直居中对齐
    justify-content: center;
    .price{
      font-size: 30px;
      margin-bottom: 10px;
      line-height: 30px;
      height: 30px;
    }
    .desc{
      font-size: 14px;
      color: #999;
      text-align: center;
    }
  }
```

调整：

```
.num{
  display: flex;
  // 弹性布局默认不换行，wrap会在必要时换行
  flex-wrap: wrap;
  justify-content: space-between;
  .icon{
    width: 80px;
    height: 80px;
    font-size: 30px;
    text-align: center;
    line-height: 80px;
    color: #fff;
  }

  .detail{
    margin-left: 15px;
    // 弹性布局
    display: flex;
    // 垂直方向从上到下排列
    flex-direction: column;
    // 垂直居中对齐
    justify-content: center;
    .price{
      font-size: 30px;
      margin-bottom: 10px;
      line-height: 30px;
      height: 30px;
    }
    .desc{
      font-size: 14px;
      color: #999;
      text-align: center;
    }
  }
  
  .el-card{
      width: 32%;
      margin-bottom: 20px;
    }
}
```

# axios的使用及二次封装

下载

```
npm install axios
```

新建src/utils/request.js	

```javascript
import axios from 'axios'

const http = axios.create({
    // 通用请求的地址前缀
    baseURL: '/api',
    // 超时时间 毫秒
    timeout: 10000,
})

// 添加请求拦截器
http.interceptors.request.use(function (config){
    //在发送请求之前做些什么
    return config;
}, function(error){
    // 对请求错误做些什么
    return Promise.reject(error);
});

// 添加响应拦截器
http.interceptors.response.use(function(response){
    // 对响应数据做点什么
    return response;
}, function(error){
    // 对响应错误做点什么
    return Promise.reject(error);
});

export default http
```

新建src/api/index.js存放接口

```javascript
import http from "../utils/request"

// 请求首页数据
export const getData = () =>{
    // 返回一个promise对象
    return http.get('/home/getData')
}

```

发请求：

```
<script>
import {getData} from '../api'

export default {
  data() {
    return {
    }
  },
  mounted(){
    getData().then((data) =>{
      console.log(data)
    })
  }
}
</script>
```

# mock数模拟实战

下载mock

```
npm i mockjs
```

新建src/api/mock.js

```javascript
import Mock from 'mockjs'

// 定义mock请求拦截,r如果是get可以省略参数
Mock.mock('/api/home/getData', function(){
    // 拦截到请求后的处理逻辑
    console.log('mock拦截请求成功！')
})
```

main.js引入

```
import './api/mock'
```

这里出了一个小问题：路径写错少了个/,结果查半天bug./api/home/getData





新建src/api/mockServeData/home.js和src/api/mockServeData/permission.js

home.js内容

```javascript
import Mock from "mockjs"

// 图表数据
let List = []
export default {
  getStatisticalData: () => {
    //Mock.Random.float 产生随机数100到8000之间 保留小数 最小0位 最大0位
    for (let i = 0; i < 7; i++) {
      List.push(
        Mock.mock({
          苹果: Mock.Random.float(100, 8000, 0, 0),
          vivo: Mock.Random.float(100, 8000, 0, 0),
          oppo: Mock.Random.float(100, 8000, 0, 0),
          魅族: Mock.Random.float(100, 8000, 0, 0),
          三星: Mock.Random.float(100, 8000, 0, 0),
          小米: Mock.Random.float(100, 8000, 0, 0),
        })
      )
    }
    return {
      code: 20000,
      data: {
        // 饼图
        videoData: [
          {
            name: "小米",
            value: 2999,
          },
          {
            name: "苹果",
            value: 5999,
          },
          {
            name: "vivo",
            value: 1500,
          },
          {
            name: "oppo",
            value: 1999,
          },
          {
            name: "魅族",
            value: 2200,
          },
          {
            name: "三星",
            value: 4500,
          },
        ],
        // 柱状图
        userData: [
          {
            date: "周一",
            new: 5,
            active: 200,
          },
          {
            date: "周二",
            new: 10,
            active: 500,
          },
          {
            date: "周三",
            new: 12,
            active: 550,
          },
          {
            date: "周四",
            new: 60,
            active: 800,
          },
          {
            date: "周五",
            new: 65,
            active: 550,
          },
          {
            date: "周六",
            new: 53,
            active: 770,
          },
          {
            date: "周日",
            new: 33,
            active: 170,
          },
        ],
        // 折线图
        orderData: {
          date: ["20191001", "20191002", "20191003", "20191004", "20191005", "20191006", "20191007"],
          data: List,
        },
        tableData: [
          {
            name: "oppo",
            todayBuy: 500,
            monthBuy: 3500,
            totalBuy: 22000,
          },
          {
            name: "vivo",
            todayBuy: 300,
            monthBuy: 2200,
            totalBuy: 24000,
          },
          {
            name: "苹果",
            todayBuy: 800,
            monthBuy: 4500,
            totalBuy: 65000,
          },
          {
            name: "小米",
            todayBuy: 1200,
            monthBuy: 6500,
            totalBuy: 45000,
          },
          {
            name: "三星",
            todayBuy: 300,
            monthBuy: 2000,
            totalBuy: 34000,
          },
          {
            name: "魅族",
            todayBuy: 350,
            monthBuy: 3000,
            totalBuy: 22000,
          },
        ],
      },
    }
  },
}

```

导入mock.js

```javascript
import Mock from 'mockjs'
import homeApi from './mockServeData/home'
// 定义mock请求拦截,r如果是get可以省略参数
Mock.mock('/api/home/getData', homeApi.getStatisticalData)
```

# 首页可视化图标样式调整

mock动态数据代替原始静态数据

```js
getData().then(({data}) =>{
    // 这一行是JavaScript的解构赋值语句。
    // 它假设data.data是一个包含tableData属性的对象，
    // 并将这个tableData属性的值赋给同名的常量tableData。
    const { tableData }=data.data
    console.log(data)
    this.tableData = tableData
})
```

画3个card，呈“品”字型

```html
<el-card style="height: 280px;">
    <!-- 折线图 -->
</el-card>
<div class="graph">
    <el-card style="height: 260px;"></el-card>
    <el-card style="height: 260px;"></el-card>
</div>
```

```
.graph{
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
  .el-card{
    width: 48%;
  }
}
```

# echarts表格的基本使用

基本使用

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>ECharts</title>
    <!-- 引入刚刚下载的 ECharts 文件 -->
    <script src="https://cdn.staticfile.org/echarts/4.3.0/echarts.min.js"></script>
</head>
  <body>
    <!-- 为 ECharts 准备一个定义了宽高的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
    <script type="text/javascript">
      // 基于准备好的dom，初始化echarts实例
      var myChart = echarts.init(document.getElementById('main'));

      // 指定图表的配置项和数据
      var option = {
        title: {
          text: 'ECharts 入门示例'
        },
        // 鼠标悬停显示的悬浮框
        tooltip: {},
        // 图例
        legend: {
          data: ['销量']
        },
        // x轴
        xAxis: {
          data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
        },
        yAxis: {},
        series: [
          {
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
          }
        ]
      };

      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option);
    </script>
  </body>
</html>
```

# echarts表格的折线图

安装echarts

```
npm i echarts@5.1.2
```

import echarts，一定不要落下引号

```
import * as echarts from 'echarts'
```

创建一个容器

```
<div ref="echarts1" style="height: 280px;"></div>
```

`ref="echarts1"` 是一个属性，通常用于为这个特定的 `div` 元素指定一个参考标识（reference identifier)



```js
// 基于准备好的dom，初始化echarts实例
const echarts1 = echarts.init(this.$refs.echarts1)
```

**$refs**: 在 Vue.js 中，`$refs` 是一个对象，它存储了所有带有 `ref` 属性的 DOM 元素。当你在模板中的一个 HTML 元素上使用 `ref="someRefName"` 属性时，你可以通过 `this.$refs.someRefName` 访问到这个元素。它是一种直接从 JavaScript 访问 DOM 元素的方法。



下面就是按照echarts的demo一个一个数据填进去就差不多了。

第一步echarts.init

第二步填充option数据

x轴、y轴、图例、series

重点说一下series：

```
在 ECharts 中，series 是一个数组，其中的每个对象代表图表中的一个系列。每个系列可以独立配置，包括系列的类型、名称、数据等。在你的代码示例中，series 定义了一个系列，具体如下：
name: 这个属性为系列命名，这里的名称是 '销量'。它通常用于图例和工具提示中，用于识别不同的数据系列。
type: 定义了图表的类型。这里使用的是 'bar'，表示这是一个条形图系列。
data: 提供了系列的具体数据。这里的数据是 [5, 20, 36, 10, 10, 20]，表示每个类别（比如 '衬衫', '羊毛衫' 等）的销量数据。
```

第三步echarts1.setOption

```js
// 基于准备好的dom，初始化echarts实例
const echarts1 = echarts.init(this.$refs.echarts1)
// 指定图表的配置项和数据
var echarts1Option = {}
// 处理数据xAxis x轴
const { orderData } = data.data
const xAxis = Object.keys(orderData.data[0])

const xAxisData = {
    data: xAxis
}
echarts1Option.xAxis = xAxisData
// 处理y轴
echarts1Option.yAxis = {}
//处理图例
echarts1Option.legend = xAxisData

// 处理series
echarts1Option.series = []
xAxis.forEach((key) => {
    echarts1Option.series.push({
        name: key,
        type: "line",
        // 这个data是最难的
        data: orderData.data.map((item) => item[key]),
    })
})
// 使用刚指定的配置项和数据显示图表。
echarts1.setOption(echarts1Option)
```

重点记一下这段代码的含义：

```js
xAxis.forEach((key) => {
    echarts1Option.series.push({
        name: key,
        type: "line",
        // 这个data是最难的
        data: orderData.data.map((item) => item[key]),
    })
})
```

遍历这个xAxis数组，里面都是苹果、小米这些字符串

遍历中把每个苹果小米系列push到series数组中，每个元素都是一个表格系列

name和type就不说了，都很简单

这个data是什么？在demo中data是从左到右一系列柱状图的数据[5, 20, 36, 10, 10, 20]

我们我们line类型的表格有多个系列，所以目标就是把苹果系列、小米系列...数据都遍历出来

数据在哪？

在orderData.data里

.map方法是干什么的？

它是一个数组方法，通过某种方法映射，对原数组操作生成一个新数组。

item就是原数组的每个元素，它长这样：{苹果: 3190, vivo: 3905, oppo: 2655, 魅族: 614, 三星: 4546}

这是遍历的数组对象中其中的一个，它是一个对象，也是一个key-value集合。

item[key]表示访问每个item对象中的key属性对应的值，遍历拿出来

然后就变成了map后的新数组[100,200,300,200,400]

遍历过程中，苹果系列得到一个数组，小米系列得到一个数组，最终形成整个的series。

# echarts表格的折线图、饼状图

简单，照抄，省略