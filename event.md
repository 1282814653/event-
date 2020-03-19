# 事件

## onlond 事件加载

```js
function addLoad(fun) {
    var oladNew = window.onload;
    //   如果 加载元素 为true 那么就直接执行传入的元素
    if (typeof window.onload != "function") {
        window.onload = fun;
    } else {
        //   否则就 一个一个 执行加载
        window.onload = function() {
            oladNew();
            fun();
        };
    }
}
```

## 在某个元素之后插入元素

```js
function insertAfter(newEl, targetEl) {
    // 获取 目标元素的父元素
    var parent = targetEl.parentNode;
    //   如果 父元素的最后一个元素等于  目标元素
    if (parent.lastChild == targetEl) {
        //   那么直接在父元素后面插入
        parent.appendChild(newEl);
    } else {
        //   否则 相对于目标元素的下一个兄弟元素进行插入
        parent.insertBefore(newEl, targetEl.nextSibling);
    }
}
```

## 回到顶部(缓动)

```js
// 三个参数 类名|时间(千分制)| 滚动高度
getTop("top", 2000, 0);

function getTop(num, timer, height) {
    // 类名点击的时候 执行函数
    $("." + num).click(function() {
        // 获取当前的页面的位置 然后执行动画会到某个位置 需要多少秒
        $(document.documentElement).animate(
            {
                scrollTop: height || 0
            },
            timer
        );
        // 获取当前元素的高度
        console.log($(document.documentElement).scrollTop());
    });
}
```

## 默认事件与冒泡

```js
// 阻止默认事件
$("a").click(function(event) {
    console.log("你好吗? 明天!");
    event.preventDefault();
    // 取消默认事件
});
// 父元素
$(".box").click(function(event) {
    console.log("box 盒子");
    // event.stopPropagation();
});
// 阻止子元素像父元素身上冒泡
$(".inner-box").click(function(event) {
    console.log("你好 ");
    event.stopPropagation();
});

// 主要的event 对象事件
// event.data 传递给事件处理程序的额外数据
// event.currentTarget 事件绑定的对象(事件源), 和 this 相同
// event.pageX 鼠标相对于文档左部边缘的位置
// event.target 实际触发事件的对象， 不一定 === this
// event.type 事件类型： click， mouseover…
// event.which 鼠标的按键类型： 左 1 中 2 右 3
// event.keyCode 键盘按键代码
```

## 事件委托

```js
// 选择父类 当点击的子元素的时候执行函数
$("ul").on("click", "li", function() {
    // 打印 子元素的 val 值
    console.log($(this).text());
});
```

## 类名的添加

```js
function addClass(ele, orangeCl) {
    // 如果没有 类名 那么 就直接赋值
    if (!ele.className) {
        ele.className = orangeCl;
    } else {
        //   有的话 就进行累加
        var newClass = ele.className;
        newClass += " ";
        newClass += orangeCl;
        ele.className = newClass;
    }
}
```

## 输入框的聚焦

```js
// 聚焦
function focusLablse() {
    // 获取 lable 元素
    var lables = document.getElementsByTagName("lable");
    //   循环 所有的 lable 元素
    for (var i = 0; i < lables.length; i++) {
        //  在lable 元素中 如果没有 获取到 for 就跳过
        if (!lables[i].getAttribute("for")) continue;
        // 当前的 lable 元素 点击的时候
        lables[i].onclick = function() {
            // 获取当前元素的 for 属性
            var id = this.getAttribute("for");
            // 如果 for 属性 这个 内容找不到 就返回 false
            if (!document.getElementById(id)) return false;
            //   找到的 话 就使用 focus() 聚焦
            var ele = document.getElementById(id);
            //   元素  聚焦
            ele.focus();
        };
    }
}
window.onload(focusLablse);
```

## 奇偶行变色

```js
// 基数行 偶数行 变色
function stripeTables() {
    var tables = document.getElementsByTagName("table");
    for (var i = 0; i < tables.length; i++) {
        var odd = false;
        var rows = tables[i].getElementsByTagName("tr");
        for (var j = 0; j < rows.length; j++) {
            if (odd == true) {
                rows[j].setAttribute("class", "odd");
                odd = false;
            } else {
                odd = true;
            }
        }
    }
}
window.onload(stripeTables);
```

## ajax 封装

