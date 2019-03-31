---
title: PHP Standards Recommendations(PSR)
date: 2019-03-31 12:05:07
tags:
  - PSR
  - PHP
description: 粗略翻译PSR ACCEPTED
---

### PSR-1 代码规范
1. 使用php tag <?php ?> 或 <?= ?>
2. 使用UTF-8 无BOM
3. 文件内可以声明方法/类,可以写执行性脚本，但是不能同时存在
4. Namespace必须遵循（PSR-0，PSR-4）
5. Class 命名StudlyCaps
6. 类常量声明大写加下划线
7. 类方法声明camelCase

### PSR-2 代码规范
1. 代码必须遵循PSR-1
2. 代码缩进 4空格
3. 代码行长度没有强制性的限制，建议限制120c，最好是80c
4. namespace和use后要有空行
5. 类的左括号要换行，右括号要在类内容后空一行
6. 方法的左括号要换行，右括号要在方法内容后空一行
7. 变量和方法的可见性必须被声明，abstract和final声明在可见性变量之前，static声明在可见性变量之后
8. 控制语句的关键字后面要有空格，方法调用不需要
9. 控制语句的左括号不换行，右括号换行
10. 控制语句的左括号前要有空格，右括号前则没有空格

### PSR-3 Logger Interface
1. Logger Interface 暴露8个方法来记录8种级别(debug, info, notice, warning, error, critical, alert, emergency)的log
2. 第九个方法log，第一个参数接收log的级别，来调用任何一个级别的log，输出要同8种级别的log结果相同；第一个参数不能接收除了8种级别以外的类别，否则抛出异常
3. 每一个log方法接收一个string作为异常信息，或者含有toString的对象；异常信息中可以含有占位符，实现中要用相关数组中的值来替换

### PSR-4 Autoloader
此PSR描述了根据文件路径自动加载类，也描述了自动加载的文件要怎么放
1. 下文中的Class包括Class，Interface，Traits和其他类似的结构
2. 一个完整合规的Class应该是这样的格式 \<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>
   1. 必须有一个最高级别的命名空间，一般为vendor名
   2. 可以有一个或者多个子命名空间
   3. 必须包含最终的类名
   4. 下划线在类中没有任何意义
   5. 字母字符可以大小写任意组合(\open.org\Sub\HelloWorld)
   6. Class名是大小写敏感的
3. 当加载一个对应完整合规的Class的文件时
   1. 命名空间或者子命名空间必须至少对应一个目录
   2. 连续的子命名空间对应连续的子目录，分隔符对应目录分隔符
   3. Class名对应Class文件名，对应声明的Class名
4. 自动加载实现不能跑出异常，不能输出错误，也不能返回值
> [自动加载实现](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md)

