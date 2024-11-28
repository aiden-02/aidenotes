---
title: 浏览器缓存机制
order: 1
---

浏览器的缓存机制是前端性能优化中至关重要的一环，它通过缓存之前请求过的文件，以提高页面再次访问时的加载速度。以下是对浏览器缓存机制的详细讲解：

### 一、缓存的分类

浏览器缓存可以根据不同的标准进行分类，以下是按缓存位置和缓存策略的分类方式：

1. **按缓存位置分类**

   - `Service Worker`：运行在浏览器后台的独立线程，可以拦截所有网络请求并缓存数据，主要用于创建有效的离线体验，如后台同步、离线渲染、推送通知等。
   - `Memory Cache`：存储当前页面中已经抓取到的资源，如样式、脚本、图片等。读取速度快，但缓存持续性短，随进程释放而释放。
   - `Disk Cache`：存储在硬盘中的缓存，覆盖面最广，绝大部分的缓存都来自 Disk Cache。读取速度相对较慢，但持久性强。
   - `Push Cache（或称 Heuristic Caching）`：HTTP/2 新增的内容，用于在会话期间缓存资源，会话结束即释放。

2. **按缓存策略分类**

   - `强制缓存（强缓存）`：浏览器在发起请求前，会先检查本地缓存中是否有满足条件的资源。如果有，则直接使用缓存资源，不再向服务器发起请求。主要依赖 HTTP 响应头中的 Expires 和 Cache-Control 字段。
   - `协商缓存（弱缓存）`：当强制缓存失效后，浏览器会携带缓存标识（如 Last-Modified 或 ETag）向服务器发起请求，由服务器根据缓存标识判断是否使用缓存。主要依赖 HTTP 请求头中的 If-Modified-Since 和 If-None-Match 字段，以及响应头中的 Last-Modified 和 ETag 字段。

### 二、缓存的头部信息

浏览器缓存的工作原理基于 HTTP 协议中的缓存控制头（`Cache-Control` Header），这些头部信息由服务器发送，指示浏览器如何缓存资源。以下是一些常见的缓存头部信息：

1. `Expires`：HTTP/1.0 引入，表示资源会在某个绝对时间后过期。由于它依赖于客户端时间，可能存在误差，因此 HTTP/1.1 中引入了 Cache-Control 来替代它。
2. `Cache-Control`：HTTP/1.1 引入，用于控制缓存的行为。它包含多个指令，如：

   - `max-age`：表示资源在多少秒内有效。
   - `no-cache`：表示需要协商缓存。
   - `no-store`：表示不缓存任何内容。
   - `public`：表示资源可以被浏览器和代理服务器缓存。
   - `private`：表示资源只针对单个用户缓存。

3. `Last-Modified / If-Modified-Since`：HTTP/1.0 引入。Last-Modified 表示资源在服务器上的最后修改时间；If-Modified-Since 是浏览器在请求时携带的字段，表示资源在客户端的最后修改时间。服务器通过比较这两个时间来决定是否返回 `304` 状态码（表示资源未修改，使用缓存）或 200 状态码（表示资源已修改，返回新资源）。
4. `ETag / If-None-Match`：HTTP/1.1 引入，用于替代 Last-Modified / If-Modified-Since。ETag 是服务器为资源生成的一个唯一标识（hash 值）；If-None-Match 是浏览器在请求时携带的字段，表示资源在客户端的唯一标识。服务器通过比较这两个标识来决定是否返回 304 或 200 状态码。ETag / If-None-Match 的`优先级高于` Last-Modified / If-Modified-Since。

### 三、缓存的工作流程

当浏览器发起请求时，会按照以下流程处理缓存：

1. 浏览器首先检查本地缓存中是否有满足条件的资源。
2. 如果有，则根据 `Cache-Control` 或 Expires 字段判断资源是否过期。
3. 如果未过期，则直接使用缓存资源。
4. 如果已过期，则进入`协商缓存`阶段，浏览器携带缓存标识（如 If-Modified-Since 或 If-None-Match）向服务器发起请求。
5. 服务器根据请求头中的缓存标识判断资源是否修改。
6. 如果未修改，则返回 304 状态码，浏览器使用缓存资源。
7. 如果已修改，则返回 200 状态码和新资源，浏览器更新缓存。

### 四、缓存的优缺点

**优点**：

1. **减少网络带宽消耗**：缓存减少了不必要的数据传输，节省了带宽。
2. **减轻服务器负担**：通过缓存，服务器不需要处理重复的请求，从而减轻了负担。
3. **提升访问速度**：缓存使得页面加载更加迅速，提升了用户体验。

**缺点**：

1. **缓存失效问题**：如果资源有更改，而缓存未及时更新，可能导致用户获取到滞后的信息。

### 五、缓存策略优化

为了充分发挥浏览器缓存的优势并避免其缺点，可以采取以下策略进行优化：

1. **根据资源性质设置缓存**：对于频繁变动的资源，可以设置较短的缓存时间或采用协商缓存策略；对于长期不变的资源，可以设置较长的缓存时间或采用强制缓存策略。
2. **定期清理缓存**：用户可能需要手动清理缓存以解决缓存导致的显示问题。同时，开发者也可以考虑在资源更新时通过改变资源名称或路径等方式来避免缓存干扰。
3. **使用 CDN 加速**：CDN 通过将资源分发到全球各地的服务器节点，缩短了用户与服务器之间的距离，提升了资源加载速度。同时，CDN 通常会自动处理缓存管理，进一步提升了网页加载速度。

综上所述，浏览器缓存机制是提高网页加载速度和用户体验的重要手段。通过合理设置缓存头部信息、优化缓存策略以及使用 CDN 加速等措施，可以充分发挥缓存的优势并避免其缺点。