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
**Psr\Http\Message\MessageInterface**
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
**Psr\Http\Message\RequestInterface**
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
**Psr\Http\Message\ServerRequestInterface**
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
**Psr\Http\Message\ResponseInterface**
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
**Psr\Http\Message\StreamInterface**
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
**Psr\Http\Message\UriInterface**
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
**Psr\Http\Message\UploadedFileInterface**
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

### PSR-11 Container Interface
标准化库如何使用container获取对象
Container是创建对象的容器，用户可以通过container拿到需要创建的对象，而不必关心创建过程，例如拿到一个service
使用容器获取的对象必须有一个唯一的关键字
Container Interface的实现对象必须暴露两个方法，get和have
```
<?php
namespace Psr\Container;

/**
 * Describes the interface of a container that exposes methods to read its entries.
 */
interface ContainerInterface
{
    /**
     * Finds an entry of the container by its identifier and returns it.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @throws NotFoundExceptionInterface  No entry was found for **this** identifier.
     * @throws ContainerExceptionInterface Error while retrieving the entry.
     *
     * @return mixed Entry.
     */
    public function get($id);

    /**
     * Returns true if the container can return an entry for the given identifier.
     * Returns false otherwise.
     *
     * `has($id)` returning true does not mean that `get($id)` will not throw an exception.
     * It does however mean that `get($id)` will not throw a `NotFoundExceptionInterface`.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @return bool
     */
    public function has($id);
}
```

### PSR-13 Link definition interfaces
定义了Link对象

**Psr\Link\LinkInterface**
```
<?php

namespace Psr\Link;

/**
 * A readable link object.
 */
interface LinkInterface
{
    public function getHref();
    public function isTemplated();
    public function getRels();
    public function getAttributes();
}
```

**Psr\Link\EvolvableLinkInterface**
```
<?php

namespace Psr\Link;

/**
 * An evolvable link value object.
 */
interface EvolvableLinkInterface extends LinkInterface
{
    public function withHref($href);
    public function withRel($rel);
    public function withoutRel($rel);
    public function withAttribute($attribute, $value);
    public function withoutAttribute($attribute);
}
```
**Psr\Link\LinkProviderInterface**
```
<?php

namespace Psr\Link;

/**
 * A link provider object.
 */
interface LinkProviderInterface
{
    public function getLinks();
    public function getLinksByRel($rel);
}
```
**Psr\Link\EvolvableLinkProviderInterface**
```
<?php

namespace Psr\Link;

/**
 * An evolvable link provider value object.
 */
interface EvolvableLinkProviderInterface extends LinkProviderInterface
{
    public function withLink(LinkInterface $link);
    public function withoutLink(LinkInterface $link);
}
```

### PSR-14: Event Dispatcher
事件分发是一种公认的通过验证的让开发者能够更容易的将逻辑插入应用的机制
##### 目的
为基于事件的扩展和协作建立通用的机制，使库和组件在不同的应用中更自由的重用
Eg：
- 一个安全的系统会阻止用户保存/访问数据当用户没有权限的时候
- 一个通用的整页缓存系统
- 继承其他库的库，无论它们应用在哪个系统
- 用于跟踪应用程序中执行的所有操作的日志包

##### 定义
- Event 由发射器产生的一条信息，可以是任何的php对象
  - Stoppable Event,一种带有阻止Event传递功能的特殊事件，继承StoppableEventInterface
- Listener 任何等待被传递给事件的php调用，可以将同一个Event传递给多个Listener
  - 只有一个参数 Event
  - 可以通过参数类型定义来标记它是哪一种Event的Listener
  - 不返回值，Dispatcher也不应该接收返回值
  - 可以调用委托其他代码来实现功能
  - 可以对Event处理进行排队，如queue，cron等
- Emitter 任何想发送事件的代码
- Dispatcher Emitter提供给事件的一个服务(Service)，确保Event被分发给所有的Listener，调用Listener Provider来确定事件分发给对应的监听器
  - 继承EventDispatcherInterface
  - 必须调用所有Listener Provider返回的Listener
  - 必须返回调用完所有Listener后的Event
  - 直到处理完所有Event再返回Emmiter
  - 如果传递给Stoppable Event，必须调用isPropagationStopped()，返回true则停止调用其他Listener
