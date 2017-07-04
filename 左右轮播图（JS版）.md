#左右轮播图（JS版）

@(项目)

```
//获取JS操作需要的元素
var banner = document.getElementById("banner");
var bannerInner = banner.getElementsByClassName("bannerInner")[0];
var focusList = banner.getElementsByClassName("focusList")[0];
var leftBtn = public.children(banner, "a")[0];
var rightBtn = public.children(banner, "a")[1];
//通过DOM提供的方法获取出来的元素，不管将来变成什么样，都与页面存在这一一对应的关系，这就叫做DOM映射。
var imgList = bannerInner.getElementsByTagName("img");
var list = focusList.getElementsByTagName("li");

//1.通过AJAX获取数据
var xhr = new XMLHttpRequest();
xhr.open("GET", "data.txt?_=" + Math.random(), false);
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        window.data = public.toJsonObj(xhr.responseText)
    }
};
xhr.send(null);
console.log(data);
//2.绑定数据（ES6模板字符串）
;(function () {
    if (window.data) {
        var str1 = ``;
        var str2 = ``;
        for (var i = 0; i < data.length; i++) {
            str1 += `<div>
    <img src="" alt="" photo="${data[i].src}">
</div>
    `;
            //默认是第一个焦点有选中样式
            str2 += i === 0 ? `<li class="selected"></li>` : `<li></li>`;
        }
        //为了实现无缝滚动要在最后拼接第一张图片
        str1 += `<div><img src="" photo="${data[0].src}" alt=""></div>`;
        //bannerInner轮播区域具体有多宽是根据数据来定的，有几条数据宽度就是可视区域（banner的宽度）乘以几
        public.css(bannerInner, "width", (data.length + 1) * 800);
        bannerInner.innerHTML = str1;
        focusList.innerHTML = str2;
    }
})();

//3.图片的延迟加载
;(function () {
    for (var i = 0; i < imgList.length; i++) {
        var curImg = document.createElement("img");
        curImg.src = imgList[i].getAttribute("photo");
        curImg.i = i;
        curImg.onload = function () {
            imgList[this.i].src = this.src;
            animation(imgList[this.i], {opacity: 1}, 2000)
        }
    }
})();
//4.自动轮播
var step = 0;//当前是第一个图片
var timer = null;
var isOkClick = true;
timer = window.setInterval(autoMove, 1000);
function autoMove() {
    //临界值判断
    if (step === data.length) {
        step = 0;
        public.css(bannerInner, {left: -step * 800})
    }
    step++;
    animation(bannerInner, {left: -step * 800}, 1000, function () {
        isOkClick = true;
    });
    focusSelect();
    //animation里面的时间必须要小于轮播的时间
}

//5.实现焦点的跟随选中样式
function focusSelect() {
    //处理解决最后一张结束后第一张图片对应的li没有样式的问题
    //循环所有的li，判断当前li的索引是否等于当前轮播的图片，也就是step的值
    for (var i = 0; i < list.length; i++) {
        if (step === 4) {
            list[0].className = "selected";
        }
        list[i].className = i === step ? "selected" : "";
    }
}

//6.鼠标滑过停止轮播
banner.onmouseover = function () {
    //关掉定时器
    window.clearInterval(timer);
    //让按钮显示
    public.css(leftBtn, "display", "block");
    public.css(rightBtn, "display", "block");
};
//7.鼠标移开开启轮播
banner.onmouseout = function () {
    //上边的定时器怎么写的这里就怎么写
    timer = window.setInterval(autoMove, 2000);
    public.css(leftBtn, "display", "none");
    public.css(rightBtn, "display", "none");
};
//8.给左右按钮绑定点击事件
//为了防止有人不断的点击，当前的动画还没执行完就要执行下一个，造成动画的累积完成越来越快
//默认开始你点击是有效果的，在动画执行完成之前你点击是没有效果的
rightBtn.onclick = function () {
    if (isOkClick) {
        isOkClick = false;
        autoMove();
    }
};
leftBtn.onclick = function () {
    //临界值判断
    if (isOkClick) {
        isOkClick = false;

    if (step === 0) {
        step = data.length;
        public.css(bannerInner, {left: -step * 800});
    }
        step--;
        animation(bannerInner, {left: -step * 800}, 1000, function () {
            isOkClick = true
        });
        focusSelect();
    }
};
//9.给焦点绑定时间
(function () {
    for (var i = 0; i < list.length; i++) {
        //把索引i存起来
        list[i].i = i;
        list[i].onclick = function () {
            step = this.i;
            animation(bannerInner, {left: -step * 800}, 1000);
            focusSelect();
        }
    }
})();

```