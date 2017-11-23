# [SpringBoot+MyBatis让日志输出SQL的方法](http://118.89.232.188/pages/viewpage.action?pageId=1081370)

今天在调试Redis的时候想知道数据是从数据库中查询的，还是从Redis缓存中取的，因此想通过日志是否打印执行SQL来判断。但是默认日志是不输出MyBatis执行时的SQL文的，所以查阅资料看看怎么配置让日志能输出SQL，在此记录一下。

* 假如整个工程的包名为：

```
net.lemonsoft.coder_city
```

* 我们在Spring配置文件中进行配置。

如果你是使用`application.yml`进行全局配置，那么在文件中这么写：

```
logging:
  level:
    net:
      lemonsoft:
        coder_city: DEBUG
```

如果你是使用`application.properties`进行全局配置，那么在文件中这么写：

```
logging.level.net.lemonsoft.coder_city=DEBUG
```

在具体配置中，把logging.level后面的包名换成你实际项目中的包名即可。

**注意：千万不要忘了改成自己的controller包路径！**