### PSR-6 Caching Interface
> [Ref PSR-6](https://www.php-fig.org/psr/psr-6/)
##### 目标
这个PSR让开发者开发出的cache库能被已存在的框架和系统无缝接入，不需要再开发

##### 定义
1. 调用Cache库 - 调用cache库的方法或者类库只需要关注cache interface提供的方法，不需要关注具体的实现细节
2. 实现Cache库 - 为了给调用库提供Cache服务，Cache必须实现标准。Cache必须实现以下两个Interface，Cache\CacheItemPoolInterface和Cache\CacheItemInterface，TTL必须是秒级别的
3. TTL - Time To Live, Cache的有效时间
4. Expiration - Cache实际过期时间
5. Key - 一个确定一条Cache记录的唯一关键字
6. Hit - 能通过key访问到对应的cache记录并且记录没过期
7. Miss - 通过key访问的cache记录不存在或者已过期
8. Deferred(延迟) - Cache记录存储有时并不是实时的，为了批量化操作。要保证延迟保存的条目被保存进去。外部库调用commit的时候必须将所有延迟项保存到库里面。实现Cache的库要想办法实现什么时候保存延迟项。请求一个被延迟了的item，必须由延迟项返回，而不是从新访问

##### 数据
实现的Cache库必须能保存所有类型的php数据 Strings/ Integers/ Floats/ Boolean/ Null/ Array/ Object
返回数据要与存入数据完全匹配

##### 关键词汇
1. Pool 保存所有Cache Item的地方
2. Items 所有的item都以key/value形式存在，key不能变，value可变

##### 错误处理
不能抛出异常，要内部进行处理

##### Interface

CacheItemInterface defines an interface for interacting with objects inside a cache.
``` CacheItemInterface
<?php

namespace Psr\Cache;

/**
 * CacheItemInterface defines an interface for interacting with objects inside a cache.
 */
interface CacheItemInterface
{
    public function getKey();
    public function get();
    public function isHit();
    public function set($value);
    public function expiresAt($expiration);
    public function expiresAfter($time);

}
```

CacheItemPoolInterface generates CacheItemInterface objects

``` CacheItemPoolInterface
<?php

namespace Psr\Cache;

/**
 * CacheItemPoolInterface generates CacheItemInterface objects.
 */
interface CacheItemPoolInterface
{
    public function getItem($key);
    public function getItems(array $keys = array());
    public function hasItem($key);
    public function clear();
    public function deleteItem($key);
    public function deleteItems(array $keys);
    public function save(CacheItemInterface $item);
    public function saveDeferred(CacheItemInterface $item);
    public function commit();
}
```

### PSR-7 HTTP message interfaces
1. Psr\Http\Message\MessageInterface
```
<?php
namespace Psr\Http\Message;

/**
 * RequestInterface 和 ResponseInterface 的基类
 */
interface MessageInterface
{
    public function getProtocolVersion();
    // Return updated version.
    public function withProtocolVersion($version);
    public function getHeaders();
    public function hasHeader($name);
    public function getHeader($name);
    public function getHeaderLine($name);
    public function withHeader($name, $value);
    public function withAddedHeader($name, $value);
    // Return header without header item
    public function withoutHeader($name);
    // Return StreamInterface
    public function getBody();
    public function withBody(StreamInterface $body);
}
```
2. Psr\Http\Message\RequestInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Representation of an outgoing, client-side request.
 * 服务器端创建的请求类
 */
interface RequestInterface extends MessageInterface
{
    // 大多数情况返回URL<string>
    public function getRequestTarget();
    public function withRequestTarget($requestTarget);
    public function getMethod();
    public function withMethod($method);
    // Return UriInterface
    public function getUri();
    // Returns an UriInterface instance with the provided URI.
    public function withUri(UriInterface $uri, $preserveHost = false);
}
```
3. Psr\Http\Message\ServerRequestInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Representation of an incoming, server-side HTTP request.
 * 客户端请求在服务器端生成的请求类
 */
interface ServerRequestInterface extends RequestInterface
{
    // Return $_SERVER 但不一定要相同
    public function getServerParams();
    // Return $_COOKIE
    public function getCookieParams();
    public function withCookieParams(array $cookies);
    public function getQueryParams();
    public function withQueryParams(array $query);
    // Return array An array tree of UploadedFileInterface instances
    public function getUploadedFiles();
    public function withUploadedFiles(array $uploadedFiles);

    /**
     * Retrieve any parameters provided in the request body.
     *
     * If the request Content-Type is either application/x-www-form-urlencoded
     * or multipart/form-data, and the request method is POST, this method MUST
     * return the contents of $_POST.
     *
     * Otherwise, this method may return any results of deserializing
     * the request body content; as parsing returns structured content, the
     * potential types MUST be arrays or objects only. A null value indicates
     * the absence of body content.
     *
     * @return null|array|object The deserialized body parameters, if any.
     *     These will typically be an array or object.
     */
    public function getParsedBody();

    /**
     * Return an instance with the specified body parameters.
     *
     * These MAY be injected during instantiation.
     *
     * If the request Content-Type is either application/x-www-form-urlencoded
     * or multipart/form-data, and the request method is POST, use this method
     * ONLY to inject the contents of $_POST.
     *
     * The data IS NOT REQUIRED to come from $_POST, but MUST be the results of
     * deserializing the request body content. Deserialization/parsing returns
     * structured data, and, as such, this method ONLY accepts arrays or objects,
     * or a null value if nothing was available to parse.
     *
     * As an example, if content negotiation determines that the request data
     * is a JSON payload, this method could be used to create a request
     * instance with the deserialized parameters.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated body parameters.
     *
     * @param null|array|object $data The deserialized body data. This will
     *     typically be in an array or object.
     * @return static
     * @throws \InvalidArgumentException if an unsupported argument type is
     *     provided.
     */
    public function withParsedBody($data);
    // 获取从Request变量派生出来的一些变量 即经过处理或合成的Request变量存储在attribute中
    public function getAttributes();
    public function getAttribute($name, $default = null);
    public function withAttribute($name, $value);
    public function withoutAttribute($name);
}
```
4. Psr\Http\Message\ResponseInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Representation of an outgoing, server-side response.
 */
interface ResponseInterface extends MessageInterface
{
    public function getStatusCode();
    public function withStatus($code, $reasonPhrase = '');
    public function getReasonPhrase();
}
```
4. Psr\Http\Message\StreamInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Describes a data stream.
 */
interface StreamInterface
{
    /**
     * Reads all data from the stream into a string, from the beginning to end.
     */
    public function __toString();

    /**
     * Closes the stream and any underlying resources.
     *
     * @return void
     */
    public function close();

    /**
     * Separates any underlying resources from the stream.
     *
     * After the stream has been detached, the stream is in an unusable state.
     *
     * @return resource|null Underlying PHP stream, if any
     */
    public function detach();

    public function getSize();

    /**
     * Returns the current position of the file read/write pointer
     *
     * @return int Position of the file pointer
     * @throws \RuntimeException on error.
     */
    public function tell();

    /**
     * Returns true if the stream is at the end of the stream.
     *
     * @return bool
     */
    public function eof();

    /**
     * Returns whether or not the stream is seekable.
     *
     * @return bool
     */
    public function isSeekable();

    /**
     * Seek to a position in the stream.
     *
     * @see http://www.php.net/manual/en/function.fseek.php
     * @param int $offset Stream offset
     * @param int $whence Specifies how the cursor position will be calculated
     *     based on the seek offset. Valid values are identical to the built-in
     *     PHP $whence values for `fseek()`.  SEEK_SET: Set position equal to
     *     offset bytes SEEK_CUR: Set position to current location plus offset
     *     SEEK_END: Set position to end-of-stream plus offset.
     * @throws \RuntimeException on failure.
     */
    public function seek($offset, $whence = SEEK_SET);

    /**
     * Seek to the beginning of the stream.
     *
     * If the stream is not seekable, this method will raise an exception;
     * otherwise, it will perform a seek(0).
     *
     * @see seek()
     * @see http://www.php.net/manual/en/function.fseek.php
     * @throws \RuntimeException on failure.
     */
    public function rewind();

    /**
     * Returns whether or not the stream is writable.
     *
     * @return bool
     */
    public function isWritable();

    /**
     * Write data to the stream.
     *
     * @param string $string The string that is to be written.
     * @return int Returns the number of bytes written to the stream.
     * @throws \RuntimeException on failure.
     */
    public function write($string);

    /**
     * Returns whether or not the stream is readable.
     *
     * @return bool
     */
    public function isReadable();

    /**
     * Read data from the stream.
     *
     * @param int $length Read up to $length bytes from the object and return
     *     them. Fewer than $length bytes may be returned if underlying stream
     *     call returns fewer bytes.
     * @return string Returns the data read from the stream, or an empty string
     *     if no bytes are available.
     * @throws \RuntimeException if an error occurs.
     */
    public function read($length);

    /**
     * Returns the remaining contents in a string
     *
     * @return string
     * @throws \RuntimeException if unable to read.
     * @throws \RuntimeException if error occurs while reading.
     */
    public function getContents();

    /**
     * Get stream metadata as an associative array or retrieve a specific key.
     *
     * The keys returned are identical to the keys returned from PHP's
     * stream_get_meta_data() function.
     *
     * @see http://php.net/manual/en/function.stream-get-meta-data.php
     * @param string $key Specific metadata to retrieve.
     * @return array|mixed|null Returns an associative array if no key is
     *     provided. Returns a specific key value if a key is provided and the
     *     value is found, or null if the key is not found.
     */
    public function getMetadata($key = null);
}
```
5. Psr\Http\Message\UriInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Value object representing a URI.
 */
interface UriInterface
{
    // 协议
    public function getScheme();
    // [user-info@]host[:port]
    public function getAuthority();
    // user-info
    public function getUserInfo();
    public function getHost();
    public function getPort();
    // 为空的时候需要用户处理
    public function getPath();
    public function getQuery();
    // #后的值
    public function getFragment();
    public function withScheme($scheme);
    public function withUserInfo($user, $password = null);
    public function withHost($host);
    public function withPort($port);
    public function withPath($path);
    public function withQuery($query);
    public function withFragment($fragment);
    public function __toString();
}
```
6. Psr\Http\Message\UploadedFileInterface
```
<?php
namespace Psr\Http\Message;

/**
 * Value object representing a file uploaded through an HTTP request.
 */
interface UploadedFileInterface
{
    /**
     * Retrieve a stream representing the uploaded file.
     *
     * This method MUST return a StreamInterface instance, representing the
     * uploaded file. The purpose of this method is to allow utilizing native PHP
     * stream functionality to manipulate the file upload, such as
     * stream_copy_to_stream() (though the result will need to be decorated in a
     * native PHP stream wrapper to work with such functions).
     *
     * If the moveTo() method has been called previously, this method MUST raise
     * an exception.
     *
     * @return StreamInterface Stream representation of the uploaded file.
     * @throws \RuntimeException in cases when no stream is available.
     * @throws \RuntimeException in cases when no stream can be created.
     */
    public function getStream();

    /**
     * Move the uploaded file to a new location.
     *
     * Use this method as an alternative to move_uploaded_file(). This method is
     * guaranteed to work in both SAPI and non-SAPI environments.
     * Implementations must determine which environment they are in, and use the
     * appropriate method (move_uploaded_file(), rename(), or a stream
     * operation) to perform the operation.
     *
     * $targetPath may be an absolute path, or a relative path. If it is a
     * relative path, resolution should be the same as used by PHP's rename()
     * function.
     *
     * The original file or stream MUST be removed on completion.
     *
     * If this method is called more than once, any subsequent calls MUST raise
     * an exception.
     *
     * When used in an SAPI environment where $_FILES is populated, when writing
     * files via moveTo(), is_uploaded_file() and move_uploaded_file() SHOULD be
     * used to ensure permissions and upload status are verified correctly.
     *
     * If you wish to move to a stream, use getStream(), as SAPI operations
     * cannot guarantee writing to stream destinations.
     *
     * @see http://php.net/is_uploaded_file
     * @see http://php.net/move_uploaded_file
     * @param string $targetPath Path to which to move the uploaded file.
     * @throws \InvalidArgumentException if the $targetPath specified is invalid.
     * @throws \RuntimeException on any error during the move operation.
     * @throws \RuntimeException on the second or subsequent call to the method.
     */
    public function moveTo($targetPath);

    /**
     * Retrieve the file size.
     *
     * Implementations SHOULD return the value stored in the "size" key of
     * the file in the $_FILES array if available, as PHP calculates this based
     * on the actual size transmitted.
     *
     * @return int|null The file size in bytes or null if unknown.
     */
    public function getSize();

    /**
     * Retrieve the error associated with the uploaded file.
     *
     * The return value MUST be one of PHP's UPLOAD_ERR_XXX constants.
     *
     * If the file was uploaded successfully, this method MUST return
     * UPLOAD_ERR_OK.
     *
     * Implementations SHOULD return the value stored in the "error" key of
     * the file in the $_FILES array.
     *
     * @see http://php.net/manual/en/features.file-upload.errors.php
     * @return int One of PHP's UPLOAD_ERR_XXX constants.
     */
    public function getError();

    /**
     * Retrieve the filename sent by the client.
     *
     * Do not trust the value returned by this method. A client could send
     * a malicious filename with the intention to corrupt or hack your
     * application.
     *
     * Implementations SHOULD return the value stored in the "name" key of
     * the file in the $_FILES array.
     *
     * @return string|null The filename sent by the client or null if none
     *     was provided.
     */
    public function getClientFilename();

    /**
     * Retrieve the media type sent by the client.
     *
     * Do not trust the value returned by this method. A client could send
     * a malicious media type with the intention to corrupt or hack your
     * application.
     *
     * Implementations SHOULD return the value stored in the "type" key of
     * the file in the $_FILES array.
     *
     * @return string|null The media type sent by the client or null if none
     *     was provided.
     */
    public function getClientMediaType();
}
```