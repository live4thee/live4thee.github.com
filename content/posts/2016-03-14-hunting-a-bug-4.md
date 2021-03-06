---
categories:
- work
date: 2016-03-14T00:00:00Z
description: ""
tags:
- .Net
- Azure
title: Hunting a Bug (4)
url: /2016/03/14/hunting-a-bug-4/
---


上午到公司还没出电梯，手机里就蹦出一封邮件，某客户询问某些文件未能及时
上传的原因。当时觉得有点不可思议，因为相关服务已经好几个月没出过问题了。

第一个可能：出于某种原因，服务代码检查到自己一直处于 `staging` 状态，
因此自动停止服务。稍微一想就排除了。

第二个可能：由于 `await` 和 `async` 没处理好，导致服务死锁。这还是不大
可能，因为之前已经很确定，相关代码并没有跑在 `MVC` 上下文。

因此，剩下唯一的可能就是服务挂掉了。由于相当巨大的数据量，在 Azure 的
`MDS` 里面搜索相关信息是个非常痛苦的过程。以半小时为时间窗口，进行二分
法搜索，花了一个小时左右，定位到出错记录。因为一个 `unhandled
exception`，服务代码挂掉了。跟踪了代码历史，这个错误正是我自己去年11月
下旬引入的，当时一定是脑子进水了。

如果 C# 有什么我比较抱怨的地方，那就是它允许 `unchecked exception`。虽
然写代码方便了，但是牺牲了一点可靠性。后来发现，其实邮件里面有相关错误
报警，只是当时没有时间看邮件。