```js
function ajax(obj) {
    obj.method = obj.method || "GET";
    obj.params = obj.params || "null";
    obj.success =
        obj.success ||
        function(res) {
            console.log(res);
        };
    obj.fail =
        obj.fail ||
        function() {
            console.log(fail);
        };
    var xhr = new XMLHttpRequest();
    xhr.onload = function() {
        var res = JSON.parse(this.responseText);
        obj.success(res);
    };
    xhr.onerror = function() {
        obj.fail();
    };
    xhr.open(obj.method, obj.url);
    if (obj.setHeader) {
        for (var i in obj.setHeader) {
            xhr.setRequestHeader(i, obj.setHeader[i]);
        }
    }
    xhr.send(obj.params);
}
ajax({
    url: "xx",
    // method:"GET" // 默认不写
    success: function(res) {
        console.log(res);
    }
});
```

## 计数器 查看网页访问量

```html
<body>
    <span class="p3"></span>
    <script>
        var pps = document.querySelector(".p3");
        console.log(pps);
        if (localStorage.pagecount) {
            localStorage.pagecount = Number(localStorage.pagecount) + 1;
        } else {
            localStorage.pagecount = 1;
        }
        document.write("网页访问人数: " + localStorage.pagecount + "");
        console.log(localStorage.pagecount);
        pps.innerHTML = JSON.parse(JSON.stringify(localStorage.pagecount));
    </script>
</body>
```

## 文件(头像)上传

```html
<!--  vue 文件 -->
<template>
    <div class="">
        <!-- 用表单可以最直接 最快速的实现文件上传 
        注意: 1. enctype 表单文件类型; 如果是file必须设置成二进制形式
            2. input type='file' 必须写 name; 默认都是 name="file"
            3. 直接跟随表单的上传 一般不用了

        <form action="/api/leju/admin/material/uploadImg" enctype="multipart/form-data">
            <input type="file" name="file">
            <button type="submit">上传</button>
        </form> -->
        <img
            :class="{succ:succ}"
            :src="avatar"
            alt=""
            style="width: 200px;height: 200px"
        />
        <!-- 一般采用异步上传 
        mdn:    https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input/file
        -->
        <div class="upload-area">
            <input
                class="upload-input"
                type="file"
                name="file"
                accept="image/*"
                @change="fileChange($event)"
            />
            <div class="upload-btn">
                上传
            </div>
        </div>
    </div>
</template>

<script>
    import axios from "axios";
    export default {
        components: {},
        data() {
            return {
                avatar: "",
                succ: false
            };
        },
        computed: {},
        created() {},
        mounted() {},
        methods: {
            fileChange(e) {
                var vm = this;
                //事件触发的对象
                var target = e.target;
                // 上传文件的内容 在el对象的files中保存
                // 往后台发送的实际上是这个内容
                console.log(target.files[0]);
                var file = target.files[0];
                //图像的加载是异步的 所以必须等图片加载完毕后处理
                var img = new Image();
                // createObjectURL把资源转换为url
                img.src = window.URL.createObjectURL(file);
                img.onload = function() {
                    vm.avatar = this.src;
                };
                // 发送图片到后台
                // 原生ajax上传 需要用 FormData对象 包括file数据
                var formData = new FormData();
                formData.append("file", file);
                // if(file.size>1024*1024){
                //     alert('文件不能超过1M');
                //     return;
                // }
                if (!/image\/(jpeg|png|jpg)/.test(file.type)) {
                    alert("只能选择jpg或者png类型");
                    return;
                }
                axios
                    .post(
                        "/api/leju/admin/material/uploadImg",
                        formData,
                        //修改http的headers  标识上传的是二进制文件
                        {
                            headers: {
                                "Content-Type": "multipart/form-data"
                            },
                            // 上传一段就会触发一次
                            onUploadProgress: function(progressEvent) {
                                // Do whatever you want with the native progress event
                                // console.log(progressEvent);
                                console.log(
                                    "upload: " +
                                        Math.floor(
                                            (progressEvent.loaded /
                                                progressEvent.total) *
                                                100
                                        ) +
                                        "%"
                                );
                            }
                        }
                    )
                    .then(res => {
                        // var code = res.code;
                        console.log(res);
                        //成功之后处理文件
                        if (res.data.code == "S") {
                            vm.succ = "succ";
                        } else {
                            alert("上传失败");
                            vm.avatar = "";
                        }
                        // console.log(res);
                    });
            }
        }
    };
</script>

<style scoped lang="scss">
    img.succ {
        border: 6px solid green;
    }
    .upload-area {
        position: relative;
        width: 100%;
        height: 40px;
        background-color: pink;
        .upload-input {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            z-index: 999;
            opacity: 0;
        }
        .upload-btn {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background: green;
            font-size: 18px;
            text-align: center;
            line-height: 40px;
            color: #fff;
        }
    }
</style>
```
