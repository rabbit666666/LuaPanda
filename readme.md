# LuaPanda
### 此版本主要与官方的主要区别：
- 关闭LanguageServer：将LuaPanda作为一个调试器使用，LanguageServer使用EmmyLua的。
- 调试时增加自动启动程序功能：在配置文件中配置
  - "command": 程序的命令行。
  - "command_working_dir": 工作目录（注意该目录不一定是代码目录）。


#### LuaPanda 是一个基于 VS Code 的 lua 代码工具，设计目标是简单易用。它支持多种开发框架，主要提供以下功能：

- 自动补全（auto completion）
- 代码片段（snippet completion）
- 定义跳转（definition）
- 生成注释（comment generation）
- 类型推断（limited type inference）
- 代码格式化（formatting）: 依赖 [lua-fmt](https://github.com/trixnz/lua-fmt)
- 代码诊断（linting）：依赖 [luacheck](https://github.com/mpeterv/luacheck)
- 调试器（debugger）

功能详情可以参考[项目介绍](./Docs/Manual/feature-introduction.md)。LuaPanda 支持 lua5.1- 5.3，调试器运行环境需要包含 LuaSocket。

LuaPanda 的立项源于潘多拉项目中大量的lua调试需求。`潘多拉`为游戏提供嵌入式跨引擎的运营开发能力，使游戏研发及运营各自独立闭环，在游戏内实现各种营销活动和周边系统，让游戏分工更加专业，团队更加专注，高效产出价值。
潘多拉为游戏提供的服务包括用户生命周期的精细化运营方案、游戏内直播解决方案、游戏内内容社区解决方案、游戏内商城商业化解决方案等，已经在大量腾讯精品游戏中上线、稳定运营。



# 接入和开发指引

接入和使用文档

[项目介绍](./Docs/Manual/feature-introduction.md)	| [快速试用指引](./Docs/Manual/quick-use.md) | [调试器接入指引](./Docs/Manual/access-guidelines.md) |  [真机调试](./Docs/Manual/debug-on-phone.md)  | [单文件调试和运行](./Docs/Manual/debug-file.md) | [升级说明](./Docs/Manual/update.md) | [FAQ](./Docs/Manual/FAQ.md)

调试器开发文档

[工程说明](./Docs/Development-instructions/project-description.md) 	|  [调试器开发指引](./Docs/Development-instructions/how_to_join.md) |  [特性简述](./Docs/Development-instructions/debugger-principle.md) 



# Tips

+ 版本说明和升级建议

  3.0.0 版本中加入了 LuaPanda.lua 文件的自动更新提示，帮助把版本保持到最新。升级原理是检测VScode打开工程中是否包含 LuaPanda 文件，并匹配文件中的版本号，如果落后于当前插件版本，则用插件中附带的最新版覆盖。升级过程无需网络，也不会对外发送和接收数据。

  ![updateTips](https://github.com/Tencent/LuaPanda/blob/master/Docs/static/updateTips.png?raw=true)

  另外加入了配置页面，点击状态栏的LuaPanda图标即可打开。其中提供了一些常用配置方便用户修改。配置页面打开时读取launch.json中的对应数据，并在配置完成后把数据写回launch.json, 如果不想使用配置页面，直接修改launch.json中的项目可以达到同样的效果。

  

+ 关于找不到`libpdebug`模块报错

  `libpdebug.so(dll)` 是放置在VSCode插件中的调试器C扩展，会在调试器运行时自动加载，作用是加速调试。此模块未能加载时，调试器功能不会受到影响，仍可正常使用。

  xlua允许用户重写文件加载函数`CustomLoader`，sluaunreal也提供类似方法`setLoadFileDelegate`。

  发生此问题的原因之一是用户重写的加载函数中没有加入对so/dll的处理，加载so/dll时会报找不到文件错误，但随后执行lua原生loader能够正确加载libpdebug。

  查看libpdebug.so是否加载的方式是在控制台输入`LuaPanda.getInfo()`, 返回信息中有 hookLib Ver 说明libpdebug已经加载。此时可以忽略报错或在文件加载函数函数中正确处理.so/dll。



# 近期更新

+ V3.1.0
  
  + 新增了导出 slua 符号用于代码提示。  [使用说明](https://github.com/Tencent/LuaPanda/blob/master/Docs/Manual/lua-intellisense.md)
  + 重构了定义跳转和代码提示框架，提升了分析效率。自测修复了 #49 大量lua工程下无法工作的问题。
  + 修复了 #47 table 成员展示问题。
  + 修复了 #46 配置 program 后 , attach 启动拉起新进程的问题。增加了一个attach启动项。
  + 实现了 #44 提出的 terminal 复用的建议。
  
  
  
+ V3.0.2

  + 新增了导出 slua-unreal 符号用于代码提示。 [使用说明](https://github.com/Tencent/LuaPanda/blob/master/Docs/Manual/lua-intellisense.md)

  

+ V3.0.1

  + 修复可视化配置的bug
  + 在可视化界面增加lua辅助工具开关

  

+ V3.0.0

  + 加入代码辅助功能

    加入常用的代码补全，代码片段，定义跳转，生成注释，符号列表等功能。这些功能无需设置，VSCode包含打开lua的文件夹即可使用。

  + LuaPanda.lua 升级提示

    用户很少主动更新LuaPanda.lua文件，有时会因版本不匹配造成无法调试的问题。所以我们加入了自动更新提示。可以自动把工程中的LuaPanda.lua替换到最新。

  + 可视化配置界面

    采用了配置界面，方便用户可视化配置常用选项。原理是把用户的配置结果写回到 launch.json，和直接修改 launch.json 中的配置效果一致。

  + 合并了 `LuaPanda.lua` 和 `DebugTools.lua`  。 为了方便文件拷贝和升级对这两个文件做了合并，内容全部放入 `LuaPanda.lua`。DebugTools.lua 不再使用。

+ [更多更新记录](https://github.com/Tencent/LuaPanda/blob/master/CHANGELOG.md)




# 特性

+ 支持常用的代码补全，代码片段，定义跳转，生成注释，符号列表等功能
+ 支持单步调试，条件断点，协程调试，支持调试时变量赋值。
+ 支持lua5.1- 5.3, 支持 slua/xlua/slua-unreal 等框架
+ 在断点处可以监视和运行表达式，返回结果
+ 可以根据断点密集程度调整 hook 频率, 有较高的效率
+ 支持 attach 模式，lua 运行过程中可随时建立连接
+ 使用 lua / C 双调试引擎。lua 部分可动态下发，避免打包后无法调试。C 部分效率高，适合开发期调试

+ 可以提供代码补全，代码片段，定义跳转，生成注释，符号列表等功能



# 依赖和适用性

调试器功能依赖 LuaSocket , 可运行于 slua，slua-unreal ，xlua 等已集成 LuaSocket 的 lua 环境，也可以在 console 中调试。lua 版本支持 5.1- 5.3。

其他依赖项目（插件中已包含，无需用户手动安装）：

+  [**luaparse**](https://github.com/oxyc/luaparse)

+  [**luacheck**](https://github.com/mpeterv/luacheck)

+  [**lua-fmt**](https://github.com/trixnz/lua-fmt)

+  [**path-reader**](https://github.com/ackerapple/path-reader)



# 参与贡献

我们非常期待您的贡献，无论是完善文档，提出、修复 Bug 或是增加新特性。
如果您在使用过程中发现文档不够完善，欢迎记录下来并提交。
如果发现 bug，请通过 [issues](https://github.com/Tencent/LuaPanda/issues) 来提交并描述相关的问题，您也可以在这里查看其它的 issue，通过解决这些 issue 来贡献代码。

请将pull request提交在 `dev` 分支上，经过测试后会在下一版本合并到 `master` 分支。更多规范请看[CONTRIBUTING](./CONTRIBUTING.md)

[腾讯开源激励计划](https://opensource.tencent.com/contribution) 鼓励开发者的参与和贡献，期待你的加入。



# 技术支持

如有问题先参阅 [FAQ](./Docs/Manual/FAQ.md) ，如有问题建议使用 [issues](https://github.com/Tencent/LuaPanda/issues) ，我们会关注和回复。

QQ群：974257225

