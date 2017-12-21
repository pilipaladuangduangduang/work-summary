# Redis工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Redis相关的内容：

### 使用Redis时需要sleep

 > 每次请求使用Redis服务时，都要sleep()，防止并发量过高时把Redis给搞垮。

### Redis序列化器

 > 在使用Jedis操作redis进行数据缓存的时候，发现总是需要将redis的key或value进行序列化，然后取出来的时候进行反序列化，建议实现自己的序列化器，而不是用redis默认使用的序列化器，然后存储数据、查询数据全都重写~

### Redis中ZSet结构使用小结

 > redis中为我们提供了可以排序的数据结构ZSet，ZSet中每一条数据都会有一个score用于比较排序；而且ZSet还提供分页获取数据的方法；除此之外ZSet还提供取并集和取交集的API~

### Redis中HashMap结构使用小结

 > redis中为我们提供了HashMap的数据结构，这样一来我们只要将对象的主键ID作为redis的Key，对应的Map值就可以封装对象的属性，而且支持修改Map中的单个Key的值~


