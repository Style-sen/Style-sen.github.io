# 微服务架构-4-OSS对象存储服务(文件服务)


参考[微服务之分布式文件系统](https://www.cnblogs.com/heishao/p/10371363.html)

搭建独立的文件服务是解决文件共享、释放业务系统压力的最优选择。于是便诞生了分布式文件系统简称OSS(Object Storage Service)，提供的海量、安全、低成本、高可靠的云存储服务。它具有与平台无关的RESTful API接口，能够提供数据可靠性和服务可用性。

## 意义

1. 降低WEB服务器压力
2. 独立服务易扩展
3. 统一访问格式
4. 安全认证

## 服务选择

[Self Hosted File Sharing and Synchronization](https://selfhosted.libhunt.com/categories/1685-file-sharing-and-synchronization)

### [Minio](https://min.io/)

1. Minio是Apache License v2.0下发布的对象存储服务器。它与Amazon S3云存储服务兼容。`它最适合存储非结构化数据`，如照片，视频，日志文件，备份和容器/ VM映像。对象的大小可以从几KB到最大5TB。
2. Minio服务器足够轻，可以与应用程序堆栈捆绑在一起，类似于NodeJS，Redis和MySQL。
3. 因为与Amazon S3云存储服务兼容，所以可以被很多框架访问。

#### 原理

#### 存储机制

1. Minio使用纠删码erasure code和校验和checksum来保护数据免受硬件故障和无声数据损坏。 即便丢失一半数量（N/2）的硬盘，仍然可以恢复数据。

##### 纠删码

1. 纠删码是一种恢复丢失和损坏数据的数学算法，目前，纠删码技术在分布式存储系统中的应用主要有三类，阵列纠删码（Array Code: RAID5、RAID6等）、RS(Reed-Solomon)里德-所罗门类纠删码和LDPC(LowDensity Parity Check Code)低密度奇偶校验纠删码。
2. Erasure Code是一种编码技术，它可以将n份原始数据，增加m份数据，并能通过n+m份中的任意n份数据，还原为原始数据。即如果有任意小于等于m份的数据失效，仍然能通过剩下的数据还原出来。

#### 部署

##### 单点模式

```
docker run -p 9000:9000 --name minio1 \
  -v /mnt/data:/data \
  -v /mnt/config:/root/.minio \
  minio/minio server /data
```

##### 集群模式

1. 参考[Docker Compose创建minio集群](https://www.zengxi.net/articles/2018/docker-mino-cluster/);
2. 分布式的Minio服务至少需要4个节点，所以在docker-compose.yml文件中，至少要配置4个服务。每个服务的command配置必须一样，保证集群环境正常运行。最多共16个。
3. 添加一个服务：
    a. 复制服务定义并适当地更改新服务的名称。
    b. 更新每个服务中的命令部分。
    c. 更新要为新服务公开的端口号。 另外，请确保分配给新服务的端口尚未使用。
4. 创建集群之后，使用负载均衡。

#### 使用

##### mc客户端

##### JS API

###### 构造函数

```
var Minio = require('minio')

var minioClient = new Minio.Client({
    endPoint: 'play.min.io',
    port: 9000,
    useSSL: true,
    accessKey: 'Q3AM3UQ867SPQQA43P2F',
    secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'
});
```

###### 操作存储桶	

1. `makeBucket(名称，区域[,回调函数])`创建一个新的存储桶。*名称不能是路径*。

2. `listBuckets`列出所有存储桶。

3. `bucketExists`验证存储桶是否存在。

4. `removeBucket`删除存储桶。

5. `listObjects`列出存储桶中所有对象。

6. `listObjectsV2`使用S3 listing objects V2版本API列出所有对象。

7. `listIncompleteUploads`列出存储桶中未完整上传的对象。

###### 操作对象	

1. `getObject`下载对象。

2. `getPartialObject`下载对象中指定区间的字节数组，并返回流。

3. `fGetObject`下载并将对象保存成本地文件。

4. `putObject(bucketName, objectName, stream, size, metaData[, callback])`从一个stream/Buffer中上传一个对象。*bucketName相当于根目录，objectName可以包含相对根目录的路径，同名的objectName直接覆盖掉*

5. `fPutObject`上传文件。`会覆盖掉以前的同名文件，即更新`

6. `copyObject`将源对象拷贝到指定存储桶的新对象中。

7. `statObject`获取对象的元数据。

8. `removeObject`删除一个对象。

9. `removeIncompleteUpload`删除一个未完整上传的对象。

###### Presigned操作	

Presigned URLs用于对私有对象提供临时的上传/下载功能。

1. `presignedUrl`生成一个给指定HTTP方法（'httpMethod'）请求用的presigned URL。浏览器/移动端的客户端可以用这个URL进行下载，即使其所在的存储桶是私有的。这个presigned URL可以设置一个失效时间，默认值是7天。

2. `presignedGetObject`生成一个给HTTP GET请求用的presigned URL。浏览器/移动端的客户端可以用这个URL进行下载，即使其所在的存储桶是私有的。这个presigned URL可以设置一个失效时间，默认值是7天。

3. `presignedPutObject`生成一个给HTTP PUT请求用的presigned URL。浏览器/移动端的客户端可以用这个URL进行上传，即使其所在的存储桶是私有的。这个presigned URL可以设置一个失效时间，默认值是7天。

4. `presignedPostPolicy`允许给POST请求的presigned URL设置条件策略。比如接收上传的存储桶名称、名称前缀、过期策略。

###### 存储桶策略/通知

存储桶可以配置在指定事件类型和相应路径上触发通知。

1. `getBucketNotification`	获取指定存储桶名称的通知配置。

2. `setBucketNotification`	上传一个用户创建的通知配置，并绑定到指定的存储桶上。

3. `removeAllBucketNotification`	删除指定存储桶上的通知配置。

4. `getBucketPolicy`获取指定存储桶的访问策略，如果objectPrefix不为空，则会取相应对象前缀上的访问策略。

5. `setBucketPolicy`设置指定存储桶的策略。如果objectPrefix不为空，则会给符合该前缀的对象（们）设置策略。

6. `listenBucketNotification`	监听存储桶上的通知，可通过前缀、后缀、事件类型进行过滤。使用本API并不需要预先设置存储桶通知。这是Minio的一个扩展API，服务端基于过来的请求使用唯一ID自动注册或者取消注册。返回一个EventEmitter对象，它可以广播一个通知事件。停止监听，调用EventEmitter的stop()方法。

