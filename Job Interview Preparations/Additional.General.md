# Additional General 额外

## Data Classification 公司级别的数据分类和分级

| Level | Explanation | Examples | Note |
|-|-|-|-|
|1st class| 最敏感的数据，任何时候都不得存储在任何地方，仅用于即时计算。 | PIN，CVV等 | 不得存储 |
|2nd class| 高度敏感信息，如果此类信息遭到非法访问或者泄漏，公司可能会因此遭到重大财务和声誉损害。 |信用卡帐号，银行账号，社保账号，验证密码，加密密钥，机密问题答案等。| __需要加密以及完全隐藏传输__。 |
|3rd class| 敏感信息，本质上为私有信息，发布此类信息会对公司，商户或者员工造成不利影响。 | 消费者个人信息如Email、联系地址、出生日期，员工个人信息如出生日期、家庭住址，员工薪资信息，产品设计详细信息等等。|此类信息至少需要 __部分加密存储，以及部分隐藏传输__。|
|4th class| 内部信息，在公司或者组织内部有限或者适当范围内传播的信息。 |如公司内部文件，内部公告等等。 |此类信息至少需要 __适当存储，或则部分隐藏传输__。 |
|5th class| 公开信息，公司向公众开放的信息。 |如向公众开放的财务数据等等。| 无需加密 |

## Soft skills 软技能

### SWOT 分析法

- S(Strengths), W(Weaknesses), O(Opportunities), T(Threats);
- 针对市场产品的定位和营销策略，可以从 SWOT 角度来分析情况，进而辅助决策；

### STAR 分析法

- S(Situation), T(Task), A(Action), R(Result);
- 针对回顾之前的某段经历，可以采取 STAR 方法来有逻辑和组织的分析事件；

## HTTP 相关

HTTPS 相比 HTTP 多出了 SSL/TLS的安全部分，使用了国际加密算法如RSA以及非对称加解密；

### HTTP Request Method

常见的请求方法有：
- GET, get resource, params in URL;
- POST, create resource, optional payload in body;
- PUT, update resource, optional payload;
- DELETE, delete resource(s)

不常见的请求方法有：
- HEAD, like GET, for getting header info(only header response);
- TRACE, ...
- OPTIONS, get allowed methods info for a resource.
- PATCH, like PUT, but partial update a resource.

### HTTP Status Code

- 2xx, success, e.g. "200";
- 3xx, redirect, new url should be returned in headers.
- 4xx, client error
  + 400 bad request
  + 401 unauthorized
  + 403 forbidden
  + 404 not found
  + 405 method not allowed
- 5xx, server error
  + 500 internal server error
  + 501 not implemented
  + 503 service unavailable (service down or overloaded)

## Cookie 和 Session

- Cookie 会在本地存放一些小的信息，比如 username, session id;
- 在browser发请求时会带上 cookie 信息，服务器后台会查询session 信息并验证用户;
- Session 在后端可能是类似 hashtable 存储，通过 session id 映射，可以得到用户信息以及 session 过期时间；
- 现在都可以使用 token替换掉 cookie和session方案；

