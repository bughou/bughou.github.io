---
title: "Pgpool-II的相关疑惑"
date: 2019-09-06T18:19:16+08:00
---

Q: 为啥需要"pool\_passwd"这么个东西？为啥不能拿着前端的密码直接去后端认证？  
A: 1. 前后端的密码认证是加了随机盐的，参考[AuthenticationMD5Password](https://www.postgresql.org/docs/10/protocol-flow.html#id-1.10.5.7.3)。要连接多个后端时，盐不一样，不能直接将前端输入传给后端。  
　 2. 复用已缓存的连接时，不能拿着前端的密码去后端再认证一遍。（或许可以将已缓存的连接的密码也缓存着？）  
　 3. 后端Postgresql每次去认证都会fork一个新进程，如果认证失败，开销太大？  
　 4. Pgpool-II 4.0以后，支持前端trust，后端密码认证，这样就拿不到密码了。  
　 5. 如果开启了健康检查，需要用到密码。  
