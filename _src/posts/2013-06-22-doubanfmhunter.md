    Title: 豆瓣FM Hunter
    Date: 2013-06-22T00:00:00

前两天在听豆瓣电台，想把听到的歌都下载下来。
于是做了一个火狐浏览器的插件，doubanfmhunter。  


这里介绍一下主要用到的点，完整代码在[https://builder.addons.mozilla.org/package/190551/latest/](https://builder.addons.mozilla.org/package/190551/latest/)。因为用的是firefox的addon builder，所以放到github上倒麻烦，就不放了。  


1. 首先要通过firefox的调试平台看到请求的GET地址，也就是播放的MP3的地址。  

2. 使用events监听http-on-modify-request事件。
当接收到类似1里面监听的url地址时，记录下来该地址。  

3. 搭建一个sae的后台，想下载该MP3时，请求该SAE后台下载该MP3.


遇到一些问题，还没有时间去深入研究，如果路过的人知道答案，告知在下，不胜感激。  

1. 通过调试发现，豆瓣电台应该是先请求一个带playlist的地址，返回一个json，里面有很多的mp3地址，以及专辑信息。
然后再请求一个mp3地址。但是请求的mp3地址并不在返回的json里，所以也不知道他们是怎么选择这个mp3地址的。  

2. api文档里说text-streams是在io下面，也就是require("io/text-streams")。
但是就是编译不通过，后来下载代码发现看类库的代码，才知道写成require("text-streams")。
但是遇到byte-streams时，不管使用require("io/byte-streams")或者require("byte-streams")，都不行，所以最后只能把byte-streams里的ByteReader的代码拿出来，改改用。  

3. 在使用这里的代码去监听baidu的mp3时，总是出现错误，还未深入研究。  

4. 如果想监听http-on-examine-response事件，需要注意一点的是这个事件是在数据到达之前就会触发的。所以这时要写一个listener，在代码里有。  

5. 本来想通过本地直接下载，window.location.href="data:xxx"之类的方式达到下载的目的，可是试了很多方法都没成功。最后只能通过在SAE上建立一应用来达到下载文件的目的。这个是我最想解决的。

