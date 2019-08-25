### vue中页面的优化功能
   1.路由的懒加载的优化(须按需加载)
         import Vue from "vue";
        import Router from "vue-router";
        Vue.use(Router);
        export default new Router({
        mode: "history",
        routes:[
            {
                path:"/",
                name:"home",  //首页
                component:()=>require("@/home/index.vue")
            },
            {
                path:"/login",
                name:"login", //登陆
                component:()=>require("@/login/index.vue")
            },
            {
                path:"/list",
                name:"list",  //列表
                component:()=>require("@/list/index.vue")
            }
        ]
        )}
    2.源码优化
        (1)v-if和v-show的选择

           区别：v-if的显示隐藏是将dom元素添加或者删除   v-show:为该元素添加display:none 或 display:block，本身的dom元素还在。再重复使用显示或隐藏时可以使用 v-show
       (2)唯一key值
           在列表数据进行遍历渲染时，需要为每一项item设置唯一key值,方便制精准找到该条列表数据
       (3)分vuejs组件 
            当一个组件的数据变多时,会因为该组件的代码变多从而导致加载速度缓慢，所以需要举荐的划分 
       (4) 减少watch的使用
               在数据变化的同时进行异步操作时可以采用watch。当watch的数据变大时会让系统出现卡顿，所以要减少watch的数据
               computed:计算属性，当方法内部依赖的值发生变化时会执行

       (5) 图片按需加载
             如果出现图片加载比较多,可以使用懒加载库（vue-lazyload），减少系统加载的数据
       (6)keep-live
            有一个列表和一个详情，当滚动到想要看到的列表时，点击切换到详情，然后再切回到列表页面会看到还在原来的地方（当切换页面时会保存原来的状态）

           
    