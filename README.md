#Requests

Requests is a http request lib for human beings who use java inspired by python requests module.
The Httpclient lib is greate, but has too complex API, which confuse beginners. Requests build simple and flexible api, both for common and advanced Usage.

#User Guide
##Get Requests
Requests is now in maven central repo.
```xml
<dependency>
    <groupId>net.dongliu</groupId>
    <artifactId>requests</artifactId>
    <version>1.8.0</version>
</dependency>
```
##Make request
Simple example that do http get request:
```java
String url = ...;
Response<String> resp = Requests.get(url).text();
```
Post and other method:
```java
resp = Requests.post(url).text();
resp = Requests.head(url).text();
resp = Requests.delete(url).text();
resp = Requests.options(url).text();
```
##Response Content
The response object have common http response field to be used:
```java
Response<String> resp = Requests.get(url).text();
int statusCode = resp.getStatusCode();
Headers headers = resp.getHeaders();
Cookies cookies = resp.getCookies();
String body = resp.getBody();
```
The text() method here trans http response body as String, there are other method to process http response:
```java
// get response as string, and use provided encoding
Response<String> resp = Requests.get(url).text("UTF-8");
// get response as bytes
Response<byte[]> resp1 = Requests.get(url).bytes();
// save response as file 
Response<File> resp2 = Requests.get(url).file("/path/to/save/file");
```
or you can custom http response processor your self:
```java
Response<Json> resp1 = Requests.get(url).client(new ResponseProcessor<Json>() {
    @Override
    public Json convert(int statusCode, Headers headers, HttpEntity httpEntity) throws IOException {
        return null;
    }
});
```
##Passing Parameters
You can pass parameters in urls use param or params method:
```java
Map<String, Object> params = new HashMap<>();
params.put("k1", "v1");
params.put("k2", "v2");
Response<String> resp = Requests.get(url).param("key1", "value1").params(params).text();
```
##Custom Headers
Http request headers can be set by header or headers method:
```java
Map<String, Object> headers = new HashMap<>();
headers.put("k1", "v1");
headers.put("k2", "v2");
Response<String> resp = Requests.get(url).header("key1", "value1").headers(headers).text();
```
##UserAgent
A convenient method to set userAgent:
```java
Response<String> resp = Requests.get(url).userAgent(userAgent).text();
```
##Request with data
Http Post, Put, Patch method can send request bodys. Take Post for example:
```java
Map<String, Object> formData = new HashMap<>();
formData.put("k1", "v1");
formData.put("k2", "v2");
// send form-encoded data. x-www-form-urlencoded header will be send automatically
Response<String> resp = Requests.post(url).data(formData).text();
// send string data
String str = ...;
resp = Requests.post(url).data(str, "UTF-8").text();
// send from inputStream
InputStream in = ...
resp = Requests.post(url).data(in).text();
```
One more complicate situation is multiPart post request, this can be done via multiPart method:
```java
Map<String, Object> formData = new HashMap<>();
formData.put("k1", "v1");
formData.put("k2", "v2");
// send form-encoded data
Response<String> resp = Requests.post(url).data(formData).multiPart("ufile", "/path/to/file")
        .multiPart(..., ...).text();
```
###Redirection
Requests will handle 301/302 http redirect, you can get redirect history:
```java
Response<String> resp = Requests.get(url).text();
List<URI> history = resp.getHistory();
```
Or you can disable automatic redirect and handle it yourself:
```java
Response<String> resp = Requests.get(url).allowRedirects(false).text();
```
## Timeout
There are two timeout parameters you can set, connect timeout, and socket timeout. The timeout value default to 10_1000 milliseconds.
```java
// both connec timeout, and socket timeout
Response<String> resp = Requests.get(url).timeout(30_1000).text();
// set connect timeout and socket timeout
resp = Requests.get(url).timeout(30_1000, 30_1000).text();
```
You may not need to know, but Requests also use connect timeout as the timeout value get connection from connection pool if connection pool is used.
##Gzip
