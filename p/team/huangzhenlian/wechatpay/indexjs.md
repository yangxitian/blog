## 这里是微信支付默认调用

#### 一：以下是微信php 接口参数的生成
```php
$commonUtil = new CommonUtil();  //微信支付而外功能实现类
$wxPayHelper = new WxPayHelper(); //微信支付所需参数生成以及加密类



$wxPayHelper->setParameter("bank_type", "WX"); //
$wxPayHelper->setParameter("body", $body);//商品描述
$wxPayHelper->setParameter("partner", "1220140801");//注册时分配的财付通商号
$wxPayHelper->setParameter("out_trade_no", $getout_trade_no()); //商家内部的订单号
$wxPayHelper->setParameter("total_fee",$getprice());//商品价格
$wxPayHelper->setParameter("fee_type", "1");//支付币种，默认1人民币
$wxPayHelper->setParameter("notify_url", "http://notify_url.php"); //步返回通知页面
$wxPayHelper->setParameter("spbill_create_ip",$ip);//获取用户IP地址
$wxPayHelper->setParameter("input_charset", "UTF-8"); //传入字符串编码

//测试数据的输出   输出获得微信接口需要的json
$jj=$wxPayHelper->create_biz_package();


```
### PS：
**1：以上是微信支付中，必要的全部字段，还有一些非必要字段可以在文档中选择使用**  
**2：更需要注意的是以上所有生成的参数必须是字符串类型**  
**3：还有body（商品描述）中，我们可能用到中文的空格符号，在urlencode转码的时候空格的转码应该为%20 不可以是+**   

    
#### 二：下面是就是微信js 调用

```js

function callpay()
{
	WeixinJSBridge.invoke('getBrandWCPayRequest',<?php echo $jj; ?>,function(res){
	WeixinJSBridge.log(res.err_msg);
	alert(res.err_code+res.err_desc+res.err_msg);
	});
}

```
**1：WeixinJSBridge.invoke 三个参数分别是：调用方法，订单数据，以及回调函数**  
**2：err_msg有一下几个参数结果 ：**  
**      get_brand_wcpat_request:cancel，用户取消**  
**      get_brand_wcpat_request:fail，发送失败（可能参数有问题，加密失败，可以调用系统调试接口）**  
**      get_brand_wcpat_request:ok ，支付成功**




