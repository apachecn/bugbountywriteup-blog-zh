# Web 应用程序安全错误配置——这会让您付出代价

> 原文：<https://infosecwriteups.com/web-application-security-misconfiguration-that-will-cost-you-6cc69d22d2?source=collection_archive---------1----------------------->

闭上你的🚪—对攻击者和黑客有 70%的防御能力

# 描述

尽管您的专家团队已经尽了最大努力来减少您系统中的所有错误。作为一名独立的研究人员，跨所有平台研究它，并帮助组织使它对您的客户更加安全。在研究过程中，我发现几乎所有的网络应用都存在漏洞，有时我自己也会报告这一点，但我是一个人，一天的时间不会超过 24 小时(但我真的想要更多时间:-)。我写这篇文章是希望每当我在你的网站上创建一个新账户时，我的个人数据不会被轻易窃取。

# 问题

> **CORS(跨起点资源共享)报头错误配置
> 访问控制允许起点→***

哦，你需要我解释这个沉重的(CORS)词，
**起源:**它是某人或你在网络上的身份，用于 web 应用服务器。([www.yoursite.com](http://www.yoursite.com)，[www.someonesite.com](http://www.someonesite.com))
**资源:**资产，从你的服务器(你存储所有文件的地方——代码{ HTML，CSS，JS 文件})返回给浏览器的每一个东西。
***:** 表示全部([www.anybody.com](http://www.anybody.com))

# 问题影响

如果您在服务器上配置您的 web 应用程序时将其标记为*，则任何人都可以从任何地方访问您的资源。而且，一旦他们可以与你的后端系统互动，他们很容易拦截你的网络和 XHR。

为什么我需要这个？原因和目的..阿汉！我喜欢你的疑问和问题！
因为 www.dreamsite.com，数据是你做出决策和运行梦想项目的真正力量，因此，你需要它，因为数据相当于$$$。
而且，我相信你知道通过你的平台公开泄露你的客户和供应商的数据会造成的不良影响，这种影响会在两分钟内损害你的声誉。

> “建立声誉需要 20 年，毁掉它只需 5 分钟。如果你考虑到这一点，你会以不同的方式做事。”—沃伦·巴菲特

## 非常确信！让我们明白如何才能填补这一空白。

# 如何缓解？

1.如果资源包含敏感信息，则不应将“Access-Control-Allow-Origin”设置为*。
2。缓解很简单，只是一个适当的配置。将 Access-Control-Allow-Origin 标头配置为仅允许来自您信任的域的请求。例如:Access-Control-Allow-Origin:trusted _ domains . com
3。确保在用于检查原始头值的服务器端验证中，与绝对值进行比较，而不是与正则表达式进行比较。
举个例子:下面的代码做一个和正则表达式的比较:
regex("^[https://mail . example . com $](https://mail.example.com$)"
在上面的验证中，点(。)表示任何字符。因此，攻击者可以通过从以下域发出 CORS 请求来绕过它:[https://mailxexample.com](https://mailxexample.com)
修补后的代码将是:
if($ _ SERVER[" HTTP _ ORIGIN "]= = "[https://mail.example.com](https://mail.example.com)"
{
header(" Access-Control-Allow-ORIGIN:[https://mail.example.com](https://mail.example.com)")；
}

*客户端不应信任未经净化的接收内容，因为这将导致客户端代码执行。例如:如果网站 abc.com 信任并从 example.com 获取跨域数据。example.com 怀有恶意，开始向 abc.com 发送恶意 javascript，然后 abc.com 可以通过净化接收到的数据并将其呈现给用户来保护其用户免受跨站脚本攻击。*

示例实现:过滤器类(Java 代码)

```
 import java.io.IOException;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Enumeration;
import java.util.List;import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;import net.sf.ehcache.Cache;
import net.sf.ehcache.CacheManager;
import net.sf.ehcache.Element;
import net.sf.ehcache.config.CacheConfiguration;
import net.sf.ehcache.config.PersistenceConfiguration;
import net.sf.ehcache.store.MemoryStoreEvictionPolicy;/**
 * Sample filter implementation to scrutiny CORS “Origin” HTTP header.<br/>
 * 
 * This implementation has a dependency on EHCache API because<br/>
 * it use Caching for blacklisted client IP in order to enhance performance.
 * 
 * Assume here that all CORS resources are grouped in context path “/cors/”.
 * 
 */
[@WebFilter](http://twitter.com/WebFilter)(“/cors/*”)
public class CORSOriginHeaderScrutiny implements Filter {/** Filter configuration */
 [@SuppressWarnings](http://twitter.com/SuppressWarnings)(“unused”)
 private FilterConfig filterConfig = null;/** Cache used to cache blacklisted Clients (request sender) IP address */
 private Cache blackListedClientIPCache = null;/** Domains allowed to access to resources (white list) */
 private List<String> allowedDomains = new ArrayList<String>();/**
 * {[@inheritDoc](http://twitter.com/inheritDoc)}
 * 
 * [@see](http://twitter.com/see) Filter#init(FilterConfig)
 */
 [@Override](http://twitter.com/Override)
 public void init(FilterConfig fConfig) throws ServletException {
 // Get filter configuration
 this.filterConfig = fConfig;
 // Initialize Client IP address dedicated cache with a cache of 60 minutes expiration delay for each item
 PersistenceConfiguration cachePersistence = new PersistenceConfiguration();
 cachePersistence.strategy(PersistenceConfiguration.Strategy.NONE);
 CacheConfiguration cacheConfig = new CacheConfiguration().memoryStoreEvictionPolicy(MemoryStoreEvictionPolicy.FIFO)
 .eternal(false)
 .timeToLiveSeconds(3600)
 .diskExpiryThreadIntervalSeconds(450)
 .persistence(cachePersistence)
 .maxEntriesLocalHeap(10000)
 .logging(false);
 cacheConfig.setName(“BlackListedClientsCacheConfig”);
 this.blackListedClientIPCache = new Cache(cacheConfig);
 this.blackListedClientIPCache.setName(“BlackListedClientsCache”);
 CacheManager.getInstance().addCache(this.blackListedClientIPCache);
 // Load domains allowed white list (hard coded here only for example)
 this.allowedDomains.add(“[http://www.html5rocks.com](http://www.html5rocks.com)”);
 this.allowedDomains.add(“[https://www.mydomains.com](https://www.mydomains.com)”);
 }/**
 * {[@inheritDoc](http://twitter.com/inheritDoc)}
 * 
 * [@see](http://twitter.com/see) Filter#destroy()
 */
 [@Override](http://twitter.com/Override)
 public void destroy() {
 // Remove Cache
 CacheManager.getInstance().removeCache(“BlackListedClientsCache”);
 }/**
 * {[@inheritDoc](http://twitter.com/inheritDoc)}
 * 
 * [@see](http://twitter.com/see) Filter#doFilter(ServletRequest, ServletResponse, FilterChain)
 */
 [@Override](http://twitter.com/Override)
 public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
 throws IOException, ServletException {
 HttpServletRequest httpRequest = ((HttpServletRequest) request);
 HttpServletResponse httpResponse = ((HttpServletResponse) response);
 List<String> headers = null;
 boolean isValid = false;
 String origin = null;
 String clientIP = httpRequest.getRemoteAddr();/* Step 0 : Check presence of client IP in black list */
 if (this.blackListedClientIPCache.isKeyInCache(clientIP)) {
 // Return HTTP Error without any information about cause of the request reject !
 httpResponse.sendError(HttpServletResponse.SC_FORBIDDEN);
 // Add trace here
 // ….
 // Quick Exit
 return;
 }/* Step 1 : Check that we have only one and non empty instance of the “Origin” header */
 headers = CORSOriginHeaderScrutiny.enumAsList(httpRequest.getHeaders(“Origin”));
 if ((headers == null) || (headers.size() != 1)) {
 // If we reach this point it means that we have multiple instance of the “Origin” header
 // Add client IP address to black listed client
 addClientToBlacklist(clientIP);
 // Return HTTP Error without any information about cause of the request reject !
 httpResponse.sendError(HttpServletResponse.SC_FORBIDDEN);
 // Add trace here
 // ….
 // Quick Exit
 return;
 }
 origin = headers.get(0);/* Step 2 : Check that we have only one and non empty instance of the “Host” header */
 headers = CORSOriginHeaderScrutiny.enumAsList(httpRequest.getHeaders(“Host”));
 if ((headers == null) || (headers.size() != 1)) {
 // If we reach this point it means that we have multiple instance of the “Host” header
 // Add client IP address to black listed client
 addClientToBlacklist(clientIP);
 // Return HTTP Error without any information about cause of the request reject !
 httpResponse.sendError(HttpServletResponse.SC_FORBIDDEN);
 // Add trace here
 // ….
 // Quick Exit
 return;
 }/* Step 3 : Perform analysis — Origin header is required */
 if ((origin != null) && !””.equals(origin.trim())) {
 if (this.allowedDomains.contains(origin)) {
 // Check if origin is in allowed domain
 isValid = true;
 } else {
 // Add client IP address to black listed client
 addClientToBlacklist(clientIP);
 isValid = false;
 // Add trace here
 // ….
 }
 }/* Step 4 : Finalize request next step */
 if (isValid) {
 // Analysis OK then pass the request along the filter chain
 chain.doFilter(request, response);
 } else {
 // Return HTTP Error without any information about cause of the request reject !
 httpResponse.sendError(HttpServletResponse.SC_FORBIDDEN);
 }
 }/**
 * Blacklist client
 * 
 * [@param](http://twitter.com/param) clientIP Client IP address
 */
 private void addClientToBlacklist(String clientIP) {
 // Add client IP address to black listed client
 Element cacheElement = new Element(clientIP, clientIP);
 this.blackListedClientIPCache.put(cacheElement);
 }/**
 * Convert a enumeration to a list
 * 
 * [@param](http://twitter.com/param) tmpEnum Enumeration to convert
 * [@return](http://twitter.com/return) list of string or null is input enumeration is null
 */
 private static List<String> enumAsList(Enumeration<String> tmpEnum) {
 if (tmpEnum != null) {
 return Collections.list(tmpEnum);
 }
 return null;
 }
}
```

注意:如果你想 CORS 过滤器在不同的语言评论如下。

对于 Nginx 上的 CORS:[https://medium . com/@ hariomvashith/CORS-on-Nginx-be 38 DD 0 e 19 df](https://medium.com/@hariomvashisth/cors-on-nginx-be38dd0e19df)