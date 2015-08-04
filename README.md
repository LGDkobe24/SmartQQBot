SmartQQ-Bot Ver-0.2
=========
**注意:**此框架仍处于开发中，许多模块仍可能出现较大改动，如果仍想继续升级该框架，推荐各位暂时不要进行较大规模的自定义。

***该分支使用登陆部分逻辑与代码参考了[原名：SmartQQ-for-Raspberry-Pi(PiWebQQV2)](https://github.com/xqin/SmartQQ-for-Raspberry-Pi)这一项目***，以此为基础制作了基于SmartQQ的自动机器人*框架*。

登陆时采用QQ安全中心的二维码做为登陆条件, 不需要在程序里输入QQ号码及QQ密码。

##基本功能

###群聊功能：
<small>注：以下命令皆是在qq中发送，群聊命令发送到所在群中</small>

+ 群聊吐槽功能(tucao)，类似于小黄鸡，在群中通过发送```!learn {ha}{哈哈}```语句，则机器人检测到发言中包含“ha”时将自动回复“哈哈”。```!delete {ha}{哈哈}```可以删除该内容。吐槽内容本地保存在data/tucao_save/中。

+ 群聊复读功能(repeat)，检测到群聊中***连续两个***回复内容相同，将自动复读该内容1次。

+ 群聊关注功能(follow)，使用命令`!follow qq号`可以使机器人复读此人所有发言（除命令外）使用命令`!unfollow qq号`解除关注。qq号处可使用"me"来快速关注与解除关注自己，例：`!follow me`

+ 群聊唤出功能(callout)，群聊中检测关键词`智障机器人`，若发言中包含该词，将自动回复`干嘛（‘·д·）`，此功能一般用于检测机器人状态与调戏

+ 群聊命令功能(command_0arg/command_1arg)：使用`![命令名]`格式或`![命令名] {参数1}`执行命令，命令“吐槽列表”，使用命令`!吐槽列表`在群聊中激活，列出当前群的吐槽列表。
	+ 现有无参数命令：
		+ `!吐槽列表`:列出当前群的吐槽列表
	+ 现有单参数命令:
		+ `!删除关键字 {blablabla}`:删除关键字“blablabla”下的所有吐槽内容

###私聊功能：
+ 私聊唤出功能(callout)，私聊中检测关键词`智障机器人`，若发言中包含该词，将自动回复`干嘛（‘·д·）`，此功能一般用于检测机器人状态与调戏

+ 私聊复读功能(repeat)，检测到私聊中***连续两个***回复内容相同，将自动复读该内容1次。

###临时对话功能：
+ 唤出功能(callout)，具体同私聊与群聊。

##快速开始
+ ```python main.py```
+ 等待提示“登陆二维码下载成功，请扫描”，打开脚本所在目录的v.jpg图片扫描二维码。
+ 等待登陆成功的提示
+ 群聊各功能需要通过修改config文件夹下的共有设置QQBot_default.conf中的功能开关。

#细节说明


##如何二次开发自定义功能
+ 根据功能类型修改对应文件：
	+ 群聊功能：Group.py
	+ 私聊功能：Pm.py
	+ 临时对话功能：Sess.py

+ 在对应类(class)中编写方法，方法的参数固定为self与msg，即
```def [Function Name](self, msg):```
	+ 需要注意方法的返回值，执行成功请返回```True```,没有执行或执行失败请返回```False```
+ 在对应类的process_order中，选择适当的位置加入你添加的功能的名称（Function Name），你添加的位置决定了功能的执行顺序如
```
self.process_order = [
            "repeat",
            "callout",
        ]
```    
+ 在共有设置中添加功能的开关，如
```
callout = 0
repeat = 1
```
0代表关闭、1代表开启
+ (可选)在Config.py中的DefaultConfigs类下的__init__方法中添加属性的默认参数。
+ 私有设置会在程序启动后收到消息时自动由共有设置生成

##配置文件读取逻辑：
###程序启动时：
1. 判断是否存在共有设置文件，若不存在，从Config.py文件中的DefaultConfig类声明中生成QQBot_default.conf文件
###收到消息时:
2. 判断是否存在私有设置文件，若不存在，复制共有设置文件中的对应部分，生成[id].conf配置文件
3. 读取私有文件中的use_private_config属性，若为'1'，则使用私有配置文件，否则使用共有配置文件
4. 读取对应的配置文件内容

##程序模块：
Module | File
--- | ---
登陆与消息获取模块 | QQLogin.py
消息处理模块 | MsgHandler.py
配置参数控制模块 | Config.py

##类定义
Class type | File
--- | ---
消息类 | Msg.py
通知类 | Notify.py
群聊类 | Group.py
私聊类 | Pm.py
临时对话类 | Sess.py

##程序运行逻辑：
1. 使用QQ类登陆
2. 使用登陆后的QQ类初始化消息处理模块
3. 进入轮询
4. 处理消息前从配置文件读取最新配置
5. 使用消息处理模块处理消息

##日志输出信息分类：
Level | Logging 
--- | --- 
DEBUG | 所有请求的返回包
INFO | 所有请求的简要信息、程序运行提示 
WARNING | 不影响程序正常运行的错误提示 
ERROR | 影响程序正常运行的错误提示 

**文档仍待完善**

##TODO
+ 把旧版本原有功能逐步添加回来
+ 开发命令控制模块
+ 优化配置参数模块，加入共有设置与私有设置的热切换
+ 优化稳定性，补充类定义
+ 尽可能地简化二次开发的复杂性
+ 编写文档
+ 细化配置项（tucao_auth = (all/self/custom/none)）
+ Friend类补充
+ 寻找偶尔被保护的原因