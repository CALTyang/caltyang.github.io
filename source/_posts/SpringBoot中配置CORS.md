---
title: SpringBoot中配置CORS
date: 2016-03-01 19:33:30
tags: Java
---
。本文记录SpringBoot中配置CORS的过程
<!--more-->
## 注解
***
。直接使用 @CrossOrigin 注解即可，配置 origins 来指定允许的域
```java
@CrossOrigin(origins = {
        "http://localhost:8080"
})
@RequestMapping("/test")
public String test(){
    System.out.println("test controller ...");
    return "test";
}
```

## 过滤器
***
。我们知道，CORS进行请求的时候，会多发一次OPTION请求，用来判断是否允许，而判断要求是通过HTTP头来进行的
。所以我们可以配置一个Filter，来添加请求头内容
```java
@Component
public class CORSFilter implements Filter {
    private static List<String> allowOrigins = new ArrayList<>();
    static {
        allowOrigins = new ArrayList<>();
        allowOrigins.add("http://localhost:8080");
    }
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) res;
        String refer = request.getHeader("Referer");
        if (StringUtils.isEmpty(refer)) {
            filterChain.doFilter(req, res);
            return;
        }
        URL url = new URL(refer);
        String origin = url.getProtocol() + "://" + url.getHost() + ":" + url.getPort();
        if (allowOrigins.contains(origin)) {
            response.setHeader("Access-Control-Allow-Origin", origin);
        }
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, PUT,OPTIONS, DELETE");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");
        response.setHeader("Access-Control-Allow-Credentials", "true");
        filterChain.doFilter(req, res);
    }
    @Override
    public void destroy() {
    }
}
```
。因为实际场景中可能有多种的请求来源类型，所以可以配置一个数组，来动态判断

## Reference
***
[https://spring.io/guides/gs/rest-service-cors/](https://spring.io/guides/gs/rest-service-cors/)
