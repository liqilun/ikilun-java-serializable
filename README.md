在dubbo RPC中，同时支持多种序列化方式，例如：

dubbo序列化：阿里尚未开发成熟的高效java序列化实现，阿里不建议在生产环境使用它
hessian2序列化：hessian是一种跨语言的高效二进制序列化方式。但这里实际不是原生的hessian2序列化，而是阿里修改过的hessian lite，它是dubbo RPC默认启用的序列化方式
json序列化：目前有两种实现，一种是采用的阿里的fastjson库，另一种是采用dubbo中自己实现的简单json库，但其实现都不是特别成熟，而且json这种文本序列化性能一般不如上面两种二进制序列化。
java序列化：主要是采用JDK自带的Java序列化实现，性能很不理想。


memcache自身序列化问题，

#PS 这个问题可参考:
http://chenzhou123520.iteye.com/blog/1946798