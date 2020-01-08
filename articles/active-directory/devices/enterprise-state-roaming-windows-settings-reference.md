---
title: Windows 10 漫游设置参考 | Microsoft Docs
description: 将在 Windows 10 中漫游或备份的所有设置的完整列表。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9489228b6ba81ae8d09e0f6880634532d0c836c6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481812"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 漫游设置参考
以下为会在 Windows 10 中漫游或备份的所有设置的完整列表。 

## <a name="devices-and-endpoints"></a>设备和终结点
请参阅下表大概了解 Windows 10 中同步、备份和还原框架支持的设备和帐户类型。

| 帐户类型和操作 | 桌面型 | 移动电话 |
| --- | --- | --- |
| Azure Active Directory：同步 |是 |否 |
| Azure Active Directory：备份/还原 |否 |否 |
| Microsoft 帐户：同步 |是 |是 |
| Microsoft 帐户：备份/还原 |否 |是 |

## <a name="what-is-backup"></a>什么是备份？
Windows 设置通常会默认进行同步，但某些设置只会进行备份，例如设备上安装的应用程序列表。 备份仅适用于移动设备，目前不适用于企业状态漫游用户。 备份使用 Microsoft 帐户，并将设置和应用程序数据存储到 OneDrive。 如果用户使用“设置”应用禁用设备上的同步，通常可进行同步的应用程序数据会变为只能备份。 新设备首次运行期间，只能通过还原操作访问备份数据。 备份可通过设备设置禁用，并通过用户 OneDrive 帐户进行管理和删除。

## <a name="windows-settings-overview"></a>Windows 设置概述
最终用户可使用以下设置组在 Windows 10 设备上启用/禁用设置同步。

* 主题：桌面背景、用户磁贴、任务栏位置等。 
* Internet Explorer 设置：浏览历史记录、键入的 URL、收藏夹等。 
* 密码：Windows 凭据管理器，包括 Wi-Fi 配置文件 
* 语言首选项：拼写字典、系统语言设置 
* 轻松访问：讲述人、屏幕键盘、放大镜 
* 其他 Windows 设置：请参阅 Windows 设置详细信息
* Microsoft Edge 浏览器设置：Microsoft Edge 收藏夹、读取列表和其他设置

