---
title: 平滑流式处理 Windows 应用商店应用教程 | Microsoft Docs
description: 了解如何使用 Azure 媒体服务来创建一个 C# Windows 应用商店应用程序，该应用程序包含一个用于播放平滑流内容的 XML MediaElement 控件。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "69016794"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>如何生成平滑流式处理 Windows 应用商店应用程序  

借助适用于 Windows 8 的平滑流式处理客户端 SDK，开发人员可以生成支持按需付费、直播平滑流式处理内容的 Windows 应用商店应用程序。 除了播放平滑流式处理内容这一基本功能以外，该 SDK 还提供其他丰富功能，例如 Microsoft PlayReady 保护、质量级别限制、实时 DVR、音频流切换、收听状态更新（如质量级别更改）和错误事件，等等。 有关支持的功能的详细信息，请参阅[发行说明](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)。 有关详细信息，请参阅[适用于 Windows 8 的播放器框架](https://playerframework.codeplex.com/)。 

本教程包含四个课时：

1. 创建基本的平滑流式处理应用商店应用程序
2. 添加滚动条以控制媒体进度
3. 选择平滑流式处理流
4. 选择平滑流式处理曲目

## <a name="prerequisites"></a>先决条件
> [!NOTE]
> Visual Studio 2017 不支持 Windows 应用商店项目 8.1 版及更早的版本。  有关详细信息，请参阅 [Visual Studio 2017 平台目标以及兼容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。

* Windows 8 32 位或 64 位。
* Visual Studio 版本 2012 到 2015。
* [适用于 Windows 8 的 Microsoft 平滑流式处理客户端 SDK](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)。

可从 MSDN 开发人员代码示例（代码库）下载每一课后生成的解决方案： 

* [第 1 课](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - 简单的 Windows 8 平滑流式处理媒体播放器， 
* [第 2 课](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - 带滚动条控件的简单 Windows 8 平滑流式处理媒体播放器， 
* [第 3 课](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - 支持流选择的 Windows 8 平滑流式处理媒体播放器，  
* [第 4 课](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - 支持轨迹选择的 Windows 8 平滑流式处理媒体播放器。

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>第 1 课：创建基本的平滑流式处理应用商店应用程序

在本课中，将要使用 MediaElement 控件创建一个 Windows 应用商店应用程序，以播放平滑流内容。  运行的应用程序如下所示：

![平滑流式处理 Windows 应用商店应用程序示例][PlayerApplication]

有关开发 Windows 应用商店应用程序的详细信息，请参阅[开发适用于 Windows 8 的极佳应用](https://msdn.microsoft.com/windows/apps/br229512.aspx)。 本课包含以下过程：

1. 创建 Windows 应用商店项目
2. 设计用户界面 (XAML)
3. 修改代码隐藏文件
4. 编译和测试应用程序

### <a name="to-create-a-windows-store-project"></a>创建 Windows Store 项目

1. 运行 Visual Studio；支持版本 2012 到 2015。
1. 在“文件”菜单中，单击“新建”，并单击“项目”。
1. 在“新建项目”对话框中，键入或选择以下值：

    | 名称 | ReplTest1 |
    | --- | --- |
    | 模板组 |已安装/模板/Visual C#/Windows 应用商店 |
    | 模板 |空白应用程序(XAML) |
    | 姓名 |SSPlayer |
    | Location |C:\SSTutorials |
    | 解决方案名称 |SSPlayer |
    | 创建解决方案的目录 |(选定) |

1. 单击 **“确定”** 。

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>添加对平滑流式处理客户端 SDK 的引用

1. 在解决方案资源管理器中，右键单击“SSPlayer”，并单击“添加引用”。
1. 键入或选择以下值：

    | 名称 | ReplTest1 |
    | --- | --- |
    | 引用组 |Windows/扩展 |
    | 参考 |选择适用于 Windows 8 和 Microsoft Visual C++ 运行时程序包的 Microsoft 平滑流式处理客户端 SDK |

1. 单击 **“确定”** 。 

添加引用后，必须选择目标平台（x64 或 x86），添加引用对于任何 CPU 平台配置都不起作用。  在解决方案资源管理器中，会看到这些添加的引用出现了对应的黄色警告标记。

### <a name="to-design-the-player-user-interface"></a>设计播放器用户界面

1. 在解决方案资源管理器中，双击“MainPage.xaml”以在设计视图中将它打开。
2. 在该 XAML 文件中找到 **&lt;Grid&gt;** 和 **&lt;/Grid&gt;** 标记，并在这两个标记之间粘贴以下代码：

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   MediaElement 控件用于播放媒体。 在下一课，我们将使用名为 sliderProgress 的滚动条控件来控制媒体进度。
3. 按 **CTRL+S** 保存文件。

MediaElement 控件并非原本就支持平滑流式处理内容。 若要启用平滑流式处理支持，必须按文件扩展名和 MIME 类型注册平滑流式处理字节流处理程序。  若要注册，可以使用 Windows.Media 命名空间的 MediaExtensionManager.RegisterByteStreamHandler 方法。

在此 XAML 文件中，某些事件处理程序与控件关联。  必须定义这些事件处理程序。

### <a name="to-modify-the-code-behind-file"></a>修改代码隐藏文件

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在该文件的顶部，添加以下 using 语句：
   
        using Windows.Media;
3. 在 **MainPage** 类的开头，添加以下数据成员：
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. 在 **MainPage** 构造函数的末尾，添加以下两行：
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. 在 MainPage 类的末尾，粘贴以下代码：
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   现已定义 sliderProgress_PointerPressed 事件处理程序。  要使它正常工作，还需要执行其他操作，本教程的下一课将予以介绍。
6. 按 **CTRL+S** 保存文件。

完成的代码隐藏文件应如下所示：

![创建平滑流式处理 Windows 应用商店应用程序时 Visual Studio 中的代码视图][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>编译和测试应用程序

1. 在“生成”菜单中，单击“配置管理器”。
2. 更改“活动解决方案平台”以匹配开发平台。
3. 按 **F6** 编译项目。 
4. 按 **F5** 运行应用程序。
5. 在应用程序的顶部，可以使用默认的平滑流式处理 URL，或输入一个不同的 URL。 
6. 单击“设置源”。 由于已按默认启用“自动播放”，因此媒体会自动播放。  可以使用“播放”、“暂停”和“停止”按钮控制媒体。  可以使用垂直滚动条控制媒体音量。  但是，用于控制媒体进度的水平滚动条功能尚未完全实现。 

第 1 课到此结束。  在本课中，已学习如何使用 MediaElement 控件来播放平滑流式处理内容。  在下一课，将要添加一个滚动条，用于控制平滑流式处理内容的进度。

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>第 2 课：添加滚动条以控制媒体进度

在第 1 课，已使用 MediaElement XAML 控件创建了一个 Windows 应用商店应用程序，用于播放平滑流式处理媒体内容。  该应用程序带有基本的媒体功能，例如开始、停止和暂停。  在本课中，将要在该应用程序中添加一个滚动条控件。

在本教程中，我们将使用一个计时器，基于 MediaElement 控件的当前位置更新该滚动条的位置。  在播放实况内容时，滚动条开始时间和结束时间也需要更新。  可以在自适应源更新事件中更好地处理此操作。

媒体源是生成媒体数据的对象。  源解析程序采用 URL 或字节流，并为该内容创建相应的媒体源。  源解析程序是应用程序创建媒体源的标准途径。 

本课包含以下过程：

1. 注册平滑流式处理处理程序 
2. 添加自适应源管理器级别事件处理程序
3. 添加自适应源级别事件处理程序
4. 添加 MediaElement 事件处理程序
5. 添加滚动条相关的代码
6. 编译和测试应用程序

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>注册平滑流式处理字节流处理程序并传递属性集

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在该文件的开头，添加以下 using 语句：

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. 在 MainPage 类的开头，添加以下数据成员：

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. 在 **MainPage** 构造函数中的 **this.Initialize Components();** 行以及在上一课编写的注册代码行的后面添加以下代码：

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. 在 **MainPage** 构造函数中，修改两个 RegisterByteStreamHandler 方法以添加第四个参数：

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. 按 **CTRL+S** 保存文件。

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>添加自适应源管理器级别事件处理程序

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在 **MainPage** 类中，添加以下数据成员：

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. 在 **MainPage** 类的末尾，添加以下事件处理程序：

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. 在 **MainPage** 构造函数的末尾，添加以下行以订阅自适应源打开事件：

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. 按 **CTRL+S** 保存文件。

### <a name="to-add-adaptive-source-level-event-handlers"></a>添加自适应源级别事件处理程序

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在 **MainPage** 类中，添加以下数据成员：

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. 在 **MainPage** 类的末尾，添加以下事件处理程序：

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. 在 **mediaElement AdaptiveSourceOpened** 方法的末尾，添加以下代码以订阅事件：

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. 按 **CTRL+S** 保存文件。

相同的事件也可以在自适应源管理器级别使用，因此可用于处理应用程序中所有媒体元素通用的功能。 每个 AdaptiveSource 包含其自身的事件，所有 AdaptiveSource 事件将级联在 AdaptiveSourceManager 下面。

### <a name="to-add-media-element-event-handlers"></a>添加媒体元素事件处理程序

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在 **MainPage** 类的末尾，添加以下事件处理程序：

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. 在 **MainPage** 构造函数的末尾，添加以下代码以订阅事件：

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. 按 **CTRL+S** 保存文件。

### <a name="to-add-slider-bar-related-code"></a>添加与滚动条相关的代码

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在该文件的开头，添加以下 using 语句：

   ```csharp
        using Windows.UI.Core;
   ```
3. 在 **MainPage** 类中，添加以下数据成员：

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. 在 **MainPage** 构造函数的末尾，添加以下代码：

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. 在 **MainPage** 类的末尾，添加以下代码：

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher 用于从非 UI 线程对 UI 线程作出更改。 如果调度程序线程出现瓶颈, 开发人员可以选择使用要更新的 UI 元素提供的调度程序。  例如：

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. 在 mediaElement_AdaptiveSourceStatusUpdated 方法的末尾，添加以下代码：

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. 在 **MediaOpened** 方法的末尾，添加以下代码：

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. 按 **CTRL+S** 保存文件。

### <a name="to-compile-and-test-the-application"></a>编译和测试应用程序

1. 按 **F6** 编译项目。 
2. 按 **F5** 运行应用程序。
3. 在应用程序的顶部，可以使用默认的平滑流式处理 URL，或输入一个不同的 URL。 
4. 单击“设置源”。 
5. 测试滚动条。

已完成第 2 课。  在本课中，已将一个滑块添加到应用程序。 

## <a name="lesson-3-select-smooth-streaming-streams"></a>第 3 课：选择平滑流式处理流
平滑流式处理可以流送包含观看者可选择的多语言音频曲目的内容。  在本课中，将要学习如何使观看者能够选择流。 本课包含以下过程：

1. 修改 XAML 文件
2. 修改代码隐藏文件
3. 编译和测试应用程序

### <a name="to-modify-the-xaml-file"></a>修改 XAML 文件

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看设计器”。
2. 找到 &lt;Grid.RowDefinitions&gt;，并按如下所示修改 RowDefinitions：

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. 在 &lt;Grid&gt;&lt;/Grid&gt; 标记中，添加以下代码以定义一个列表框控件，使用户能够看到可用流的列表及选择流：

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. 按 **CTRL+S** 保存更改。

### <a name="to-modify-the-code-behind-file"></a>修改代码隐藏文件

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在 SSPlayer 命名空间中添加一个新类：

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. 在 MainPage 类的开头，添加以下变量定义：

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. 在 MainPage 类中，添加以下区域：
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. 找到 mediaElement_ManifestReady 方法，并在函数的末尾追加以下代码：
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    因此，当 MediaElement 清单准备就绪时，该代码将获取可用流的列表，并将该列表的内容填充到 UI 列表框。
6. 在 MainPage 类中，找到 UI 按钮单击事件区域，并添加以下函数定义：
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>编译和测试应用程序

1. 按 **F6** 编译项目。 
2. 按 **F5** 运行应用程序。
3. 在应用程序的顶部，可以使用默认的平滑流式处理 URL，或输入一个不同的 URL。 
4. 单击“设置源”。 
5. 默认语言为 audio_eng。 尝试在 audio_eng 和 audio_es 之间切换。 每次选择一个新流时，都必须单击“提交”按钮。

已完成第 3 课。  在本课中，已添加了用于选择流的功能。

## <a name="lesson-4-select-smooth-streaming-tracks"></a>第 4 课：选择平滑流式处理曲目

平滑流式处理演播内容可能包含以不同质量级别（比特率）和分辨率编码的多个视频文件。 在本课中，将要学习如何使用户能够选择曲目。 本课包含以下过程：

1. 修改 XAML 文件
2. 修改代码隐藏文件
3. 编译和测试应用程序

### <a name="to-modify-the-xaml-file"></a>修改 XAML 文件

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看设计器”。
2. 找到名为 **gridStreamAndBitrateSelection** 的 &lt;Grid&gt; 标记，并在该标记的末尾追加以下代码：
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. 按**CTRL + S**保存更改

### <a name="to-modify-the-code-behind-file"></a>若要修改代码隐藏文件，请执行以下操作

1. 在解决方案资源管理器中，右键单击“MainPage.xaml”，并单击“查看代码”。
2. 在 SSPlayer 命名空间中添加一个新类：
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. 在 MainPage 类的开头，添加以下变量定义：
   ```csharp
        private List<Track> availableTracks;
   ```
4. 在 MainPage 类中，添加以下区域：
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. 找到 mediaElement_ManifestReady 方法，并在函数的末尾追加以下代码：
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. 在 MainPage 类中，找到 UI 按钮单击事件区域，并添加以下函数定义：
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>编译和测试应用程序

1. 按 **F6** 编译项目。 
2. 按 **F5** 运行应用程序。
3. 在应用程序的顶部，可以使用默认的平滑流式处理 URL，或输入一个不同的 URL。 
4. 单击“设置源”。 
5. 默认情况下，已选中视频流的所有曲目。 要体验比特率的变化，可以先选择最低的可用比特率，再选择最高的可用比特率。 每次更改后都必须单击“提交”。  可以看到视频质量的变化。

已完成第 4 课。  在本课中，已添加了用于选择曲目的功能。

## <a name="media-services-learning-paths"></a>媒体服务学习路径

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>其他资源：
* [如何生成具有高级功能的平滑流式处理 Windows 8 JavaScript 应用程序](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [平滑流式处理技术概述](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

