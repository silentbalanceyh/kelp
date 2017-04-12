# KTS10002 - Design Patterns

## UI Structure

* 小手机尺寸设备（&lt;600px）：仅保留TopBar快捷方式作为Global菜单，TopBar本身隐藏、触摸时显示；
* 中型平板设备（600px ~ 1270px）：主菜单（Primary）和二级菜单（Secondary）在TopBar中呈现，不常用的三级菜单（Tertiary）隐藏；
* 大型桌面设备（&gt;1270px）：Global Menu展开显示，TopBar以平板风格显示；

菜单种类：

* **Global Menu**：用户在主要功能区域切换的入口
* **TopBar**：基于不同呈现有多重目的，比如一些Action、Operation、Navigtion等
* **Main**：常用工作区域，覆盖整个区域，让用户集中精力处理主要任务

## Navigation Items

* 菜单项可插入到Global Menu中提供直接访问应用路径
* 必须提供和应用程序名称一样的应用程序图标，触发时高粱显示
* **Shortcuts**：仅仅在平板尺寸中可用（不包含名称的菜单）——实际上就是全局菜单

### TopBar

可使用不同方式定制，当使用TopBar的方式描述按钮，按钮关联的行为是可见的（Top Level），按钮也可使用右键方式弹出。

TopBar可以包含分割流程的TAB页，一般如：Step1 -&gt; Step2 -&gt; Step3这种。

如果使用了Tab格式导航来处理用户流程，基于流程的TopBar可使用，这种流程中需要包含一定的空白空间。

若是TopBar中包含了复杂的列表，TopBar中包含的就是Search/Filter的输入框和按钮。

### Buttons

按钮基本同意风格（包括颜色、尺寸、位置），主要按钮风格：

* **Primary Button**：用户主要交互行为
* **Secondary Button**：大部分用户需要，但没有它页面流程依旧工作，位于Primary Button左边
* **Tertiary Button**：大部分用户不常用的按钮（删除、查看文档、导出文档等）
* **Kill Button**：删除和拒绝行为的专用按钮

### Option List & Pickers

[http://ui.tradeshift.com/\#design/patterns/pickers.html](http://ui.tradeshift.com/#design/patterns/pickers.html "Reference")

### Tables

表格使用中考虑下边几个概念：

* Line-wrapping：单元格中文字自动换行
* Row-selection：可直接执行行选择，除了第一个Checkbox还可通过选中行的方式来处理
* Clickable rows：行点击事件
* Responsive column handling：响应式列处理

### Switches

开关控件，用于选择”是/否“选项。

### Email

[http://ui.tradeshift.com/\#design/patterns/emails.html](http://ui.tradeshift.com/#design/patterns/emails.html "Reference")





