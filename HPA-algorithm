# Kubernetes自动缩扩容HPA算法小结
> 来源：https://www.jianshu.com/p/504f49710f84
>96  BookKeeper 
>2018.11.05 16:46 字数 753 阅读 900评论 0喜欢 0
Kubernetes自动缩扩容HPA
Kubernetes在autoscalingV2版本的api中支持了Resource、Object、Pods三种指标类型，其中

Resource类型:主要用于受限于requests和limits的资源，比如cpu、memory
Object类型:主要用于Kubernetes内置的资源对象，如Ingress的每秒请求数指标
Pods类型: 主要用于每个pod内的指标，比如tps等。
##关键概念
Requests: 对单个容器分配的最小资源，在Deployment的spec中定义，HPA算法以该值作为资源总量来计算。
target: 达到缩扩容条件的目标值，更准确的说法是参考值，因为要达到缩扩容条件，需要结合下面的tolerance值。该指标支持利用率(Utilization)和数值(Value)二者择其一，不可同时设定。
tolerance: 容忍值，当且仅当本次获取的指标与target的差值的绝对值高于该值时，才会触发缩扩容
##指标收集
metrics server从kubelet中的cAdvisor组件中获取，默认情况下，每10s(--cpu-manager-reconcile-period)采样一次，每30s(kubernetesCadvisorWindow)计算一次cpu使用量。

Pod在未Ready状态下，可正常收集metrics

扩容算法
计算步骤
获取所有pod的metrics信息
获取所有pod状态信息
遍历pod状态信息，记录并剔除未Ready的数据，同时记录暂无metrics的Pod
计算目标副本数
算法
* 参与缩扩容计算的metrics个数 等价于 参与缩扩容计算的pod数量 ，用podCount表示
* 扩容时，未Ready的Pod和无metrics的Pod，metrics均按0计算，计podCount
* 缩容时，未Ready的Pod不参与计算，不计podCount， 无metrics的Pod metrics值按target计算，计podCount
* 目标副本数是通过一个调整副本的比例系数(usageRatio)计算的。

usageRatio = avgUtilization / target

一般性指标利用率，基于所有容器的当前指标和参与缩扩容计算的pod数量计算
usageRatio = (sum(metrics) / podCount) / target
cpu、memory等资源利用率，基于所有容器的当前指标和requests计算
usageRatio = (sum(metrics) / sum(requests)) / target

如果 Math.abs(1-usageRatio) > tolerance, 则按下面的公式计算目标副本数，否则返回原本副本数
TargetNumOfPods = ceil(usageRatio * podCount)

##实践
经实践，在进行滚动升级(Rolling-Update)时，由于cpu load升高会触发HPA。

在扩容计算时，获取所有pod的metrics，刚刚变为ready状态的pod load虽然降低了，但由于指标收集的滞后性，此时拿到的指标大概率是高负载时的数据，由于pod已经ready，该数据将会参与计算目标副本数。

为了避免这种情况，目前做法是，在进行滚动升级之前将HPA移除，待发布完成时，再重建HPA。
