## 2016年6月12日 10:50:16

## Dubbo 背景

* 当服务越来越多，小服务资源的浪费问题（一个很小的模块也要跟支付能一些重要模块一样配置各种文件）凸显，需要一个==调度中心== 实时关联集群容量
* 资源调度 和 治理中心（SOA）是关键


## Dubbo构架

* Provider： 暴露服务的服务提供方
* Counsumer
* Registry
* Monitor： 监控中心


## Dubbo优点

* 使用简单与spring无缝集成
* 统一的服务调用地址
* 能负载均衡
* 可以进行集群配置
* 方便服务治理

## Dubbo实现负载均衡

* 提供者都注册到注册中心
* 消费者到注册中心中调用服务，不用关系服务的具体地址
* 注册中心获取请求后将请求分发的不同的提供者

## Dubbo注册中心也可以集群

## Provider 配置

* 设置提供方应用名称
* 配置dubbo注册中心地址 registry address (zookeeper://127.0.0.1:2181)
* 暴露服务在20880端口
```
<dubbo:protocol name="dubbo" serialization="dubbo" port="20880" />

```
* 配置线程数 ，在<dubbo:protocol /> 里面配置`threads="400"`

* 配置dubbo提供方服务
```xml

<!-- 声明一个普通的Spring bean -->
<bean id="demoService" class="com.alibaba.dubbo.demo.provider.DemoServiceImpl" />

<!-- 声明dubbo服务-->
<dubbo:service interface="com.alibaba.dubbo.demo.DemoService" ref="demoService" />


```

## Consumer配置

* 配置消费者应用名称
* 配置注册中心地址
* 配置消费者应用reference
```xml
<dubbo:reference id="demoService" interface="com.alibaba.dubbo.demo.DemoService" timeout="5000" check="false" retries="0"/>
```

## 在消费端配置超时时间，重试次数
```
<dubbo:consumer check="false"  timeout="10000" retries="0"/>

check：启动的是是否检查提供方是否存在

```

## 服务端负载均衡配置

* `<dubbo:reference ... loadbalance="leastActive"/>`

### 负载均衡配置的种类

* Random 随机，按权重设置随机概率
* RoundRobin 轮询，按公约后的权重设置轮询比率
* LeastActive 最少活跃数 （使慢的服务者获取到最少请求）
* Consistenthash 一致性hash，相同参数的请求总发送到同一个提供者

