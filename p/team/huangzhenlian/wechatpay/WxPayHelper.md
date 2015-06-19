##这里我们一起详细学习Helper这个功能类



####一：引入配置参数 
```php
include_once("CommonUtil.php");  
include_once("WxPay.config.php");  
include_once("SDKRuntimeException.class.php");  
include_once("MD5SignUtil.php");  
```

**上面是引入的一些配置文件，都是以一些微信那边认证后分配的参数**  


#### 二：下面是关于参数设置还有获取的函数

```php
function setParameter($parameter, $parameterValue) {
$this->parameters[CommonUtil::trimString($parameter)] = CommonUtil::trimString($parameterValue);  
//CommonUtil 是一个对应字符串操作的类，有机会后面我们可以研究一下
//从上面代码我们不难看出来，这就是参数存在到 parameters 数组中
}
function getParameter($parameter) {
return $this->parameters[$parameter];
}
```




#### 三；打包生成支付传输的参数
```php
function create_app_package($traceid=""){
//echo $this->create_noncestr();
try {
//var_dump($this->parameters);
if($this->check_cft_parameters() == false) {  // ①对于配置检测函数
throw new SDKRuntimeException("生成package参数缺失！" . "<br>");
}
$nativeObj["appid"] = APPID;
$nativeObj["package"] = $this->get_cft_package();  //②  生成订单参数的函数
$nativeObj["timestamp"] = time();
$nativeObj["traceid"] = $traceid;
$nativeObj["noncestr"] = $this->create_noncestr();  // noncestr 生成随机数
$nativeObj["app_signature"] = $this->get_biz_sign($nativeObj);  //执行sha1 加密函数
$nativeObj["sign_method"] = SIGNTYPE;
return json_encode($nativeObj);
}catch (SDKRuntimeException $e)
{
die($e->errorMessage());
}
}

```


**①：对配置检测函数  这个函数不用多介绍，就是检测必要的参数有没有赋值 **

```php

function check_cft_parameters(){
if($this->parameters["bank_type"] == null || $this->parameters["body"] == null || $this->parameters["partner"] == null ||
$this->parameters["out_trade_no"] == null || $this->parameters["total_fee"] == null || $this->parameters["fee_type"] == null ||
$this->parameters["notify_url"] == null || $this->parameters["spbill_create_ip"] == null || $this->parameters["input_charset"] == null
)
{
return false;
}
return true;
}

```


**②：生成订单参数**

![图片名](__IMG__/zhlhuang/11.jpg)

```php
protected function get_cft_package(){
try {
if (null == PARTNERKEY || "" == PARTNERKEY ) {
throw new SDKRuntimeException("密钥不能为空！" . "<br>");
}
$commonUtil = new CommonUtil();
ksort($this->parameters);
$unSignParaString = $commonUtil->formatQueryParaMap($this->parameters, false);
$paraString = $commonUtil->formatQueryParaMap($this->parameters, true);
$md5SignUtil = new MD5SignUtil();  //这里我们是用了一个微信提供给我的MD5加密方法 生成sign
//echo "----".strtoupper(md5($unSignParaString."&key=".PARTNERKEY));
return $paraString . "&sign=" . $md5SignUtil->sign($unSignParaString,$commonUtil->trimString(PARTNERKEY));
}catch (SDKRuntimeException $e)
{
die($e->errorMessage());
}
}
```

##### 该函数执行结果就会生成以下格式的Packer
```
	"package":"bank_type=WX&body=XXX&fee_type=1&input_charset=GBK&notify_url=http%3a%2f%2f
www.qq.com&out_trade_no=16642817866003386000&partner=1900000109&spbill_create_ip=127.0.0.1&total_fee=1&sign=BEEF37AD19575D92E191C1E4B1474CA9",
```






#### 下面是一个微信提供的sha1 加密函数，在后面微信支付开发中，我们会经常用到

```php
protected function get_biz_sign($bizObj){
foreach ($bizObj as $k => $v){
$bizParameters[strtolower($k)] = $v;
}   //统一将数组的键 名称改成小写 然后重新生成加密数组
try {
if(APPKEY == ""){
throw new SDKRuntimeException("APPKEY为空！" . "<br>");
}
$bizParameters["appkey"] = APPKEY;
ksort($bizParameters); 
//var_dump($bizParameters);
$commonUtil = new CommonUtil();
$bizString = $commonUtil->formatBizQueryParaMap($bizParameters, false);  //该函数是通过key 和value 拼接成字符串
//var_dump($bizString);
return sha1($bizString);  //拼接后的字符串 用sha1加密后返回
}catch (SDKRuntimeException $e)
{
die($e->errorMessage());
}
}
```


#### 四；经过上述几个函数的调用之后，我们可以返回到下面一串json
```

 {
	"appid":"wwwwb4f85f3a797777",
	"traceid":"crestxu",
	"noncestr":"111112222233333",
	"package":"bank_type=WX&body=XXX&fee_type=1&input_charset=GBK&notify_url=http%3a%2f%2f
	www.qq.com&out_trade_no=16642817866003386000&partner=1900000109&spbill_create_ip=127.0.0.1&total_fee=1&sign=BEEF37AD19575D92E191C1E4B1474CA9",
	"timestamp":1381405298,
	"app_signature":"53cca9d47b883bd4a5c85a9300df3da0cb48565c",
	"sign_method":"sha1"
}


```











