---
title: 最优优惠券的自动匹配和算法的运用
date: 2020-04-18 17:44:00
categories: 
- [frontend,JavaScript] 
- [frontend,算法]
tags: [JavaScript,算法]
---

## 引言
商城开发中会有很多功能需求，其中优惠券模块的开发算是商城中常见且复杂的功能。
## 先决条件
1. 卡券商品只能直接购买不能加入购物车，满足条件可以使用优惠券。
2. 普通商品可以加入购物车购买可以直接购买，满足可以使用优惠券。
## 需求如下：
1. 用户可以领取多张优惠券。
2. 优惠券的类型分为三种(商品，卡券，通用)。
3. 优惠券有使用条件(满减)，或者没有使用条件(现金)。
4. 优惠券可以指定对应的商品可以对应一个也可以对应多个商品。
5. 优惠券有使用时间范围，开始时间和结束时间。
6. 用户在下单页面中自动匹配优惠金额最高的优惠券，如果没有则不匹配。
## 分析
<!-- more -->
光看需求感觉云里雾里，但是仔细分析后用数据结构表示一目了然，后端json数据返回如下：
```json
{
	"code": 1,
	"msg": "操作成功",
	"data": [{
		"couponId": 184535,
		"userId": 26094,
		"couponName": "组合",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 5.00,
		"lessMoney": 4.00,
		"startTime": 1585670400000,
		"overTime": 1588262399000,
		"status": 0,
		"projectId": "505,502,507,512"
	}, {
		"couponId": 184534,
		"userId": 26094,
		"couponName": "组合",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 5.00,
		"lessMoney": 4.00,
		"startTime": 1585670400000,
		"overTime": 1588262399000,
		"status": 0,
		"projectId": "505,502,507,512"
	}, {
		"couponId": 184489,
		"userId": 26094,
		"couponName": "组合卡券专用",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 4.00,
		"lessMoney": 3.00,
		"startTime": 1585843200000,
		"overTime": 1586275199000,
		"status": 0,
		"projectId": "505,502"
	}, {
		"couponId": 184488,
		"userId": 26094,
		"couponName": "组合卡券专用",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 4.00,
		"lessMoney": 3.00,
		"startTime": 1585843200000,
		"overTime": 1586275199000,
		"status": 0,
		"projectId": "505,502"
	}, {
		"couponId": 184487,
		"userId": 26094,
		"couponName": "卡券和商品共用券测试专用",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1586188800000,
		"overTime": 1587571199000,
		"status": 0,
		"projectId": "480,505"
	}, {
		"couponId": 184486,
		"userId": 26094,
		"couponName": "卡券和商品共用券测试专用",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1586188800000,
		"overTime": 1587571199000,
		"status": 0,
		"projectId": "480,505"
	}, {
		"couponId": 184485,
		"userId": 26094,
		"couponName": "卡券和商品共用券测试专用",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1586188800000,
		"overTime": 1587571199000,
		"status": 0,
		"projectId": "480,505"
	}, {
		"couponId": 184484,
		"userId": 26094,
		"couponName": "卡券和商品共用券测试专用",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1586188800000,
		"overTime": 1587571199000,
		"status": 0,
		"projectId": "480,505"
	}, {
		"couponId": 184469,
		"userId": 26094,
		"couponName": "组合卡券专用",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 4.00,
		"lessMoney": 3.00,
		"startTime": 1585843200000,
		"overTime": 1586275199000,
		"status": 0,
		"projectId": "505,502"
	}, {
		"couponId": 184468,
		"userId": 26094,
		"couponName": "组合卡券专用",
		"couponType": "满减",
		"useType": "卡券",
		"limitMoney": 4.00,
		"lessMoney": 3.00,
		"startTime": 1585843200000,
		"overTime": 1586275199000,
		"status": 0,
		"projectId": "505,502"
	}, {
		"couponId": 184467,
		"userId": 26094,
		"couponName": "组合商品专用",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 400.00,
		"lessMoney": 399.00,
		"startTime": 1586241684000,
		"overTime": 1586534390000,
		"status": 0,
		"projectId": "495,496,494,489"
	}, {
		"couponId": 184466,
		"userId": 26094,
		"couponName": "组合商品专用",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 400.00,
		"lessMoney": 399.00,
		"startTime": 1586241682000,
		"overTime": 1586534390000,
		"status": 0,
		"projectId": "495,496,494,489"
	}, {
		"couponId": 184465,
		"userId": 26094,
		"couponName": "组合商品专用",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 400.00,
		"lessMoney": 399.00,
		"startTime": 1586241679000,
		"overTime": 1586534390000,
		"status": 0,
		"projectId": "495,496,494,489"
	}, {
		"couponId": 184464,
		"userId": 26094,
		"couponName": "组合商品专用",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 400.00,
		"lessMoney": 399.00,
		"startTime": 1586241676000,
		"overTime": 1586534390000,
		"status": 0,
		"projectId": "495,496,494,489"
	}, {
		"couponId": 184463,
		"userId": 26094,
		"couponName": "组合商品专用",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 400.00,
		"lessMoney": 399.00,
		"startTime": 1586241674000,
		"overTime": 1586534390000,
		"status": 0,
		"projectId": "495,496,494,489"
	}, {
		"couponId": 184440,
		"userId": 26094,
		"couponName": "指定券004",
		"couponType": "现金",
		"useType": "商品",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1585799170000,
		"overTime": 1586015990000,
		"status": 0,
		"projectId": "474,477"
	}, {
		"couponId": 184439,
		"userId": 26094,
		"couponName": "指定券004",
		"couponType": "现金",
		"useType": "商品",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1585799167000,
		"overTime": 1586015990000,
		"status": 0,
		"projectId": "474,477"
	}, {
		"couponId": 184438,
		"userId": 26094,
		"couponName": "指定券004",
		"couponType": "现金",
		"useType": "商品",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1585799164000,
		"overTime": 1586015990000,
		"status": 0,
		"projectId": "474,477"
	}, {
		"couponId": 184437,
		"userId": 26094,
		"couponName": "指定券004",
		"couponType": "现金",
		"useType": "商品",
		"limitMoney": 0.00,
		"lessMoney": 50.00,
		"startTime": 1585799160000,
		"overTime": 1586015990000,
		"status": 0,
		"projectId": "474,477"
	}, {
		"couponId": 184376,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566434000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184375,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566432000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184374,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566431000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184373,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566429000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184372,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566427000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184371,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566426000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184370,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566424000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184369,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566423000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184368,
		"userId": 26094,
		"couponName": "卡券专用券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1288.00,
		"startTime": 1585566421000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184361,
		"userId": 26094,
		"couponName": "购买现金券",
		"couponType": "现金",
		"useType": "卡券",
		"limitMoney": 0.00,
		"lessMoney": 1200.00,
		"startTime": 1585565882000,
		"overTime": 1585929590000,
		"status": 0,
		"projectId": "448,470"
	}, {
		"couponId": 184359,
		"userId": 26094,
		"couponName": "购买满减券",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 1000.00,
		"lessMoney": 999.00,
		"startTime": 1585497600000,
		"overTime": 1586015999000,
		"status": 0,
		"projectId": "475,476,477,480,481,483,484,485,486"
	}, {
		"couponId": 184349,
		"userId": 26094,
		"couponName": "测试商品现金券",
		"couponType": "现金",
		"useType": "通用",
		"limitMoney": 0.00,
		"lessMoney": 300.00,
		"startTime": 1585549579000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "0"
	}, {
		"couponId": 184348,
		"userId": 26094,
		"couponName": "测试商品现金券",
		"couponType": "现金",
		"useType": "通用",
		"limitMoney": 0.00,
		"lessMoney": 300.00,
		"startTime": 1585549573000,
		"overTime": 1585756790000,
		"status": 0,
		"projectId": "0"
	}, {
		"couponId": 184346,
		"userId": 26094,
		"couponName": "测试商品优惠券",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 500.00,
		"lessMoney": 200.00,
		"startTime": 1585238400000,
		"overTime": 1585670399000,
		"status": 0,
		"projectId": "486"
	}, {
		"couponId": 184345,
		"userId": 26094,
		"couponName": "测试商品优惠券",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 500.00,
		"lessMoney": 200.00,
		"startTime": 1585238400000,
		"overTime": 1585670399000,
		"status": 0,
		"projectId": "486"
	}, {
		"couponId": 184344,
		"userId": 26094,
		"couponName": "测试商品优惠券",
		"couponType": "满减",
		"useType": "商品",
		"limitMoney": 500.00,
		"lessMoney": 200.00,
		"startTime": 1585238400000,
		"overTime": 1585670399000,
		"status": 0,
		"projectId": "486"
	}, {
		"couponId": 184312,
		"userId": 26094,
		"couponName": "会员专享10元券",
		"couponType": "现金",
		"useType": "商品",
		"limitMoney": 0.00,
		"lessMoney": 10.00,
		"startTime": 1583856000000,
		"overTime": 1584115199000,
		"status": 0,
		"projectId": "0"
	}]
}
```
### 字段说明
说明下每个字段具体对应的值代表什么。

