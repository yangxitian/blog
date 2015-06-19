模型自动验证，自动完成
===
> By Jayin Ton
> 2014.11.20

首先我假设你已经会使用自动验证和自动完成，下文主要针对源码的分析.

灵活运用ThinkPHP的自动验证与自动完成，会让你觉得：啊~好爽

定义：
```php
/* 自动验证规则 */
protected $_validate = array(
);

/* 自动完成规则 */
protected $_auto = array();
```

执行`$model->create($data);` 就要自动执行上述规则

那么问题来了，**到底是先执行自动验证还是先执行自动完成呢？**

读了源码，发现执行顺序是:
`数据自动验证->数据进行自动完成`

### 实际应用场景1.
需求：我要更新一个会议记录的数据，创建一会议的时候，这个会议已经是归属于公司c_id，更新的时候不能更新所属公司c_id这个字段,不然的话就坑了，有测试人员错误调用，把c_id这个字段更新，那就惨了，数据错乱!

简而言之，需求就是:更新的时候不能(禁止)更新一些字段 

那么容易写出下面代码：
```php
class XxxModel extends Model{
    public function update($data){
        //过滤掉c_id
        $model->field('c_id',true)->create($data)->save();
    }
}
```

好了，这样可以解决我们的需求了。

但是，如果维护的人忘记这么过滤这个字段，那么坑爹了，而且这些代码只写在一个函数里，也就是写在业务层，能不能像自动完成/自动验证那样定义更酷炫，更清晰明了容易维护？

这里不得不提出一个ThinkPHP的高级模型--AdvModel 中提供的`只读字段`

```
//继承高级模型
class FooModel extends AdvModel{
    //定义只读字段(不允许被更改，save之前过滤一些字段)
    protected $readonlyField = array('uid', 'c_id');
}

```

这样，save()之前会自动过滤到只读字段的值，避免更新到数据库
