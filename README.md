为什么要实现序列化接口

   当一个类实现了Serializable接口(该接口仅为标记接口,不包含任何方法定义),表示该类可以序列化.序列化的目的是将一个实现了Serializable接口的对象转换成一个字节序列,可以。 把该字节序列保存起来(例如:保存在一个文件里),以后可以随时将该字节序列恢复为原来的对象。甚至可以将该字节序列放到其他计算机上或者通过网络传输到其他计算机上恢复，只要该计 算机平台存在相应的类就可以正常恢复为原来的对象。 实现：要序列化一个对象，先要创建某些OutputStream对象，然后将其封装在一个ObjectOutputStream对象内，再调用writeObject()方法即可序列化一个对象；反序列化也类似。
注意：使用对象流写入到文件是不仅要保证该对象是序列化的，而且该对象的成员对象也必须是序列化的

关于Serializable接口的类中的serialVersionUID:
serialVersionUID是long类型的。在Eclipse中有两种生成方式：
默认的是1L：
private static final long serialVersionUID = 1L;
另外一个则是根据类名、接口名、成员方法以及属性等生成一个64位的哈希字段：
private static final long serialVersionUID = 3969438177161438988L;
serialVersionUID主要是为了解决对象反序列化的兼容性问题。
如果没有提供serialVersionUID，对象序列化后存到硬盘上之后，再增加或减少类的filed。这样，当反序列化时，就会出现Exception，造成不兼容问题。
但当serialVersionUID相同时，它就会将不一样的field以type的缺省值反序列化。这样就可以避开不兼容问题了。


在dubbo RPC中，同时支持多种序列化方式，例如：

dubbo序列化：阿里尚未开发成熟的高效java序列化实现，阿里不建议在生产环境使用它
hessian2序列化：hessian是一种跨语言的高效二进制序列化方式。但这里实际不是原生的hessian2序列化，而是阿里修改过的hessian lite，它是dubbo RPC默认启用的序列化方式
json序列化：目前有两种实现，一种是采用的阿里的fastjson库，另一种是采用dubbo中自己实现的简单json库，但其实现都不是特别成熟，而且json这种文本序列化性能一般不如上面两种二进制序列化。
java序列化：主要是采用JDK自带的Java序列化实现，性能很不理想。


memcache自身序列化问题，

#PS 这个问题可参考:
http://chenzhou123520.iteye.com/blog/1946798


redis不支持直接将java对象存储到数据库中，所以需要将java对象进行序列化得到字节数组，然后将字节数组存入到redis中，
需要数据的时候就从redis数据库中取出字节数组，再经过反序列化将自己数组转换成对象使用（jdk序列化性能比谷歌公司的Protobuf序列化性能要差一些，而且序列化后的字节长度要也会长一些，所以推荐使用Protobuf

可以对redis序列化自己定义：
Object类型的value使用HessianSerializer,其对应的key使用string serializer
redisTemplate.setKeySerializer(new StringRedisSerializer());
redisTemplate.setValueSerializer(new HessianRedisSerializer<>());
redisTemplate.setHashKeySerializer(new StringRedisSerializer());
redisTemplate.setHashValueSerializer(new HessianRedisSerializer<>());