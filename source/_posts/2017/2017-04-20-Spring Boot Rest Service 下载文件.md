---
layout: post
title: Spring Boot Rest Service 下载文件
category: Spring Boot 
tags: [Spring Boot]
---

>download a file from Spring boot rest service

## 前言

 这次总结一下文件下载，一般的文件下载是没问题的，设置一下HttpServletResponse,直接通过浏览器来下载。
 
`resp.setContentType("application/x-msdownload");`
`resp.setHeader("Content-Disposition", "attachment;filename=\"" + filename + "\"");`

开发中，需要从后端服务器下载创建的Excel文件。项目前后端是分离的，这个比较麻烦，不知道前后端怎么交互了。

之前想到的一种方案是：先创建成功Excel，通过输出流写到服务器一个固定路径下面，通过超链接来下载，这个是没问题的。这种方案是需要额外提供一个文件服务器，这个就比较麻烦了。

最好的方案是直接把文件以流的形式输出到浏览器。

在前端架构大神的帮助下总算解决了，直接上代码。。。

## 后端代码

### 创建Excel
Apache提供的POI包可以生成Excel,看官方提供的[示例](http://poi.apache.org/spreadsheet/converting.html)。这里创建个简单的Excel文件。

```java
	public static Workbook createExcel() {
		//new HSSFWorkbook() make xls
		//new XSSFWorkbook() make xlsx
		Workbook xlsx = new XSSFWorkbook();

		Sheet sheet = xlsx.createSheet();
		Row row = sheet.createRow(1);
		Cell cell0 = row.createCell(0);
		cell0.setCellValue("0");
		Cell cell1 = row.createCell(1);
		cell1.setCellValue("1");
		Cell cell2 = row.createCell(2);
		cell2.setCellValue("2");

		return xlsx;
	}
```

### 下载

```java
@RestController
@RequestMapping("/download")
public class DownloadController {

	@RequestMapping(value = "/file", method = RequestMethod.GET)
	public ResponseEntity<Resource> downloadFile() {
		ByteArrayOutputStream bos = null;
		String filename = "测试.xlsx";
		try {
			Workbook workbook = createExcel();
			bos = new ByteArrayOutputStream();
			workbook.write(bos);
			workbook.close();

			HttpHeaders headers = new HttpHeaders();
			headers.add("Cache-Control", "no-cache, no-store, must-revalidate");
			headers.add("Pragma", "no-cache");
			headers.add("Expires", "0");
			headers.add("charset", "utf-8");
			//设置下载文件名
			filename = URLEncoder.encode(filename, "UTF-8");
			headers.add("Content-Disposition", "attachment;filename=\"" + filename + "\"");

			Resource resource = new InputStreamResource(new ByteArrayInputStream(bos.toByteArray()));

			return ResponseEntity.ok().headers(headers).contentType(MediaType.parseMediaType("application/x-msdownload")).body(resource);

		} catch (IOException e) {
			if (null != bos) {
				try {
					bos.close();
				} catch (IOException e1) {
					e1.printStackTrace();
				}
			}
		}
		return null;
	}
}
```

## 前端代码

```javascript

$scope.download = function () {
    $http({
        method: 'GET',
        url: 'http://localhost:9090/download/file',
        params: { name: name },
        responseType: 'arraybuffer'
    }).then(function (result) {
        var linkElement = document.createElement('a');
        try {
            var blob = new Blob([result.data], { type: 'application/octet-stream' });
            var url = window.URL.createObjectURL(blob);
            linkElement.setAttribute('href', url);
            linkElement.setAttribute("download", 'filename');
            var clickEvent = new MouseEvent("click", {
                "view": window,
                "bubbles": true,
                "cancelable": false
            });
            linkElement.dispatchEvent(clickEvent);
        } catch (ex) {
            console.log(ex);
        }
    });
}

```

## 测试

接口测试，直接通过浏览器访问`http://localhost:9090/download/file`。

![这里写图片描述](http://img.blog.csdn.net/20170420120227020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



这次也是涨姿势了。。。总结一下留着备用，希望能帮到有需要的同学。

## 参考
[download a file from Spring boot rest service](http://stackoverflow.com/questions/35680932/download-a-file-from-spring-boot-rest-service)
[Return file from Spring @Controller having OutputStream](http://stackoverflow.com/questions/27741283/return-file-from-spring-controller-having-outputstream)
[Problems using @Consumes(MediaType.APPLICATION_JSON) in RESTful web service](http://stackoverflow.com/questions/31105142/problems-using-consumesmediatype-application-json-in-restful-web-service)