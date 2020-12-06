# 1.中文乱码过滤器

public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

```java
	response.setContentType("text/html;charset=utf-8");
	response.setCharacterEncoding("utf-8");
	request.setCharacterEncoding("utf-8");
	
	chain.doFilter(request, response);
}

```
# 2.js错误过滤器

```java
<jsp-config>
  	<jsp-property-group>
      <display-name>JsConfiguration</display-name>
      <url-pattern>*.js</url-pattern>
      <page-encoding>UTF-8</page-encoding>
    </jsp-property-group>
  </jsp-config>
```

