# KTS10603 - Babelway Notes One

[http://www.babelway.com/help/BabelwayHelp.html](http://www.babelway.com/help/BabelwayHelp.html)

## 1. Chapter 2

### Textual Filter

| Operator | Comments |
| :--- | :--- |
| ~ | contains |
| !~ | not contains |
| = | equals |
| != | not equals |
| ^ | begins with |
| \| | end with |
|  | regular expression \( Pending \) |
| \# | is empty |
| !\# | is empty |
| \[\] | \( Numeric to select range \) |
| , | id is in \( Component only \) |
| !, | id is not in（Component only） |

## 2. Chapter 3

### 2.1. Message Details

#### Status

| Status | Comment |
| :--- | :--- |
| Processing | 正在处理 |
| Paused | 临时停止 |
| In delivery | 正在传输 |
| Success | 处理完成传输成功 |
| Error | 处理完成但在任何某一步中有错 |
| Error\(closed\) | 这个状态系统不会使用，但可以被人工生成 |

错误原因：

* 接收文件未通过输入验证
* 文件生成成功并放到了Babelway FTP上，但很长时间没下载（因为有错未被最终用户接收）
* 文件生成成功并通过Email发送了两次，但其中一次成功另外的失败了
* 文件生成成功并上传到正确位置，但我们并没有收到回执
* 执行过程Crashed

Type字段的两个值：Test/Regular

Test status字段的三个值： Test failed, Waiting result, Test successful

Internal Files主要包含两种：Step Files/Other Files——主要用于诊断问题以及理解系统行为

### 2.2. Resubmitting a Message

#### Send To

* Same channel
* Channel
* Same gateway in
* Gateway in

### 2.3. Download Limitation

* 一次只能开启5个下载线程，如果开启新线程必须等待其中一个完成
* 最多可申请10000消息记录一次下载，如果你要下载更多，则最好将下载请求进行分类

所有消息会形成一个Chain，安全Chain则使用SHA-512算法进行加密形成Secure Chain。

### 2.4. Alert Details

* Severity：High, Medium, Low, Info
* Status: Open, Closed
* Type:
  * General Monitoring
  * Message Processing Issue
  * Unidentified Source
  * Unidentified Message In
  * Unidentified Channel
  * Message Validation Issue
  * Failed Delivery
  * Planned Deletion of Unattended Messages
  * Hub Issue
  * Storage Capacity Reached
  * Planned Maintenance Unavailability
  * New Functionality Available
  * Account Issue
  * Credit Limit Reached
  * Outstanding Invoice
  * Credit Limit Almost Reached
  * VAT Rate Has Changed
  * Channel Management Issue
  * Notification of a Change in a Linked Channel
  * Planned Deletion of Unused Channels
  * Documentation of a Channel
  * User Management Issue
  * Failed Connection Attempts
  * First Connection of new New User
  * Planned Deletion of Inactive Users
  * Invitation Expired



