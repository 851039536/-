# vue实现分页功能



## 1.数据展示

```vue
<template>
    <div class="sn-text ">
        <div class="sn-text1" v-for="(info, index) in dataShow"
             :class="{ 'alt': index%2==1 }" :key="info.articleId">
            <img src="../../assets/img/003.jpg" align="right"/>
            <h5 v-on:click="AsyGetTestID(info.articleId)">
                {{ info.title }}
            </h5>
            <p class="p-1">
                {{ info.titleText }}
            </p>

<!--        v-for="info in newinfo" :key="info.articleId"-->
            <div class="sn-text2">
                <p>{{ info.time }}</p>
                <p>
                    {{info.comment}}
                </p>
                <p>
                    {{info.read}}
                </p>
                <p>
                    {{info.give}}
                </p>
                <p>
                    Junior
                </p>
                <p class="t-1" @click="AsyGetTestID(info.articleId)">全文阅读</p>
            </div>
        </div>

        <div class="page">
            <ul>
                <li class="inline-block text-gray-700 text-center bg-gray-400 px-4 py-2 m-2">
                    <a href="#"  v-on:click="prePage"> &lt; </a>

                </li>

                <li class="inline-block text-gray-700 text-center bg-gray-400 px-4 py-2 m-2" v-for="(item, index) in totalPage" :key="item.articleId">
                    <a href="#" v-on:click="toPage(index)" :class="{active: currentPage==index}">{{ index+1 }}</a>
                </li>
                <li class="inline-block text-gray-700 text-center bg-gray-400 px-4 py-2 m-2">
                    <a href="#" v-on:click="nextPage">&gt;</a>
                </li>
            </ul>
        </div>

    </div>
</template>
```

## 2.data

```js
 data() {
            return {
                newinfo: [],
                Labels: [],
                //每页展示多少内容
                perPage: 10000,
                // 总页数
                pageNum:1,
                // 每页显示的个数
                pageSize:4,
                // 当前页
                currentPage:0,
                // 总数据
                totalPage:[],
                // 当前显示的数据
                dataShow:[]
            }
        },
            
            created() {
            this.AsyGetTest()
        },
           methods: {
            nextPage: function(){
                var vm = this;
                if (vm.currentPage == vm.pageNum - 1) return;
                vm.dataShow = vm.totalPage[++vm.currentPage]
            },
            prePage: function(){
                var vm = this;
                if (vm.currentPage == 0) return;
                vm.dataShow = vm.totalPage[--vm.currentPage]
            },
            toPage: function(page){
                var vm = this;
                vm.currentPage = page
                vm.dataShow = vm.totalPage[vm.currentPage];
            },
            //加载文章
            AsyGetTest() {
                request({
                    url: '/api/SnArticle/GetTest'
                }).then(res => {
                    // 只获取0到4之间的数据
                    this.newinfo = res.data.slice(0, this.perPage);

                    var vm = this;
                    // 总页数
                    vm.pageNum = Math.ceil(vm.newinfo.length / vm.pageSize) || 1 ;
                    // 分组
                    for (var i = 0; i<vm.pageNum; i++) {
                        vm.totalPage[i] = vm.newinfo.slice(vm.pageSize * i, vm.pageSize * (i + 1))
                    }
                    // 取值
                    vm.dataShow =  this.newinfo[vm.currentPage];
                    // vm.nextPage();

                }).catch((e) => {
                    console.log(e + '获取数据失败');
                });

                //首次加载
                request({
                    url: '/api/SnArticle/GetTest'
                }).then(res => {
                    // 只获取0到4之间的数据
                    this.dataShow = res.data.slice(0, 4);
                }).catch((e) => {
                    console.log(e + '获取数据失败');
                });
            },
           
        }
```

