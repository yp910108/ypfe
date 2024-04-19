## REST
#### REST 是什么?
- 万维网软件架构<font color='red'>风格</font>
#### 为何叫 REST？
<font color='red'>Re</font>presentational <font color='red'>S</font>tate <font color='red'>T</font>ransfer
- Representational：数据的表现形式（JSON、XML......）
- State：当前的状态或者数据
- Transfer：数据传输
#### REST 的6个限制
##### <font color='red'>客户端-服务器（Client-Server）</font>
- 关注点分离
- 服务端专注数据存储，提升了简单性
- 前端专注用户界面，提升了可移植性
##### <font color='red'>无状态（StateLess）</font>
- 所有用户会话信息都保存在客户端
- 每次请求都必须包括所有信息，不能依赖上下文信息
- 服务端不会保存会话信息，提升了简单性、可靠性、可见性
##### <font color='red'>缓存（Cache）</font>
- 所有服务端响应都要标为可缓存或不可缓存
- 减少前后端交互，提升性能
##### <font color='red'>统一接口（Uniform Interface）</font>
- 接口设计尽可能统一通用，提升了简单性、可见性
- 接口与实现解藕，使前后端可以独立开发迭代
##### <font color='red'>分层系统（Layered System）</font>
- 每层只知道相邻的一层，后面隐藏的就不知道了
- 客户端不知道是和代理还是真实的服务器通信
- 其他层：安全层、负载均衡层、缓存层等
##### <font color='red'>按需代码（Code-On-Command 可选）</font>
- 客户端可以下载运行服务端传来的代码（比如JS）
- 通过减少一些功能，简化了客户端
#### 统一接口的限制
##### 资源的标识
- 资源是任何可以命名的事物，比如用户、评论等
- 每个资源都可以都过 URI 被唯一的标识
##### 通过表述来操作资源
- 表述就是 Representational，比如 JSON、XML等
- 客户端不能直接操作（比如 SQL）服务端资源
- 客户端应该通过表述（比如 JSON）来操作资源
##### 自描述信息
- 每个消息（请求或响应）必须提供足够的信息让接受者理解
- 媒体类型（application/json、application/xml）
- HTTP 方法：GET（查）、POST（增）、DELETE（删）
- 是否缓存：Cache-Control
##### 超媒体作为应用状态引擎
- 超媒体：带文字的链接
- 应用状态：一个网页
- 引擎：驱动、跳转
- 合起来：点击链接跳转到另一个网页
## RESTful API
#### 什么是 RESTful API？
- 符合 REST 架构风格的 API
#### RESTful API 具体长什么样子？
- 基本的 URI，如：https://api.github.com/users
- 标准 HTTP 方法，如：GET、POST、PUT、PATCH、DELETE
- 传输的数据媒体类型，如：JSON、XML
> https://developer.github.com/v3/users/
#### 现实举例
- GET /users # 获取 user 列表
- GET /users/12 # 查看某个具体的 user
- POST /users # 新建一个 user
- PUT /users/12 # 更新 user 12
- DELETE /users/12 # 删除 user 12
#### 请求设计规范
- URI 使用名词，尽量使用复数，如 /users
- URI 使用嵌套表示关联关系，如 /users/12/repos/5
- 使用正确的 HTTP 方法，如：GET、POST、PUT、DELETE
- 不符合 CRUD 的情况：POST + 动词、action、子资源
#### 响应设计规范
- 查询
- 分页
- 字段过滤
- 状态码
- 错误处理
#### 安全
- HTTPS
- 鉴权
- 限流
#### 开发者友好
- 文档
- 超媒体
#### 什么是控制器？
- 路由是根据不同的 url 分配不同的任务，控制器则是拿到路由分配的任务，并执行
- 在 Koa 中，控制器就是一个中间件
#### 为什么需要控制器？
- 获取 HTTP 请求参数
- 处理业务逻辑
- 发送 HTTP 响应
#### 获取 HTTP 请求参数
- 查询字符串，Query String，如：?q=keyword
- 路由参数，Router Params，如：/users/:id
- 请求体，Body，如：{ name: '李雷' }
- 请求头，Header，如：Accept、Cookie
#### 发送 HTTP 响应
- 发送 Status，如：200、404
- 发送 Body，如：{ name: '李雷' }
- 发送 Header，如：Allow、Content-Type
#### 编写控制器最佳实践
- 每个资源的控制器放在不同的文件里
- 尽量使用`类 + 类方法`的形式编写控制器
- 严谨的错误处理
#### 什么是错误处理？
- 编程语言或计算机硬件里的一种机制
- 处理软件或信息系统中出现的异常状况
#### 异常状况有哪一些？
- 运行时错误，都返回500，代表服务器内部错误，如：读取值为 undefined 的某个属性
- 逻辑错误，如：找不到（404）、先觉条件失败（412）、无法处理的实体（比如请求体内参数格式不对 422）等
#### 为什么要用错误处理？
- 防止程序挂掉
- 告诉用户错误信息
- 便于开发者调试
#### 什么是 NoSQL？
- 对不同于传统的关系型数据库的数据库管理系统的总称
#### NoSQL 数据库的分类
- 列存储（HBase）
- 文档存储（MongoDB）
- Key-Value 存储（Redis）
- 图存储（FlockDB）
- 对象存储（db4o）
- XML 存储（BaseX）
#### 为什么要用 NoSQL？
- 简单（没有原子性、一致性、隔离性等复杂规范）
- 便于横向拓展
- 适合超大规模数据的存储
- 很灵活地存储复杂结构的数据（Schema Free）
#### 什么是 MongoDB？
- 来源于英文单词 “Humongous”，中文含义为“庞大”
- 面向文档存储的开源数据库
#### 为什么要用 MongoDB？
- 性能好（内存计算）
- 大规模数据存储（可拓展性）
- 可靠安全（本地复制、自动故障迁移）
- 方便存储复杂数据库（Schema Free）
#### Session 的优势
- 相比于 JWT，最大的优势就在于可以主动清除了
- session 保存在服务器端，相对较为安全
- 结合 cookie 使用，较为灵活，兼容性较好
#### Session 的劣势
- cookie 和 session 在跨域场景表现并不好
- 如果是分布式部署，需要做多机共享 session 机制
- 基于 cookie 的机制很容易被 CSRF
- 查询 session 信息可能会有数据库查询操作
#### Session 相关的概念介绍
- session：主要存放在服务器端，相对安全
- cookie：主要存放在客户端，不是很安全
- sessionStorage：仅在当前会话下有效，关闭页面或浏览器后被清除
- localStorage：除非被清除，否则永久保存
#### 什么是 JWT？
- JSON Web Token 是一个开放标准（RFC 7519）
- 定义了一种紧凑且独立的方式，可以将各方之间的信息作为 JSON 对象进行安全传输
- 该信息可以验证和信任，因为是经过数字签名的
#### JWT 的构成
##### 头部（Header）
- typ：token 的类型，这里固定为 JWT
- alg：使用的 hash 算法，例如：HMAC、SHA256、RSA
##### 有效载荷（Payload）
- 存储需要传递的信息，如：用户 ID、用户名等
- 还包含元数据，如：过期时间、发布人等
- 与 Header 不同，Payload 可以加密
##### 签名（Signature）
- 对 Header 和 Payload 部分进行签名
- 保证 Token 在传输的过程中没有被篡改或者损坏
#### JWT vs Session
- 可拓展性
- 安全性
- RESTful
- 性能
- 时效性