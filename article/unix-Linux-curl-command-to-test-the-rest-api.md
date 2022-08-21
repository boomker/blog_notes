# Unix/Linux Curl命令测试Rest API

在`Unix/Linux`系统中有这样一个命令，他类似`Python`中的`requests`模块，而且它是系统自带的，不需要安装，它就是`curl`。

它是一个非常强大的命令，你可以使用它下载文件，还可以访问http/https，它的参数非常的多，当然，在本篇文章中，我们只需要知道它关于HTTP的使用。

## 参数

下面的参数是测试API时，最常用的几个

|参数|描述|
|:--|:--|
|`-X/--request`|指定HTTP使用的METHOD|
|`-H/--header`|设置request里header|
|`-d/--data`|设置http parameters|

- **-X/--request**

Rest API最经典的就是增`(POST)删(DELETE)改(PUT)查(GET)`四个METHOD

```bash
$ curl -X GET http://localhost/api/user/
$ curl -X POST http://localhost/user/
$ curl -X PUT http://localhost/user/
$ curl -X DELETE http://localhost/user/
```

如果你没有指定`-X`参数，那个默认的就是`-X GET`

- **-H/--header**

在访问某些需要认证的API时，往往我们需要在请求头中添加`Token`

```bash
$ curl -H "Authorization: Token 379b8e8cafa684d0eaed38d95f227ebf7208de1a" http://localhost:8080/api/user/
{"id":1,"last_login":"2017-12-13T07:50:06.176290Z","is_superuser":true,"username":"admin","is_staff":true,"is_active":true,"date_joined":"2017-07-26T13:23:34Z","name":"安生","update":"2017-12-13T07:56:32.855788Z"}
```

- **-d/--data**

当我们在修改或者新增一些数据的时候，需要传入一些参数

```bash
$ curl -X PUT -H "Authorization: Token 379b8e8cafa684d0eaed38d95f227ebf7208de1a" -d "name=安安生&username=anansheng" http://localhost/api/user/
```

如果你想传入`JSON`数据，那么可以这样做

```bash
$ curl -X PUT -H "Content-Type:application/json" -H "Authorization: Token 379b8e8cafa684d0eaed38d95f227ebf7208de1a" -d '{"name":"安安生","username":"anansheng"}' http://localhost/api/user/
```

如果你使用的是GET，直接在URL里面传递参数就可以了

```bash
$ curl -H "Authorization: Token 379b8e8cafa684d0eaed38d95f227ebf7208de1a" http://localhost/api/users/?limit=10&page=1
```

## 参考

- [使用curl指令測試REST服務](http://blog.kent-chiu.com/2013/08/14/testing-rest-with-curl-command.html)
