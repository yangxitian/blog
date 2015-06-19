#JQuery 的小Tips#

![jquery](__IMG__/wenjun/jquery.jpg)
- ###禁止右键点击###
####如果你想为用户节省网站信息，那么开发者可以使用这段代码——禁用右键单击功能。####

```shell
$(document).ready(function(){
    $(document).bind("contextmenu",function(e){
        return false;
    });
});
```

- ###隐藏搜索文本框文字###
####Hide when clicked in the search field, the value.####

```shell
$(document).ready(function() {
$("input.text1").val("Enter your search text here");
   textFill($('input.text1'));
});
  
    function textFill(input){ //input focus text function
    var originalvalue = input.val();
    input.focus( function(){
        if( $.trim(input.val()) == originalvalue ){ input.val(''); }
    });
    input.blur( function(){
        if( $.trim(input.val()) == '' ){ input.val(originalvalue); }
    });
}
```

- ###在新窗口中打开链接###

```shell
$(document).ready(function() {
   //Example 1: Every link will open in a new window
   $('a[href^="http://"]').attr("target", "_blank");
  
   //Example 2: Links with the rel="external" attribute will only open in a new window
   $('a[@rel$='external']').click(function(){
      this.target = "_blank";
   });
});
// how to use
<A href="http://www.opensourcehunter.com" rel=external>open link</A>
```

- ###预加载图片###

```shell
$(document).ready(function() {
jQuery.preloadImages = function()
{
  for(var i = 0; i<ARGUMENTS.LENGTH; jQuery(?<img { i++)>").attr("src", arguments[i]);
  }
}
// how to use
$.preloadImages("image1.jpg");
});
```

- ###返回页面顶部功能###
####For a smooth(animated) ride back to the top(or any location).####

```shell
$(document).ready(function() {
$('a[href*=#]').click(function() {
 if (location.pathname.replace(/^\//,'') == this.pathname.replace(/^\//,'')
 && location.hostname == this.hostname) {
   var $target = $(this.hash);
   $target = $target.length && $target
   || $('[name=' + this.hash.slice(1) +']');
   if ($target.length) {
  var targetOffset = $target.offset().top;
  $('html,body')
  .animate({scrollTop: targetOffset}, 900);
    return false;
   }
  }
  });
// how to use
// place this where you want to scroll to
<A name=top></A>
// the link
<A href="#top">go to top</A>
});
```

- ###获得鼠标指针ＸＹ值###

```shell
$(document).ready(function() {
   $().mousemove(function(e){
     //display the x and y axis values inside the div with the id XY
    $('#XY').html("X Axis : " + e.pageX + " | Y Axis " + e.pageY);
  });
// how to use
<DIV id=XY></DIV>
 
});
```

- ###验证元素是否为空###
####This will allow you to check if an element is empty.####

```shell
$(document).ready(function() {
  if ($('#id').html()) {
   // do something
   }
});
```

- ###使元素居屏幕中间位置###
####Center an element in the center of your screen.####

```shell
$(document).ready(function() {
  jQuery.fn.center = function () {
      this.css("position","absolute");
      this.css("top", ( $(window).height() - this.height() ) / 2+$(window).scrollTop() + "px");
      this.css("left", ( $(window).width() - this.width() ) / 2+$(window).scrollLeft() + "px");
      return this;
  }
  $("#id").center();
});
```
