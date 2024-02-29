# 一、信息响应
【100】【Continue】请求者应当继续提出请求。服务器返回此代码表示已收到请求的第一部分，正在等待其余部分。

【101】【Switching Protocols】请求者已要求服务器切换协议，服务器已确认并准备切换。

【102】【Processing】  服务器已收到并正在处理该请求，但当前没有响应可用。

【103】【Early Hints】 此状态代码主要用于与 Link 链接头一起使用，以允许用户代理在服务器准备响应阶段时开始预加载 preloading 资源。

# 二、成功响应
【200】【OK】服务器已成功处理了请求。通常，这表示服务器提供了请求的网页。

【201】【Created】请求成功并且服务器创建了新的资源。

【202】【Accepted】服务器已接受请求，但尚未处理。

【203】【Non-Authoritative Information】服务器已成功处理了请求，但返回的信息可能来自另一来源。

【204】【No Content】服务器成功处理了请求，但没有返回任何内容。

【205】【Reset Content】告诉用户代理重置发送此请求的文档。

【206】【Partial Content】服务器成功处理了部分 GET 请求。

【207】【Multi-Status】对于多个状态代码都可能合适的情况，传输有关多个资源的信息。

【208】【Already Reported】在 DAV 里面使用 `<dav:propstat>` 响应元素以避免重复枚举多个绑定的内部成员到同一个集合。

【226】【IM Used】服务器已经完成了对资源的GET请求，并且响应是对当前实例应用的一个或多个实例操作结果的表示。

# 三、重定向消息
【300】【Multiple Choice】针对请求，服务器可执行多种操作。服务器可根据请求者(user agent)选择一项操作，或提供操作列表供请求者选择。

【301】【Moved Permanently】请求的网页已永久移动到新位置。服务器返回此响应(对 GET 或 HEAD 请求的响应)时，会自动将请求者转到新位置。

【302】【Found】服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。

【303】【See Other】请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。

【304】【Not Modified】自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。

【305】【Use Proxy】在 HTTP 规范中定义，以指示请求的响应必须被代理访问。由于对代理的带内配置的安全考虑，它已被弃用。

【306】【unused】此响应代码不再使用；它只是保留。它曾在 HTTP/1.1 规范的早期版本中使用过。

【307】【Temporary Redirect】服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。

【308】【Permanent Redirect】这意味着资源现在永久位于由Location: HTTP Response 标头指定的另一个 URI。

# 四、客户端错误响应
【400】【Bad Request】表示客户端请求的语法错误，服务器无法理解，例如 url 含有非法字符、json 格式有问题。

【401】【Unauthorized】虽然 HTTP 标准指定了"unauthorized"，但从语义上来说，这个响应意味着"unauthenticated"。也就是说，客户端必须对自身进行身份验证才能获得请求的响应。

【402】【Payment Required】表示保留，将来使用。

【403】【Forbidden】客户端没有访问内容的权限；也就是说，它是未经授权的，因此服务器拒绝提供请求的资源。与 401 Unauthorized 不同，服务器知道客户端的身份。

【404】【Not Found】服务器无法根据客户端的请求找到资源(网页)。

【405】【Method Not Allowed】禁用请求中指定的方法。

【406】【Not Acceptable】无法使用请求的内容特性响应请求的网页。

【407】【Proxy Authentication Required】此状态代码与 401(未授权)类似，但指定请求者应当授权使用代理。

【408】【Request Timeout】服务器等候请求时发生超时。

【409】【Conflict】服务器在完成请求时发生冲突。服务器必须在响应中包含有关冲突的信息。

【410】【Gone】如果请求的资源已永久删除，服务器就会返回此响应。

【411】【Length Required】服务器不接受不含有效内容长度标头字段的请求。

【412】【Precondition Failed】服务器未满足请求者在请求中设置的其中一个前提条件。

【413】【Payload Too Large】表示响应实在太大。服务器拒绝处理当前请求，请求超过服务器所能处理和允许的最大值。

【414】【URI Too Long】请求的 URI(通常为网址)过长，服务器无法处理。

【415】【Unsupported Media Type】请求的格式不受请求页面的支持。

【416】【Range Not Satisfiable】如果页面无法提供请求的范围，则服务器会返回此状态代码。

【417】【Expectation Failed】在请求头 Expect 指定的预期内容无法被服务器满足(力不从心)。

【418】【I'm a teapot】表示我是一个茶壶。超文本咖啡馆控制协议，但是并没有被实际的 HTTP 服务器实现。

【421】【Misdirected Request】请求被定向到无法生成响应的服务器。这可以由未配置为针对请求 URI 中包含的方案和权限组合生成响应的服务器发送。

【422】【Unprocessable Entity】表示不可处理的实体。请求格式正确，但是由于含有语义错误，无法响应。

【423】【Locked】正在访问的资源已锁定。

【424】【Failed Dependency】由于前一个请求失败，请求失败。

【425】【Too Early】表示服务器不愿意冒险处理可能被重播的请求。

【426】【Upgrade Required】服务器拒绝使用当前协议执行请求，但在客户端升级到其他协议后可能愿意这样做。

【429】【Too Many Requests】用户在给定的时间内发送了太多请求。

【431】【Request Header Fields Too Large】服务器不愿意处理请求，因为其头字段太大。在减小请求头字段的大小后，可以重新提交请求。

【451】【Unavailable For Legal Reasons】用户代理请求了无法合法提供的资源，例如政府审查的网页。

# 五、服务端错误响应
【500】【Internal Server Error】服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。

【501】【Not Implemented】服务器不具备完成请求的功能。例如，服务器无法识别请求方法时可能会返回此代码。

【502】【Bad Gateway】服务器作为网关或代理，从上游服务器收到无效响应。

【503】【Service Unavailable】服务器目前无法使用(由于超载或停机维护)。通常，这只是暂时状态。

【504】【Gateway Timeout】服务器作为网关或代理，但是没有及时从上游服务器收到请求。

【505】【HTTP Version Not Supported】服务器不支持请求中所用的 HTTP 版本。

【506】【Variant Also Negotiates】服务器存在内部配置错误：所选的变体资源被配置为参与透明内容协商本身，因此不是协商过程中的适当终点。

【507】【Insufficient Storage】无法在资源上执行该方法，因为服务器无法存储成功完成请求所需的表示。

【508】【Loop Detected】服务器在处理请求时检测到无限循环。

【510】【Not Extended】服务器需要对请求进行进一步扩展才能完成请求。

【511】【Network Authentication Required】指示客户端需要进行身份验证才能获得网络访问权限。