- Listener Provider 决定哪些Listener对应哪些Event，以及Listener调用顺序，但是不直接调用Listener
  - Order功能
  - 根据Event的类型和实现的接口，通过反射派生适用的Listener列表。
  - 实现一些权限控制来决定用户是否有权限访问某些Listener
  - 提取一些扩展信息根据Event中的Reference数据
  - Delegating its responsibility to one or more other Listener Providers using some arbitrary logic.
  - 监听器应该使用Event名字来区分

##### Interface
```
namespace Psr\EventDispatcher;

/**
 * Defines a dispatcher for events.
 */
interface EventDispatcherInterface
{
    /**
     * Provide all relevant listeners with an event to process.
     *
     * @param object $event
     *   The object to process.
     *
     * @return object
     *   The Event that was passed, now modified by listeners.
     */
    public function dispatch(object $event);
}
namespace Psr\EventDispatcher;

/**
 * Mapper from an event to the listeners that are applicable to that event.
 */
interface ListenerProviderInterface
{
    /**
     * @param object $event
     *   An event for which to return the relevant listeners.
     * @return iterable[callable]
     *   An iterable (array, iterator, or generator) of callables.  Each
     *   callable MUST be type-compatible with $event.
     */
    public function getListenersForEvent(object $event) : iterable;
}
namespace Psr\EventDispatcher;

/**
 * An Event whose processing may be interrupted when the event has been handled.
 *
 * A Dispatcher implementation MUST check to determine if an Event
 * is marked as stopped after each listener is called.  If it is then it should
 * return immediately without calling any further Listeners.
 */
interface StoppableEventInterface
{
    /**
     * Is propagation stopped?
     *
     * This will typically only be used by the Dispatcher to determine if the
     * previous listener halted propagation.
     *
     * @return bool
     *   True if the Event is complete and no further listeners should be called.
     *   False to continue calling listeners.
     */
    public function isPropagationStopped() : bool;
}
```

### PSR-15 HTTP Server Request Handlers
为HTTP server request handlers("request handlers") 和 HTTP server middleward components("middleware")定义接口，使用在PSR-7中描述的HTTP messages
"request handlers"是web应用的基础。服务端接收request消息，处理它并产生response消息。"middleware"提供了一种隔离request/response和应用层的方式

##### 定义
- Request Handlers
一种独立的组件，处理request，并产生response
接口 Psr\Http\Server\RequestHandlerInterface
- Middleware
中间件是一种独立的组件，参与或者与其他中间件合作处理请求和产生结果
接口 Psr\Http\Server\MiddlewareInterface

##### INTERFACE
**Psr\Http\Server\RequestHandlerInterface**
```
namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Handles a server request and produces a response.
 *
 * An HTTP request handler process an HTTP request in order to produce an
 * HTTP response.
 */
interface RequestHandlerInterface
{
    /**
     * Handles a request and produces a response.
     *
     * May call other collaborating code to generate the response.
     */
    public function handle(ServerRequestInterface $request): ResponseInterface;
}
```
**Psr\Http\Server\MiddlewareInterface**
```
namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Participant in processing a server request and response.
 *
 * An HTTP middleware component participates in processing an HTTP message:
 * by acting on the request, generating the response, or forwarding the
 * request to a subsequent middleware and possibly acting on its response.
 */
interface MiddlewareInterface
{
    /**
     * Process an incoming server request.
     *
     * Processes an incoming server request in order to produce a response.
     * If unable to produce the response itself, it may delegate to the provided
     * request handler to do so.
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface;
}
```

### PSR-16: Common Interface for Caching Libraries
为缓存项和缓存驱动提供一个一个简单却可扩展的接口

##### 介绍

