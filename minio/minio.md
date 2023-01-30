# Minio使用

## 1、Docker单机启动

```shell
mkdir -p ~/minio/data

docker run \
  -p 9000:9000 \
  -p 9001:9001 \
  -d \
  --name minio \
  -v ~/minio/data:/data \
  -e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  quay.io/minio/minio server /data --console-address ":9001"


```

## 2、Minio的使用

访问`http://192.168.33.10:9001/`可以进入后台

设置存储桶的访问权限

## 3、Minio的api文档

`https://minio-java.min.io/io/minio/MinioClient.html`

### 1、获取minio客户端

```java
        MinioClient minioClient = MinioClient.builder()
                .endpoint("http://192.168.33.10:9000")
                .credentials("ctw123","ctw20010705")
                .build();

        MinioClient minioClient = MinioClient.builder()
                .endpoint("http://192.168.33.10:9000",9000,false)
                .credentials("ctw123","ctw20010705")
                .build();
```

### 2、桶操作

#### bucketExists(BucketExistsArgs args)

`public boolean bucketExists(BucketExistsArgs args)`

检查存储桶是否存在。

```java
        //单个存储桶
        BucketExistsArgs args = BucketExistsArgs.builder().bucket("amiya").build();
        System.out.println(minioClient.bucketExists(args));

        //多个存储桶
```

#### listBuckets()

`public List<Bucket> listBuckets()`

列出所有桶的桶信息.

```java
List<Bucket> bucketList = minioClient.listBuckets();
```

#### listObjects(ListObjectsArgs args)

`public Iterable<Result<Item>> listObjects(ListObjectsArgs args)`

列出桶的对象信息

#### removeBucket(RemoveBucketArgs args)

`public void removeBucket(RemoveBucketArgs args)`

删除一个空桶。

#### makeBucket(MakeBucketArgs args)

`public void makeBucket(MakeBucketArgs args)`

```java

```


