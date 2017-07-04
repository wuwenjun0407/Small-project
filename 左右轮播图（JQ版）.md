#左右轮播图（JQ版）

@(项目)

```
var $banner = $("#banner");
var $inner = $(".bannerInner");
var $focus = $(".focusList");
var $l = $("#banner a").eq(0);
var $t = $("#banner a").eq(1);
var step = 0, timer = 0, isOkClick = true, data = null;
var $imgList = null;
var $list = null;
$(function () {
    $.ajax({
        type: "get",
        url: "js/data.txt?_=" + Math.random(),
        dataType: "json",
        async: false,
        data: null,
        success: function (data) {
            window.data = data;
            //  console.log(data);
        },
        error: function () {
            console.log("error")
        }
    });
    if (data && data.length > 0) {
        var str1 = ``, str2 = ``;
        $.each(data, function (i, it) {
            // console.log(i);
            //  console.log(it);
            str1 += `<div><img src="" photo="${it.src}" alt=""/></div>`;
            str2 += i == 0 ? `<li class="selected"></li>` : `<li></li>`;
        });
        str1 += `<div><img src="" photo="${data[0].src}" alt=""/></div>`;
        $inner.css({width: (data.length + 1) * 800});
        $inner.html(str1);
        $focus.html(str2);
        $imgList = $inner.find("img");
        $list = $focus.children("li");
    };
    function delayLoad() {
        $imgList.each(function (i, it) {
            var $this = $(this);
            var curImg = new Image;
            curImg.src = $(it).attr("photo");
            curImg.onload = function () {
                $this.prop("src", this.src).stop().animate({opacity: 1}, 1000)
            }
        })
    }
    delayLoad();
    timer = window.setInterval(move, 2000);
    function move() {
        if(step===data.length){
            step=0;
            $inner.css({left:-step*800})
        }
        step++;
        if(step===data.length){
            $list.eq(0).addClass("selected").siblings().removeClass("selected");
        }else {$list.eq(step).addClass("selected").siblings().removeClass("selected");}
        $inner.animate({left:-step*800},1000,function () {
            isOkClick=true;
        });
    };
    $banner.mouseover(function () {
        window.clearInterval(timer);
        $l.css({display: "block"});
        $t.css({display: "block"});
    }).mouseout(function () {
        timer = window.setInterval(move, 2000);
        $l.css({display: "none"});
        $t.css({display: "none"});
    });
    $t.click(function () {
        if (isOkClick) {
            isOkClick = false;
            move()
        }
    });
    $l.click(function () {
        if (isOkClick) {
            isOkClick = false;
            if (step == 0) {
                step = data.length;
                $inner.css({left: -step * 800});
            }
            step--;
            $list.eq(step).addClass("selected").siblings().removeClass("selected");
            $inner.animate({left: -step * 800}, 1000, function () {
                isOkClick = true;
            });
        }
    });
    $list.click(function () {
        if(isOkClick){
            isOkClick=false;
            step=$(this).index();
            console.log($(this));
            //console.log($(this).index());
            $list.eq(step).addClass("selected").siblings().removeClass("selected");
            $inner.animate({left: -step * 800}, 1000, function () {
                isOkClick=true;
            });
        }
    })
});
```