couponId:优惠券id --type:number

userId：用户id --type:number

couponName：优惠券名称 --type:string

couponType：优惠券条件类型 --type:string

useType：优惠券使用类型 --type:string

limitMoney：优惠券满减条件起始值(如果是现金返回0) --type:number

lessMoney：优惠券优惠的金额 --type:number

startTime：优惠券有效开始时间 --type:data-time

overTime：优惠券有效结束时间 --type:data-time

status：优惠券的状态 --type:number

projectId：当前优惠券对应的商品id。0表示当前商品类型的任何商品可以使用，否则返回字符串每个商品id以逗号分隔 --type:string

## 方法实现
我们暂定从卡券商品入手（一对多），如果使用商品实现一篇还不能全部说明，因为设置多个商品对应多张优惠券问题(多对多)
假设用户选择了卡券商品直接购买，进入了结算页面。


```javascript
/**
   * autoMatchCoupon.
   *
   * @param num 商品数量.
   * @param dPrice 商品单价.
   * @param data 优惠券列表.
   */
var card = {
 cardsId: 476
}
var autoMatchCoupon = function (num,dPrice,data){
            var total = dPrice * num,
                xtDate = new Date().getTime(),
                coupList;
            if (data && data.length > 0) {
            //第一次过滤卡券和通用的优惠券，并且在优惠券有效时间范围内，并且总价满足优惠券使用条件
            //第二次过滤优惠券对应的卡券商品id
                  coupList = data.filter(function(ele){
                    return ((ele.useType === '卡券' || ele.useType === '通用') && (total >= ele.limitMoney) && (xtDate >= ele.startTime) && (xtDate <= ele.overTime))
                }).filter(function (ele) {
                    return  ele.projectId.split(',').some(function (ele) {
                        return ele == card.cardsId
                    })
                });
            //考虑兼容性使用es5通用数组去重，es6直接可以使用Set
                var unique = function (arr){
                    for(var i=0; i<arr.length; i++){
                        for(var j=i+1; j<arr.length; j++){
                            if(arr[i].lessMoney==arr[j].lessMoney){
                                arr.splice(j,1);
                                j--;
                            }
                        }
                    }
                    return arr;
                };
               //去掉重复的优惠券
                coupList = unique(coupList);
               //映射成只需要关键字段的list
                coupList = coupList.map(function(ele){
                    return {
                        id:ele.couponId,
                        name:ele.couponName,
                        price:ele.lessMoney
                    }
                });
               //判断优惠券是否大于1张，大于则使用sort降序
                if (coupList.length > 1) {
                    coupList.sort(function(a, b) {
                        return b.lessMoney - a.lessMoney;
                    });
                }
                 coupList = coupList[0];
            }
            return coupList
        };
```
上面代码中最后会返回一张最优的优惠券，已经实现核心需求，当然后期远远不止这样，其中sort可以根据具体业务来使用不同的算法
