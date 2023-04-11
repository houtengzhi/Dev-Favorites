# IJKPlayer优化记录 (一)重定向问题

#### 问题
最近尝试使用IJKPlayer(k0.8.8，目前最新的版本)，在播放HLS协议的直播流时报I/O error
```
04-26 13:55:25.758 3848-31142/com.ahgx.dvb E/IJKMEDIA: (http://10.88.201.114:11001/LIVE_1463896291340/playlist.m3u8?serviceType=LIVE): I/O error
```
定位到`http.c`文件中的方法`http_connect`每次请求返回的http_code为301，Moved Permanently，意为被请求的资源已移到新的地址，同时新地址由响应的location域返回，此时客户端会再次发出请求。处理重定向的相关代码在`http_open_cnx`方法中：
```c
if ((s->http_code == 301 || s->http_code == 302 ||
         s->http_code == 303 || s->http_code == 307) &&
        location_changed == 1) {
        /* url moved, get next */
        ffurl_closep(&s->hd);
        if (redirects++ >= MAX_REDIRECTS)
            return AVERROR(EIO);
        /* Restart the authentication process with the new target, which
         * might use a different auth mechanism. */
        memset(&s->auth_state, 0, sizeof(s->auth_state));
        attempts         = 0;
        location_changed = 0;
        goto redo;
    }
```
播放失败的log中显示请求头中的host是重定向的新url，但通过抓包发现还是在向旧的url发送请求，所以导致不断返回301然后失败。

#### 解决方法
这是由于IJKPlayer中默认使用了dns cache。在发送请求头之前需要建立tcp连接，`tcp.c`文件中`tcp_open`方法中相关代码
```c
if (s->dns_cache_timeout > 0) {
        memcpy(hostname_bak, hoststr, 1024);
		av_log(NULL, AV_LOG_DEBUG, "[tcp.c] dns_cache_clear: %d", s->dns_cache_clear);
        if (s->dns_cache_clear) {
            av_log(NULL, AV_LOG_INFO, "[tcp.c] will delete cache entry, hoststr = %s\n", hostname_bak);
            remove_dns_cache_entry(hostname_bak);
        } else {
            dns_entry = get_dns_cache_reference(hostname_bak);
        }
    }
```
**缓存的地址信息中包含host和port，而只用hostname作为key新增或查询dns cache，当重定向的url与之前地址host相同port不同时就导致继续使用了旧的dns cache。**

  所以在添加dns cache时应该用hostname+port作为key，可参考[zhanggao提的问题ijkplayer/issues/3700](https://github.com/Bilibili/ijkplayer/issues/3700)。

或者干脆不使用dns cache，
```java
mMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "dns_cache_clear", 1);
```
这设置有个新问题，第一次请求时dns_cache_clear值的确为1，但后来又变为0，还得研究研究。