---
layout: post
title: Spring-RestTemplate实战
category: Spring 
tags: [Spring]
---
>The RestTemplate is the core class for client-side access to RESTful services. It is conceptually similar to other template classes in Spring, such as JdbcTemplate and JmsTemplate and other template classes found in other Spring portfolio projects.

## 前言

这里总结一下，怎么用代码发起HTTP请求：Post、Get等。之前类似的请求都是用Postman。RestTemplate网上教程也是很多，但是编程就是要多实战，可能受制于版本等各种客观因素，同样的教程实战可能会有不同的结果。

对于实现HTTP请求有多种类库，目前用过`RestTemplate `和`HttpComponents`，以`Post`方法为例，创建以下服务：

```
	@RequestMapping(value = "/selectPost", method = RequestMethod.POST)
	public ModelMap selectPost(String username, String password) {
		List<User> userList = userService.selectPost(username, password);

		return result(Constant.SUCCESS_CODE, Constant.SUCCESS_MSG, userList);
	}
```

## RestTemplate

`RestTemplate`支持以下6六种HTTP请求方法。这些请求方法是定义在`RestOperations`接口中的，`RestTemplate`提供实现。

![这里写图片描述](http://img.blog.csdn.net/20170627111857187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 代码示例

```
	@Test
	public void selectPost1() {
		//参数
		String url = "http://localhost:9091/user/selectPost";
		String username = "admin";
		String password = "1212";
		
		MultiValueMap<String, Object> map = new LinkedMultiValueMap<>();
		map.add("username", username);
		map.add("password", password);
		//设置响应头
		HttpHeaders headers = new HttpHeaders();
		headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
		//封装参数响应头
		HttpEntity<MultiValueMap<String, Object>> entity = new HttpEntity<>(map, headers);


		RestTemplate template = new RestTemplate();
		//执行请求，并接受返回结果
		ResponseEntity<String> result = template.exchange(url, HttpMethod.POST, entity, String.class);
		JSONObject jsonObject = JSONObject.fromString(result.getBody());
		System.out.println("===========selectPost1===============");

		System.out.println(jsonObject);

	}
```

### 结果

#### 返回字符串

```
{"msg":"SUCCESS","result":[{"password":"1212","createTime":"2017-38-14 07:38:05","updateUser":"admin","createUser":"admin","updateTime":"2017-38-14 07:38:05","id":3,"username":"admin"},{"password":"1212","createTime":"2017-39-14 07:39:47","updateUser":"admin","createUser":"admin","updateTime":"2017-39-14 07:39:47","id":4,"username":"admin"}],"code":"200"}
```

#### 返回对象

通常返回一个字符串在项目中用处不大，如果能够直接返回相应的实体，那就更好了。

创建返回值对应的对象

```
public class UserVo {
	private String code;
	private String msg;
	private List<User> result;
	}
```
以对象的类型接受返回值

`UserVo result = template.postForObject(url, entity, UserVo.class);`

返回结果：

```
UserVo{code='200', msg='SUCCESS', result=[User{id=3, username='admin', password='1212', createUser='admin', createTime=Sat Jan 14 15:38:05 CST 2017, updateUser='admin', updateTime=Sat Jan 14 15:38:05 CST 2017}, User{id=4, username='admin', password='1212', createUser='admin', createTime=Sat Jan 14 15:39:47 CST 2017, updateUser='admin', updateTime=Sat Jan 14 15:39:47 CST 2017}]}
```

## HttpComponents

`HttpComponents`是Apache下的一个项目，目前最新的版本是`4.5.3`。
需要说明一下，`4.3.6`中提供的HttpPost是非线程安全的。

![这里写图片描述](http://img.blog.csdn.net/20170627154544455?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 代码示例

```
	@Test
	public void selectPost3() {
		//参数
		String url = "http://localhost:9091/user/selectPost";
		String username = "admin";
		String password = "1212";

		try {
			//初始化
			CloseableHttpClient httpClient = HttpClients.createDefault();
			CloseableHttpResponse httpResponse = null;
			HttpPost httpPost = new HttpPost(url);

			//请求参数
			List<NameValuePair> nameValuePairs = new ArrayList<>();
			nameValuePairs.add(new BasicNameValuePair("username", username));
			nameValuePairs.add(new BasicNameValuePair("password", password));

			//编码 封装参数
			UrlEncodedFormEntity formEntity = new UrlEncodedFormEntity(nameValuePairs, "utf-8");
			httpPost.setEntity(formEntity);
			httpPost.addHeader("Content-Type", "application/x-www-form-urlencoded");

			//执行请求
			httpResponse = httpClient.execute(httpPost);

			//返回结果
			org.apache.http.HttpEntity respHttpEntity = httpResponse.getEntity();
			String resultStr = EntityUtils.toString(respHttpEntity, "utf-8");

			System.out.println(resultStr);

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```

### 结果

```
{"code":"200","msg":"SUCCESS","result":[{"id":3,"username":"admin","password":"1212","createUser":"admin","createTime":"2017-38-14 07:38:05","updateUser":"admin","updateTime":"2017-38-14 07:38:05"},{"id":4,"username":"admin","password":"1212","createUser":"admin","createTime":"2017-39-14 07:39:47","updateUser":"admin","updateTime":"2017-39-14 07:39:47"}]}
```

### 扩展

`HttpComponents 结构`：

 - HttpComponents Core 是一组低级的HTTP传输组件，支持2中I/O模型:基于经典Java I/O的阻塞I/O和非阻塞I/O。
 - HttpComponents Client 是一个基于HttpCore的符合HTTP/1.1标准的HTTP代理实现。
 - HttpComponents AsyncClient 是一个基于HttpCore NIO的符合HTTP/1.1标准的HTTP代理实现和HttpClient组件。

## 参考

对于学习编程的态度一贯是多实战，实战才能发现问题。以上只是简单实现了一下，有些内容深入不够，后续还得继续深入了解。

[Class RestTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)
[28.10 Accessing RESTful services on the Client](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#rest-client-access)
[Spring RestTemplate介绍](http://www.cnblogs.com/softidea/p/5977375.html)
[Apache HttpComponents](http://hc.apache.org/index.html)
