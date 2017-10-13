# AJAX

先复习下ajax这部分的知识。 <br />
ajax是基于XMLHttpRequest对象的，在IE5、IE6中则是ActiveXObject。 <br />

- 所以我们先进行能力测试：<br />

```js
var xmlhttp;
if (window.XMLHttpRequest) {
  xmlhttp = new XMLHttpRequest();
} else {
  xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
}
```
- 向服务器进行数据请求： <br />

```js
//xmlhttp.open(method, url, async)
xmlhttp.open("POST", "test.txt", true); //true为异步 false为同步
xmlhttp.send();
```
- 响应来自服务器的数据：<br />

```js
xmlhttp.onreadystatechange () {
  if (readyState === 4 && state === 200) {
    // 字符串数据响应
    document.getElementsByClassName("getMessage").innerHTML = xmlhttp.responseText;
    // XML数据响应
    var XMLData = xmlhttp.responseXML;
    // 然后再获取需要的数据
  }
}
```
# AJAX进阶

## 阻塞、非阻塞 & 同步、异步

从知乎上看见这段，蛮有意思的：<br />

`老张爱喝茶，废话不说，煮开水。出场人物：老张，水壶两把（普通水壶，简称水壶；会响的水壶，简称响水壶）。
1 老张把水壶放到火上，立等水开。（同步阻塞）老张觉得自己有点傻
2 老张把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。（同步非阻塞）老张还是觉得自己有点傻，于是变高端了，买了把会响笛的那种水壶。水开之后，能大声发出嘀~~~~的噪音。
3 老张把响水壶放到火上，立等水开。（异步阻塞）老张觉得这样傻等意义不大
4 老张把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。（异步非阻塞）老张觉得自己聪明了。` <br />
`链接：https://www.zhihu.com/question/19732473/answer/23434554` <br />

所谓阻塞、非阻塞，是对当前线程来讲的；同步和异步，是对调用者来讲的。 <br />



# axios

#### 使用

```
npm install axios
```
- GET请求

```js
aixos.get('url')
  .then(function (response) {
    //...
  })
  .catch(function (error) {
    //...
  })
```
- POST请求

```js
axios.post('url',{
  ValueA: 'a',
  ValueB: 'b'
  // ...
})
  .then(function (response) {
    //...
  })
  .catch(function (error) {
    //...
  })
```

- get()和post()的区别


















