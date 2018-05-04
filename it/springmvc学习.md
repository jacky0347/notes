# spring 学习

## 1. 配置文件加载

另外在加载resource的时候,其他前缀的意义如下表所示:注意classpath*只能用与指定配置文件的路径,不能用在用于getResource的参数.如appContext.getResource("classpath*:conf/bfactoryCtx.xml")会异常的.

| 前缀         | 例子                               | 说明                            |
| ---------- | -------------------------------- | ----------------------------- |
| classpath: | `classpath:com/myapp/config.xml` | 从classpath中加载。                |
| file:      | `file:/data/config.xml`          | 作为 `URL` 从文件系统中加载。            |
| http:      | `http://myserver/logo.png`       | 作为 `URL` 加载。                  |
| (none)     | `/data/config.xml`               | 根据 `ApplicationContext` 进行判断。 |



addresses a problem