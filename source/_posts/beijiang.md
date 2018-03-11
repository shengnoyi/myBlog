---
title: course
date: 2017-11-11 23:51:09
tags: Javascript
---

# 北江教程
大家好！
在[开发 | 适用场景广，表单收集类小程序开发案例复盘（上）](https://mp.weixin.qq.com/s/pwvxoFGHsn2qjkE_q3faJw)中，介绍了「北江纺织牛仔新时尚」的产品逻辑和数据库的设计思路，那么今天我们来探索一下「北江纺织牛仔新时尚」主要功能点是怎么使用知晓云在小程序中实现的。

### 搜索功能
在「北江纺织牛仔新时尚」中，搜索是比较基础的功能，其实它就是一个查询数据的过程。
我们从一个搜索的实例去讲一下这个问题，假如我们是一个消费者，想要搜索一个裤型修身的、水洗颜色浅色的、成分全棉的童装牛仔长裤，那么我们应该怎么做这个查询呢？
![](https://cloud-minapp-1.cloud.ifanrusercontent.com/1eESEoVDhdAabNDO.jpg)
看上去有点复杂，那么我们先简化一下，如何搜索童装牛仔长裤的信息：
首先，我们需要有这样一张数据表，存储了商品的信息，也就是上一篇中我们讲过的 `product` 表，由于我们要搜索童装牛仔长裤，那么除了一个商品的其他基本信息外，我们肯定要为童装牛仔长裤设定一个字段用于我们的查询，我们把这个字段定义为 `category_id`。根据实际的业务场景，一个产品可能会属于很多不同的分类，所以我们把 `category_id` 设定为数组类型。
现在我们的数据表定义已经初步完成了，那么假设`product` 的 `table_id` 是 `2`, 童装的`category_id`是 `3`，牛仔长裤的 `category_id`是 `5`，那么在小程序端，参考 [知晓云的文档](https://doc.minapp.com/schema/query.html) 去获取所有童装牛仔长裤：
``` bash javascript
  const TABLE_ID_PRODUCT = '2'
  const KIDS_CLOTH_CATEGORY_ID = '3'
  const JEANS_CLOTH_CATEGORY_ID = '5'

  let Product = new wx.BaaS.TableObject(TABLE_ID_PRODUCT)
  let query = new wx.BaaS.Query()

  // 设置类别为童装牛仔长裤
  query.in('category_id', [KIDS_CLOTH_CATEGORY_ID, JEANS_CLOTH_CATEGORY_ID])

  Product.setQuery(query).find().then( (res) => {
    // 获取商品成功
  }, (err) => {
    // 获取商品失败，需做错误处理
  })
```
好的，现在搜索童装牛仔长裤的问题解决，会到之前那个更复杂的问题，搜索一个裤型修身的、水洗颜色浅色的、成分全棉的童装牛仔长裤，其实就是往数据表上分别加上裤型、水洗颜色、成分的相关字段，然后在小程序端使用组合查询去把这些查询条件合并起来，嗯，那么我们现在尝试查询所有水洗颜色浅色的童装牛仔长裤：
``` bash javascript
  const TABLE_ID_PRODUCT = '2'
  const KIDS_CLOTH_CATEGORY_ID = '3'
  const JEANS_CLOTH_CATEGORY_ID = '5'

  let targetColor = '浅'
  let Product = new wx.BaaS.TableObject(TABLE_ID_PRODUCT)
  let query1 = new wx.BaaS.Query()
  let query2 = new wx.BaaS.Query()

  // 设置类别为童装牛仔长裤
  query1.in('category_id', [KIDS_CLOTH_CATEGORY_ID, JEANS_CLOTH_CATEGORY_ID])
  query2.compare('color', '=', targetColor)

  // 使用组合查询 and
  let andQuery = wx.BaaS.Query.and(query1, query2)

  Product.setQuery(andQuery).find().then( (res) => {
    // 获取商品成功
  }, (err) => {
    // 获取商品失败，需做错误处理
  })
```
上面讲了根据筛选条件进行查询，那么做到搜索功能呢？比如我输入'童装'，点击搜索，然后就返回所有童装的商品列表，这里有一种比较简单的做法是给商品表定义一个 `keyword` 数组类型字段用于这种查询，在用户点击搜索后，把用户输入的 '童装' 作为查询条件添加到查询中，那么我们就会得到一个搜索结果列表。

数据查询到这里基本就展示完成了，关键是如何结合你的业务需求去一步步完善你的数据表的设计，然后才是使用合适的查询条件去获取数据。对于新手来说，设计出既能满足业务需求又简单优雅的数据表可能是一大挑战，这个只能靠不断实践和经验去完成了。

### 购物车
在「北江纺织牛仔新时尚」进入商品详情页，我们可以选择把商品添加到自己到购物车中，点击购物车，我们就会跳转到购物车页，可以选择下单，那么这个购物车功能是怎么实现的呢？
![](https://mmbiz.qpic.cn/mmbiz_gif/dzvQgHlKewxOcsypjmkl9ltOibTqdWTrI0mOBKeAYFUnqYTic7t17fqyoqJeiaAqictB5MjYibfd3tfz4tO9WicEER3A/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

首先，需要说明，在「北江纺织牛仔新时尚」为购物过程中的信息存储添加了如下几个数据表：
`product_sku`, 存储了某个商品的相应副产品（面料，挂卡）；
`order_item`, 存储了添加购物车中 `product_sku` 数量的信息；
`order`, 存储了某个订单中的 `order_item` 列表，购物快照，地址等信息；

在商品详情页中，我们根据页面的商品 id，可以在之前提到的 `product`表获取它的信息用于展示，在表 `product_sku` 中查询它的相关副产品（面料，挂卡），在 `order_item` 表中查询用户之前的购物车信息，这些都是我们用于生成购物车内容的基础信息，在用户选择产品的数量并点击添加到购物车之后，我们会向 `order_item`新增或者更新相应的数据项，这些数据项其实就代表着最新的购物车信息。

点击商品详情页中的购物车图标，会直接跳转到购物车页，用户可以在这里选择和修改 `order_item` 的相关信息，在这个过程中，如果`order_item` 的相关信息，需要发送更新请求去更新数据库中的信息。
点击「下单」时，我们将会根据本次被选择的最新 `order_item` 的数据项和之前用户登记留下的个人信息等信息生成一条 `order` 记录，这条记录就可以用于北江纺织与用户的联系沟通，去完成他们的线下订单。

好了，购物车功能的实现思路基本上就是如此，可能没有搜索功能讲的那么详细，但是关键点仍然是在搜索功能中所说的，如何根据业务需求去设计合适的数据表和表结构，完成相应业务，这个可能真的只能靠实践和经验去完善了。