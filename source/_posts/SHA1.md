---
title: SHA1算法的猜想
date: 2017-03-2 18:11:19
tags: 网络基础
---

## SHA1算法的猜想
参考资料：[如何评价 2 月 23 日谷歌宣布实现了 SHA-1 碰撞？](https://www.zhihu.com/question/56234281)
原本一直以为是使用SHA1算法对密码进行加密，这样hacker即使获得了密文，也没有办法进行破解，但在看了这个文章后发现我是错的
### SHA1的正确用法猜想
以用户的注册和登录为例，在注册的时候，客户端发送明文密码，服务器使用SHA1算法生成一个数据摘要b，并存储在数据库中。当需要登录的时候，客户端发送一个密码过来，服务器再次调用SHA1密码，生成另一个数据摘要b1,比对b1和b是否一致，如果一致则说明密码是正确的。
### 在服务端加密
如果服务器被人攻击了，密码数据丢失，但是由于存储的只是摘要，而通过摘要是没有办法（在SHA1没有被破解之前）反向得到原文的，那么hacker仍没有办法获取用户的其他数据，
### 在客户端加密
如果是在客户端进行加密，加密后把密文发到服务器直接比对是否一致，这样如果服务器被攻陷了，那么密码也被丢失了，跟明文存储没有区别了，这样使用SHA1算法就没有意义了。