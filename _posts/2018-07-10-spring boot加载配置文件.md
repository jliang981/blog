#### spring boot加载jar包中的配置文件

#####

参考博客：[](https://blog.csdn.net/gwd1154978352/article/details/79122863)

```
@Configuration
@ImportResource(locations= {"classpath:application-bean.xml"})
public class Config {

}
```

但是这种只能因为本项目中的文件，如果配置文件在jar中，就会报错。fileNotFound的异常。

把文件路径换成这样就可以了。classpath*:spring-config.xml。多加一个*号。还可以使用通配符。

[spring加载多个配置文件](https://www.cnblogs.com/GarfieldTom/p/3723915.html)

##### @ImportResource @Import
@Configuration 类似于xml里面的beans标签
@ImportResource 类似于 <import resource="application.xml" />
@Import 是导入一个java配置类。