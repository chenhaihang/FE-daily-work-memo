# vue作为主项目，react作为子项目，用nginx部署出现的问题

1. 要注意的点entry 如果和主应用所属的端口不一致的话就会有跨域的问题 比如localhost:3000/react-app
2. activeRule前面的micro-vue 是主应用的基础路径，然后再其中跳转至/react-app这个子路由，我觉得是可以把前面的主路径给去除掉的

``` javascript
registerMicroApps([
    {
      name: 'react-app',
      entry: '//localhost/react-app',
      container: '#micro-container',
      // 肯定有更好的方法，但这个确实是能实现的
      activeRule: 'micro-vue/#/react-app'
    }
  ]);
```
3. nginx 配置如下，将两个应用配置在一个端口之下，如果配置在两个端口下有跨域的问题，但是最终没有解决。
``` nginx
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        location ^~ /micro-vue {
            try_files $uri $uri/ /micro-vue/index.html;
        }
        location ^~ /react-app {
            try_files $uri $uri/ /react-app/index.html;
        }
    }
```
