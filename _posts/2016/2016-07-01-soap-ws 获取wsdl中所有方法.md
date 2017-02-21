---
layout: post
title: soap-ws 获取wsdl中所有方法
category: WebService 
tags: [WebService]
---

soap-ws ,A lightweight and easy-to-use Java library to handle SOAP on a purely XML level.

## 前言

soap-ws Java开源框架,github上是这样解释的：
`soap-ws ,A lightweight and easy-to-use Java library to handle SOAP on a purely XML level.`
github列举了一些使用方法，地址:https://github.com/reficio/soap-ws。

   在使用的过程中，需要通过wsdl地址获取到wsdl中定义的所有方法，也就是operation。wsdl中定义Operation的地方有2个：①portType节点；②binding节点。
一般来说，这2个节点的operation是完全一样的。不过通过SOAPUI访问wsdl,获取的是binding节点中的所有operation。如果2个节点的operation都一样获取哪个节点都可以。

## 1. 获取binding节点的所有operation
看了一下示例代码，
soap-ws是通过以下的方法获取的operation。
`SoapOperation operation = builder.operation().soapAction("http://www.webserviceX.NET/ConversionRate").find(); `
上述方法必须要知道Action的定义,大部分wsld中的Operation都是没有定义action的。所以通过以上方法不行。
查看接口SoapBuilder，定义了获取operation的方法，获取的是binding节点的所有operation。
    `public interface SoapBuilder {List<SoapOperation> getOperations();}`
那通过wsdl怎么用到接口SoapBuilder的getOperations()呢？
参考示例` SoapBuilder builder = *wsdl.binding()*.localPart("CurrencyConvertorSoap").find();`
类Wsdl中提供了获取所有binding的方法`public List<QName> getBindings()`
完整代码如下：
``` 
public static List<String> getBindingOperations(String wsdlUrl) {
    List<String> operationList = new ArrayList();
    List<SoapOperation> soapOperationList = new ArrayList();
    Wsdl parser = Wsdl.parse(wsdlUrl);
    List<QName> bindQnames = parser.getBindings();
    for (QName qName : bindQnames) {
        SoapBuilder soapBuilder = parser.binding().localPart(qName.getLocalPart()).find();
        soapOperationList.addAll(soapBuilder.getOperations());
    }
    for (SoapOperation soapOperation : soapOperationList) {
        operationList.add(soapOperation.getOperationName());
    }
    return operationList;
}
```
以获取天气为例，测试结果：
wsdlUrl = "http://www.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl";
```
[getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro]

```

