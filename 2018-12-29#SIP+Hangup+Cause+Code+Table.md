# 2018-12-29#SIP+Hangup+Cause+Code+Table

hangup_reason_id | name | comment
--| -- | --
0 | unknow_status | 未知的状态
1 | UnallocatedNumber | 未分配的号码
2 | NoRouteToNetwork | 没有路由到网络
3 | NoRouteToDestination | 没有路由到目标
4 | SendSpecialTone | 发送特别的音频
5 | MisdialledTrunkPrefix | 未拨出中继前缀
6 | ChannelUnacceptable | 通道不可接受
16 | NormalCallClearing | 正常挂机
17 | UserBusy | 用户忙
18 | NoResponse | 无应答
19 | NoAnswer | 无人接听
20 | SubscriberAbsent | 归属地没有
21 | CallRejected | 呼叫拒绝
22 | NumberChanged | 号码被改变
23 | Redirection | 重定向
25 | ExchangeRoutingError | 交换出划
26 | NonSelectedUserClearing | 没有选择用户清除
27 | DestinationOutOfOrder | 目的地顺序错
28 | InvalidNumberFormat | 无效的号码格式
29 | FacilityRejected | 设备被拒绝
30 | StatusEnquiryResponse | 状态查询无应答
31 | NormalUnspecified | 正常未被描述
34 | NoCircuitChannelAvailable | 没可用电路
38 | NetworkOutOfOrder | 网络顺序出错
41 | TemporaryFailure | 临时不可用
42 | Congestion | 拥塞
44 | RequestedCircuitNotAvailable | 请求的电路不可用
47 | ResourceUnavailable | 资源不可用
63 | ServiceOptionNotAvailable | 服务选项不可用
81 | InvalidCallReference | 无效的呼叫标识
86 | ClearedRequestedCallIdentity | 被清除的请求呼叫标识
88 | IncompatibleDestination | 不匹配的目标类型
99 | IENonExistantOrNotImplemented | 不存在或未实现
102 | TimerExpiry | 定时器超时
111 | ProtocolErrorUnspecified | 协议出错未描述
127 | InterworkingUnspecified | 内部转换出错

参考：
- https://freeswitch.org/confluence/display/FREESWITCH/Hangup+Cause+Code+Table
- https://hwzyyx.iteye.com/blog/2103438
- https://blog.csdn.net/bigtree_3721/article/details/50966868
- https://www.voip-info.org/asterisk-variable-hangupcause