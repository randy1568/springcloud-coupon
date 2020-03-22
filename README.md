# 分布式优惠券系统

## 技术栈

SpingBoot<br>

SpringCloud<br>

MySQL<br>

Redis<br>

Kafka<br>

## 系统架构图
![tu1](https://github.com/Rumoers/springcloud-coupon/blob/master/png/coupon1.png)

![tu2](https://github.com/Rumoers/springcloud-coupon/blob/master/png/coupon2.png)
## 模块划分

模版模块：提供给运营人员，运营人员根据要求构建优惠券模版

分发模块：面向用户，提供查看、领取、结算、核销优惠券等功能。需要依赖模版微服务与结算微服务

结算模块：结算优惠券，对优惠券规则进行计算

### 模版微服务

运营人员通过设定条件创建优惠券模版，之后生成对应数量优惠券，最后用户才可以去领取优惠券

核心功能：运营人员设定好条件(名称、logo、数量等)创建优惠券模版，后台异步创建对应数量优惠券。创建优惠券过程比较耗时，http接口不返回不是一种很好的体验，所以使用异步操作

使用Redis保存优惠券码，提高效率

查询优惠券信息功能，用于提供给其他微服务调用

### 分发微服务

面向用户，提供查看优惠券、领取优惠券、核销优惠券与结算优惠券功能

#### 查看优惠券

​	根据不同信息查询对应优惠券模版信息，需要依赖模版微服务

#### 领取优惠券

​	通过验证，即优惠券模版是可领取的，且成功获取到优惠券码，就将优惠券写入MySQL与Redis

#### 核销优惠券

​	标记优惠券状态为已使用，更新MySQL与Redis数据

#### 结算微服务

​	调用结算微服务计算优惠券规则

### 结算微服务

根据优惠券类型计算优惠券



#### 技术两点

1. 使用Redis做缓存提高访问速度与并发量，减少数据库压力
2. 使用异步线程生成优惠券码
3. 仿照Redis过期册，使用定期删除+惰性删除方法，检测删除过期的优惠券模版
4. 使用Kafka异步处理已使用与已过期优惠券信息回写到数据库
