ThinkPHP Tips
===
> By Jayin Ton


模板自动映射
---
> added in 11.29

前段时间，做前端的妹纸他问在ThinkPHP中访问她新写的html模板，当时我就觉得蛋疼。毕竟她不会php，在ThinkPHP的中
调用一个模板，一般是在Controller中调用`$this->display()`，那么问题来了，前端妹纸得手动在Controller中加入php代码。
有无更方便得方式？

后来看了Controller的源码，原来这样
```php
    /**
     * 魔术方法 有不存在的操作的时候执行
     * @access public
     * @param string $method 方法名
     * @param array $args 参数
     * @return mixed
     */
    public function __call($method,$args) {
        if( 0 === strcasecmp($method,ACTION_NAME.C('ACTION_SUFFIX'))) {
            if(method_exists($this,'_empty')) {
                // 如果定义了_empty操作 则调用
                $this->_empty($method,$args);
            }elseif(file_exists_case($this->view->parseTemplate())){
                // 检查是否存在默认模版 如果有直接输出模版
                $this->display();
            }else{
                E(L('_ERROR_ACTION_').':'.ACTION_NAME);
            }
        }else{
            E(__CLASS__.':'.$method.L('_METHOD_NOT_EXIST_'));
            return;
        }
    }
```

那么懂了，如果Controller中没有调用`$this->display()`，访问`~/module/controller/new_design`就会自动显示对应控制器下模板`new_design.html`(ps：我的模板后缀是html)


12.3 update:

貌似昨日，按照上面的做法，还是无法调用默认的模板，那么。请你检查你是否在Controller中复写了写_empty()。
如果是的话，补刀：
```php
public function _empty(){
    if(file_exists_case($this->view->parseTemplate())){
        // 检查是否存在默认模版 如果有直接输出模版
        $this->display();
    }else{
      // your logic code
    }
}

```
