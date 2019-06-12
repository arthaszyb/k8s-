# k8s学习记录
## 调度策略：
1. NodeSelector:定向调度，可以通过 Node 的标签( Label) 和 Pod 的 nodeSelector 属性相匹配
2. NodeAffinity：Node亲和性调度，不仅可选择node，还可根据pod标签选择，将来可取代nodeSelector
3. Taints 和 Tolerations 使节点拒绝执行不允许的pod，pod通过Tolerations使node接受它
4. schedulerName用于支持自定义调度器----纵向调度？

## 容器初始化：
系统会在启动其他容器前先启动init容器执行其中命令，多个init容器按顺序执行，全部执行成功才会开始创建应用容器。

## service
 - endpoint可以不指定selector创建，用于指定外部服务共集群内部访问。yaml中写明后端ip和port

## pod重启与容器重启：
1. init容器或pause容器出现更新等引起重启，pod就会重启
2. 应用容器重启，pod不会重启。只有全部应用容器重启，并且 RestartPolicy=Always，则 Pod 将会重启 。

## pv的高级用法：
pv使用有两种方式：静态和动态。动态即采用storageClass特性将存储分类打标，然后直接通过配置vpc来绑定storageClass来需求存储空间。这样完全屏蔽pv概念，用户不必去手工配置pv。

##API
1. api-server提供了swagger-ui形式的RESTFUL-api页面。从1.6版本开始，启动apiserver时带上--enable-swager-api=true即开启该功能。
2. 如果api-server地址为1.1.1.1:8080，则访问http://1.1.1.1:8080/swagger-ui/即可查看api列表。
3. API方法：
3.1. GET（查询）
3.2. POST（创建）
3.3. PUT（修改或创建）
3.4. DELETE
3.5. PATCH（选择修改资源详细指定的域），其本身通过HTTP首部的“Content-Type”进行识别，支持三种类型

# 其他知识：
## shell知识：
1. shell切片跟python差不多，直接${a[3]},${a:3}等等。
2. tr是修改字符重复字符的神奇，也可用于删除重复字符
3. jq是处理json文本的好工具。
