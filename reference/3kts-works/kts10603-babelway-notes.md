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

#### Type = Test/Regular

#### Test status = Test failed, Waiting result, Test successful

### 2.2. Resubmitting a Message

#### Send To







### 

### 