### 2. 获取portType节点的所有operation
获取portType节点的operation比较麻烦，soap-ws没有提供直接可以使用的接口。
不过可以参考soap-ws的源码写一个方法。
soap-ws提供了一些example。参考例子：
soap-ws\soap-examples\quickstart\src\test\java\org\reficio\ws\quickstart\SoapClientExamplesTest.java
在IntelliJ IDEA中debug，eclipse中看起来不舒服。
![这里写图片描述](http://img.blog.csdn.net/20160701111457643)

可以看到在wsdl的私有成员soapFacade中有portType节点，binding节点信息。这些信息最终是定义在messageBuilder的私有变量definition中的，按照这个路径查看源代码。查看SoapMessageBuilder的构造方法。

```
    public SoapMessageBuilder(URL wsdlUrl) throws WSDLException {
        WSDLReader reader = new WSDLReaderImpl();
        reader.setFeature("javax.wsdl.verbose", false);
        this.definition = reader.readWSDL(wsdlUrl.toString());
        this.definitionWrapper = new SchemaDefinitionWrapper(definition, wsdlUrl.toString());
    }
```
根据上面的代码进行改造。完整的代码如下：
```
    private static List<Operation> getPortTypeOperations(String wsdlUrl) {
        List<Operation> operationList = new ArrayList();
        try {
            WSDLReader reader = new WSDLReaderImpl();
            reader.setFeature("javax.wsdl.verbose", false);
            Definition definition = reader.readWSDL(wsdlUrl.toString());
            Map<String, PortTypeImpl> defMap = definition.getAllPortTypes();
            Collection<PortTypeImpl> collection = defMap.values();
            for (PortTypeImpl portType : collection) {
                operationList.addAll(portType.getOperations());
            }
        } catch (WSDLException e) {
            System.out.println("get wsdl operation fail.");
            e.printStackTrace();
        }
        return operationList;
    }
```
获取operation的名字

```
    public static List<String> getOperationByUrl(String wsdlUrl) {
        List<String> resultList = new ArrayList<>();
        List<Operation> operationList = getPortTypeOperations(wsdlUrl);
        for (Operation operation : operationList) {
            resultList.add(operation.getName());
        }
        return resultList;
    }
```


上面的方法也可以获取Binding节点的operation。代码如下
```
    public static List<String> getAllBindingOperation(String wsdlUrl) {
        List<BindingOperation> operationList = new ArrayList();
        List<String> nameList = new ArrayList();
        try {
            WSDLReader reader = new WSDLReaderImpl();
            reader.setFeature("javax.wsdl.verbose", false);
            Definition definition = reader.readWSDL(wsdlUrl.toString());
            Map<String, BindingImpl> defMap = definition.getAllBindings();
            Collection<BindingImpl> collection = defMap.values();
            for (BindingImpl binding : collection) {
                operationList.addAll(binding.getBindingOperations());
            }
            for (BindingOperation operation:operationList) {
                nameList.add(operation.getName());
            }
        } catch (WSDLException e) {
            System.out.println("get wsdl operation fail.");
            e.printStackTrace();
        }
        return nameList;
    }
```


### 3. 两种方法比较
测试方法

```
    @Test
    public void test2() {
        String wsdlUrl = "http://www.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl";
        long s1=System.currentTimeMillis();
        System.out.println(getBindingOperations(wsdlUrl));
        long e1=System.currentTimeMillis();
        System.out.println("getBindingOperations "+(e1-s1));//1707ms
        long s2=System.currentTimeMillis();
        System.out.println(getOperationByUrl(wsdlUrl));
        long e2=System.currentTimeMillis();
        System.out.println("getOperationByUrl "+(e2-s2));//199ms
        System.out.println(Arrays.equals(getBindingOperations(wsdlUrl).toArray(),getOperationByUrl(wsdlUrl).toArray()));
    }
```

测试结果：

```
[getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro]
getBindingOperations **1123**
[getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro]
getOperationByUrl **35**
false
```

执行时间上差距很大，原因是第一种方法每次都是新建一个对象。

```
    static SoapOperationBuilder create(SoapBuilder builder, Binding binding, BindingOperation operation, String soapAction) {
        String bindingInputName = operation.getBindingInput() != null ? operation.getBindingInput().getName() : null;
        String bindingOutputName = operation.getBindingOutput() != null ? operation.getBindingOutput().getName() : null;
        return new SoapOperationImpl(builder, binding.getQName(), operation.getName(), bindingInputName, bindingOutputName,
                SoapUtils.normalizeSoapAction(soapAction));
    }
```
第二中方法获取到的Binding节点和获取portType节点比较。

```
    @Test
    public void test03(){
        String wsdlUrl = "http://www.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl";
        long s1=System.currentTimeMillis();
        System.out.println(getAllBindingOperation(wsdlUrl));
        long e1=System.currentTimeMillis();
        System.out.println("getAllBindingOperation "+(e1-s1));//480ms
        long s2=System.currentTimeMillis();
        System.out.println(getOperationByUrl(wsdlUrl));
        long e2=System.currentTimeMillis();
        System.out.println("getOperationByUrl "+(e2-s2));//281ms
        System.out.println(Arrays.equals(getAllBindingOperation(wsdlUrl).toArray(),getOperationByUrl(wsdlUrl).toArray()));
    }
```

测试结果

```
[getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro]
getAllBindingOperation 265
[getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro, getSupportCity, getSupportProvince, getSupportDataSet, getWeatherbyCityName, getWeatherbyCityNamePro]
getOperationByUrl 39
false
```
综上，获取portType节点的operation时间最快。生产环境推荐获取binding节点的信息。