![同步设置](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

最终用户可通过 Microsoft Edge 浏览器的“设置”菜单选项来启用或禁用 Microsoft Edge 浏览器设置组（收藏夹、读取列表）同步。

![帐户](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

对于 Windows 10 版本 1803 或更高版本，最终用户可通过 Internet Explorer 的“设置”菜单选项启用或禁用 Internet Explorer 设置组（收藏夹、键入的 URL）同步。 

![设置](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows 设置详细信息
在下表中，“设置组”列中的“其他”项是指，可通过转到“设置”>“帐户”>“同步设置”>“其他 Windows 设置”来禁用的设置。 

“设置组”列中的“内部”项是指设置和应用，它们只能通过应用本身内的同步进行禁用，或使用移动设备管理 (MDM) 或“组策略”设置来禁用整个设备的同步进行禁用。
不漫游或同步的设置不属于某个组。

| 设置 | 桌面型 | 移动型 | 组 |
| --- | --- | --- | --- |
| **帐户**：帐户图片 |sync |X |主题 |
| **帐户**：其他帐户设置 |X |X | |
| **高级移动宽带**：Internet 连接共享网络名称（通过蓝牙启用自动发现移动 Wi-Fi 热点） |X |X |密码 |
| **应用数据**：单个应用可同步数据 |同步备份 |同步备份 |内部 |
| **应用列表**：已安装应用的列表 |X |backup |其他 |
| **蓝牙**：所有蓝牙设置 |X |X | |
| **命令提示符**：命令提示符“Defaults”设置 |sync |X |内部 |
| **凭据**：凭据保险箱 |sync |sync |password |
| **日期、时间和区域**：自动时间（Internet 时间同步） |sync |sync |language |
| **日期、时间和区域**：24 小时制 |sync |X |language |
| **日期、时间和区域**：日期和时间 |sync |X |language |
| **日期、时间和区域**：时区 | |X |language |
| **日期、时间和区域**：夏令时 |sync |X |language |
| **日期、时间和区域**：国家/地区 |sync |X |language |
| **日期、时间和区域**：每周的第一天 |sync |X |language |
| **日期、时间和区域**：区域格式（区域设置） |sync |X |language |
| **日期、时间和区域**：简短日期 |sync |X |language |
| **日期、时间和区域**：完整日期 |sync |X |language |
| **日期、时间和区域**：简短时间 |sync |X |语言 |
| **日期、时间和区域**：完整时间 |sync |X |language |
| **桌面个性化**：桌面主题（背景、系统颜色、默认系统声音、屏幕保护） |sync |X |主题 |
| **桌面个性化**：幻灯片放映壁纸 |sync |X |主题 |
| **桌面个性化**：任务栏设置（位置、自动隐藏等） |sync |X |主题 |
| **桌面个性化**：启动屏幕布局 |X |backup | |
| **设备**：已连接的共享打印机 |X |X |其他 |
| **Microsoft Edge 浏览器**：读取列表 |sync |sync |内部 |
| **Microsoft Edge 浏览器**：收藏夹 |sync |sync |内部 |
| **Microsoft Edge 浏览器**：热门网站 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：键入的 URL <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：收藏夹栏设置 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：显示主页按钮 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：阻止弹出窗口 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：询问如何处理每个下载 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：提出保存密码 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：发送 Do Not Track 请求 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：保存窗体项 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：键入时显示搜索和站点建议 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：cookie 首选项 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：允许站点保存设备中受保护的媒体许可证 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **Microsoft Edge 浏览器**：屏幕阅读器设置 <sup>[[1]](#footnote-1)</sup> |sync |sync |内部 |
| **高对比度**：“打开”或“关闭” |sync |X |轻松访问 |
| **高对比度**：主题设置 |sync |X |轻松访问 |
| **Internet Explorer**：打开选项卡（URL 和标题） |sync |sync |Internet Explorer |
| **Internet Explorer**：读取列表 |sync |sync |Internet Explorer |
| **Internet Explorer**：键入的 URL |sync |sync |Internet Explorer |
| **Internet Explorer**：浏览历史记录 |sync |sync |Internet Explorer |
| **Internet Explorer**：收藏夹 |sync |sync |Internet Explorer |
| **Internet Explorer**：排除的 URL |sync |sync |Internet Explorer |
| **Internet Explorer**：主页 |sync |sync |Internet Explorer |
| **Internet Explorer**：域建议 |sync |sync |Internet Explorer |
| **键盘**：用户可打开或关闭屏幕键盘 |sync |X |轻松访问 |
| **键盘**：开启粘滞键（默认为关闭） |sync |X |轻松访问 |
| **键盘**：开启筛选键（默认为关闭） |sync |X |轻松访问 |
| **键盘**：开启切换键（默认为关闭） |sync |X |轻松访问 |
| **Internet Explorer**：域语言：简体中文 (CHS) 标准键盘 - 启用自学习 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 启用动态候选项排名 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 字符集简体中文 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 字符集繁体中文 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 模糊拼音 |sync |backup |语言 |
| **语言**：CHS 标准键盘 - 模糊配对 |sync |backup |语言 |
| **语言**：CHS 标准键盘 - 完整拼音 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 双拼音 |sync |X |语言 |
| **语言**：CHS 标准键盘 - 读取自动更正 |sync |X |语言 |
| **语言**：CHS 标准键盘 - C/E 切换键，Shift |sync |X |语言 |
| **语言**：CHS 标准键盘 - C/E 切换键，Ctrl |sync |X |语言 |
| **语言**：CHS 五笔 - 单字符输入模式 |sync |X |语言 |
| **语言**：CHS 五笔 - 显示候选项的剩余编码 |sync |X |语言 |
| **语言**：CHS 五笔 - 误码时发出警报音 |sync |X |语言 |
| **语言**：CHT 注音符号 - 包含 CJK Ext-A |sync |X |语言 |
| **语言**：日语输入法 - 预测键入和自定义词语 |sync |sync |语言 |
| **语言**：韩文 (KOR) 输入法 |X |X |语言 |
| **语言**：手写识别 |X |X |语言 |
| **语言**：语言配置文件 |sync |backup |语言 |
| **语言**：拼写检查功能 - 自动更正并突出显示拼写错误 |sync |backup |语言 |
| **语言**：键盘列表 |sync |backup |语言 |
| **屏幕锁定**：所有屏幕锁定设置 |X |X | |
| **放大镜**：打开或关闭（主切换） |X |X |轻松访问 |
| **放大镜**：打开或关闭反转颜色（默认为关闭） |sync |X |轻松访问 |
| **放大镜**：跟踪功能 - 跟随键盘焦点 |sync |X |轻松访问 |
| **放大镜**：跟踪功能 - 跟随鼠标焦点 |sync |X |轻松访问 |
| **放大镜**：用户登录时启动（默认为关闭） |sync |X |轻松访问 |
| **鼠标**：更改鼠标光标大小 |sync |X |其他 |
| **鼠标**：更改鼠标光标颜色 |sync |X |其他 |
| **鼠标**：所有其他设置 |X |X | |
| **讲述人**：快速启动 |sync |X |轻松访问 |
| **讲述人**：用户可更改“讲述人”音调 |sync |X |轻松访问 |
| **讲述人**：用户可打开或关闭关于常见项的“讲述人”读取提示（默认为开启） |sync |X |轻松访问 |
| **讲述人**：用户可打开或关闭是否收听键入的字符（默认为开启） |sync |X |轻松访问 |
| **讲述人**：用户可打开或关闭是否收听键入的词语（默认为开启） |sync |X |轻松访问 |
| **讲述人**：“讲述人”后跟插入光标（默认为开启） |sync |X |轻松访问 |
| **讲述人**：启用“讲述人”光标视觉突出显示（默认为开启） |sync |X |轻松访问 |
| **讲述人**：播放音频提示（默认为开启） |sync |X |轻松访问 |
| **讲述人**：举起手指时激活触控键盘上的键（默认为关闭） |sync |X |轻松访问 |
| **轻松访问**：设置闪烁光标的粗细 |sync |X |轻松访问 |
| **轻松访问**：删除背景图像（默认为关闭） |sync |X |轻松访问 |
| **电源和睡眠**：所有设置 |X |X | |
| **启动屏幕个性化**：主题色（仅限电话） |X |sync |主题 |
| **键入**：拼写字典 |sync |backup |语言 |
| **键入**：自动更正拼写错误的单词 |sync |backup |语言 |
| **键入**：突出显示拼写错误的单词 |sync |backup |语言 |
| **键入**：键入时显示文本建议 |sync |backup |语言 |
| **键入**：选择文本建议后添加一个空格 |sync |backup |语言 |
| **键入**：双击空格键时添加一个句点 |sync |backup |语言 |
| **键入**：大写每个句子的首字母 |sync |backup |语言 |
| **键入**：双击 shift 键时全部使用大写字母 |sync |backup |语言 |
| **键入**：键入时播放键的声音 |sync |backup |语言 |
| **键入**：触摸键盘的个性化数据 |sync |backup |语言 |
| **Wi-Fi**：Wi-Fi 配置文件（仅 WPA） |sync |sync |密码 |

###### <a name="footnote-1"></a>脚注 1
Windows Creators 更新（内部版本 15063）受支持的最低 OS 版本。 

## <a name="next-steps"></a>后续步骤

有关概述，请参阅[企业状态漫游概述](enterprise-state-roaming-overview.md)。