##### 定义
1. Caching Library - 调用cache库的方法或者类库只需要关注cache interface提供的方法，不需要关注具体的实现细节
2. Implementing Library - 为了给调用库提供Cache服务，Cache必须实现标准。Cache必须实现以下两个Interface，Cache\CacheItemPoolInterface和Cache\CacheItemInterface，TTL必须是秒级别的
3. TTL - Time To Live, Cache的有效时间
4. Expiration - Cache实际过期时间
5. Key - 一个确定一条Cache记录的唯一关键字
6. Cache - 一个实现Psr\SimpleCache\CacheInterface接口的对象
7. Cache Misses - 缓存未命中将返回null，因此检测是否存在一个存储的空值，这是与PSR-6的主要区别

##### Interface
```
<?php

namespace Psr\SimpleCache;

interface CacheInterface
{
    /**
     * Fetches a value from the cache.
     *
     * @param string $key     The unique key of this item in the cache.
     * @param mixed  $default Default value to return if the key does not exist.
     *
     * @return mixed The value of the item from the cache, or $default in case of cache miss.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function get($key, $default = null);

    /**
     * Persists data in the cache, uniquely referenced by a key with an optional expiration TTL time.
     *
     * @param string                 $key   The key of the item to store.
     * @param mixed                  $value The value of the item to store. Must be serializable.
     * @param null|int|\DateInterval $ttl   Optional. The TTL value of this item. If no value is sent and
     *                                      the driver supports TTL then the library may set a default value
     *                                      for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function set($key, $value, $ttl = null);

    /**
     * Delete an item from the cache by its unique key.
     *
     * @param string $key The unique cache key of the item to delete.
     *
     * @return bool True if the item was successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function delete($key);

    /**
     * Wipes clean the entire cache's keys.
     *
     * @return bool True on success and false on failure.
     */
    public function clear();

    /**
     * Obtains multiple cache items by their unique keys.
     *
     * @param iterable $keys    A list of keys that can obtained in a single operation.
     * @param mixed    $default Default value to return for keys that do not exist.
     *
     * @return iterable A list of key => value pairs. Cache keys that do not exist or are stale will have $default as value.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function getMultiple($keys, $default = null);

    /**
     * Persists a set of key => value pairs in the cache, with an optional TTL.
     *
     * @param iterable               $values A list of key => value pairs for a multiple-set operation.
     * @param null|int|\DateInterval $ttl    Optional. The TTL value of this item. If no value is sent and
     *                                       the driver supports TTL then the library may set a default value
     *                                       for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $values is neither an array nor a Traversable,
     *   or if any of the $values are not a legal value.
     */
    public function setMultiple($values, $ttl = null);

    /**
     * Deletes multiple cache items in a single operation.
     *
     * @param iterable $keys A list of string-based keys to be deleted.
     *
     * @return bool True if the items were successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function deleteMultiple($keys);

    /**
     * Determines whether an item is present in the cache.
     *
     * NOTE: It is recommended that has() is only to be used for cache warming type purposes
     * and not to be used within your live applications operations for get/set, as this method
     * is subject to a race condition where your has() will return true and immediately after,
     * another script can remove it, making the state of your app out of date.
     *
     * @param string $key The cache item key.
     *
     * @return bool
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function has($key);
}

namespace Psr\SimpleCache;

/**
 * Interface used for all types of exceptions thrown by the implementing library.
 */
interface CacheException
{
}

namespace Psr\SimpleCache;

/**
 * Exception interface for invalid cache arguments.
 *
 * When an invalid argument is passed, it must throw an exception which implements
 * this interface.
 */
interface InvalidArgumentException extends CacheException
{
}
```

### PSR-17: HTTP Factories
为PSR-7中的HTTP Message的对象创建提供工厂方法

##### Interface
**RequestFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\UriInterface;

interface RequestFactoryInterface
{
    /**
     * Create a new request.
     *
     * @param string $method The HTTP method associated with the request.
     * @param UriInterface|string $uri The URI associated with the request.
     */
    public function createRequest(string $method, $uri): RequestInterface;
}
```

**ResponseFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\ResponseInterface;

interface ResponseFactoryInterface
{
    public function createResponse(int $code = 200, string $reasonPhrase = ''): ResponseInterface;
}
```

**ServerRequestFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Message\UriInterface;

interface ServerRequestFactoryInterface
{
    /**
     * Create a new server request.
     *
     * Note that server parameters are taken precisely as given - no parsing/processing
     * of the given values is performed. In particular, no attempt is made to
     * determine the HTTP method or URI, which must be provided explicitly.
     *
     * @param string $method The HTTP method associated with the request.
     * @param UriInterface|string $uri The URI associated with the request.
     * @param array $serverParams An array of Server API (SAPI) parameters with
     *     which to seed the generated request instance.
     */
    public function createServerRequest(string $method, $uri, array $serverParams = []): ServerRequestInterface;
}
```

**StreamFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\StreamInterface;

interface StreamFactoryInterface
{
    /**
     * Create a new stream from a string.
     *
     * The stream SHOULD be created with a temporary resource.
     *
     * @param string $content String content with which to populate the stream.
     */
    public function createStream(string $content = ''): StreamInterface;

    /**
     * Create a stream from an existing file.
     *
     * The file MUST be opened using the given mode, which may be any mode
     * supported by the `fopen` function.
     *
     * The `$filename` MAY be any string supported by `fopen()`.
     *
     * @param string $filename The filename or stream URI to use as basis of stream.
     * @param string $mode The mode with which to open the underlying filename/stream.
     *
     * @throws \RuntimeException If the file cannot be opened.
     * @throws \InvalidArgumentException If the mode is invalid.
     */
    public function createStreamFromFile(string $filename, string $mode = 'r'): StreamInterface;

    /**
     * Create a new stream from an existing resource.
     *
     * The stream MUST be readable and may be writable.
     *
     * @param resource $resource The PHP resource to use as the basis for the stream.
     */
    public function createStreamFromResource($resource): StreamInterface;
}
```

**UploadedFileFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\StreamInterface;
use Psr\Http\Message\UploadedFileInterface;

interface UploadedFileFactoryInterface
{
    /**
     * Create a new uploaded file.
     *
     * If a size is not provided it will be determined by checking the size of
     * the stream.
     *
     * @link http://php.net/manual/features.file-upload.post-method.php
     * @link http://php.net/manual/features.file-upload.errors.php
     *
     * @param StreamInterface $stream The underlying stream representing the
     *     uploaded file content.
     * @param int $size The size of the file in bytes.
     * @param int $error The PHP file upload error.
     * @param string $clientFilename The filename as provided by the client, if any.
     * @param string $clientMediaType The media type as provided by the client, if any.
     *
     * @throws \InvalidArgumentException If the file resource is not readable.
     */
    public function createUploadedFile(
        StreamInterface $stream,
        int $size = null,
        int $error = \UPLOAD_ERR_OK,
        string $clientFilename = null,
        string $clientMediaType = null
    ): UploadedFileInterface;
}
```

**UriFactoryInterface**
```
namespace Psr\Http\Message;

use Psr\Http\Message\UriInterface;

interface UriFactoryInterface
{
    /**
     * Create a new URI.
     *
     * @param string $uri The URI to parse.
     *
     * @throws \InvalidArgumentException If the given URI cannot be parsed.
     */
    public function createUri(string $uri = '') : UriInterface;
}
```

### PSR-18: HTTP Client
##### 目的
- 解耦，让开发者开发的库与HTTP Client的实现解耦，增加库的重用性降低了库版本冲突
- 里氏兑换原则，所有的Client在发送request的时候行为相同

##### 定义
Client 是一个库，能发送PSR-7中定义的的Request Messages返回PSR-7中的Response Messages

##### Interface
```
namespace Psr\Http\Client;

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;

interface ClientInterface
{
    /**
     * Sends a PSR-7 request and returns a PSR-7 response.
     *
     * @param RequestInterface $request
     *
     * @return ResponseInterface
     *
     * @throws \Psr\Http\Client\ClientExceptionInterface If an error happens while processing the request.
     */
    public function sendRequest(RequestInterface $request): ResponseInterface;
}
```


