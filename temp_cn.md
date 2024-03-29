![WechatIMG12](https://github.com/vickyqu115/riotslider/assets/101777355/b686d831-2de6-410b-949d-52b6b9993873)
## Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control

在WPF中，像Button和Toggle Button等这一类的基本控件的Template在结构和逻辑上都设计得非常简单和直观，（无需单独的代码处理）仅通过XAML就可以轻松实现。
而像TextBox、ComboBox、Slider这一类更加结构化的控件（不仅仅是XAML）则就必须通过C#代码进行复杂处理之后才可以实现。

因此，当我们处理复合控件时，如果能够很好地理解WPF控件的详细结构，并能够应用这些知识，就可以更加灵活地设计和开发CustomControl了。
此外，能够熟练掌握控件的基本组成部分，自然也就可以从新的角度来弥补MVVM开发模式中的不足，从而实现更加高质量的WPF应用程序。

因此这个项目中，我们将通过查看WPF提供的Slider控件，来深入了解WPF控件的内部机制设计，并对其进行详细的分析。
当然，想要一一查看和了解WPF所有控件的内部详情是比较困难的，当下我们也没有这样的必要。
因为WPF的所有源代码都通过GitHub仓库以开源形式公开和管理。因此我们可以根据需要随时访问GitHub仓库来查找和分析需要了解的控件。
不仅如此，未来我们还计划分析和解剖比Slider控件更加复杂和多样的控件，所以希望大家能够多多支持、关注和鼓励我们在在GitHub、BiliBili等其它平台上提供的教程内容。

![20240201163433798](https://github.com/vickyqu115/riotslider/assets/101777355/f30773e5-9ad3-4b19-b104-5c026af2577e)

## 目录

1.WPF系列教程

2.Specification

3.创建应用程序

4.分析Slider主要功能

5.原始样式提取过程

6.分析内部源代码

7.确认Code behind（GitHub开源）

8.跨平台中的 OnApplyTemplate

9.结束Slider分析

10.创建Riot风格的Slider控件（CustomControl）

11.创建项目与准备

12.TextBlock（Hi Slider）

13.添加引用并测试

14.设置Riot Slider大小

15.PART_Track

16.添加滑动条

17.调整滑动条和Track之间的误差间隙

18.PART_SelectionRange

19.添加Riot风格的设计元素

20.实现Riot风格的Thumb

21.声明Thumb资源

22.RiotSlider Template完成（总结）

23.结束语


## 1.WPF Tutorial Series

> 目前为止，我们在YouTube和BiliBili平台上共发布了四个中英文（YouTube上配有韩文字幕）教程系列。我们希望通过视频的形式将复杂的源代码更加整体化具像化地详细讲解，以便于能够增强大家对WPF的理解。

- [x] Theme Switch: [Youtube](https://youtu.be/rGox76Bm6VY?si=jTNutyRFctAfTTeS), [BiliBili](https://www.bilibili.com/video/BV1ez4y1N7B8/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5372268/Use-WPF-to-Create-a-Day-and-Night-Theme-Switch-But), [GitHub](https://github.com/vickyqu115/themeswitch)

- [x] Riot PlayButton: [Youtube](https://youtu.be/xgUqDavCJGg?si=Ok8qZjNQUKUEbgfT), [BiliBili](https://www.bilibili.com/video/BV1Tu4y1j7Ei/?spm_id_from=333.788.recommend_more_video.-1&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5373396/Creating-a-League-of-Legends-Inspired-Play-Button), [GitHub](https://github.com/vickyqu115/riotplaybutton)

- [x] Magic Navigation Bar: [Youtube](https://youtu.be/dxuLWlukthg?si=CpvQ-TJ2tQu9sY3F), [BiliBili](https://www.bilibili.com/video/BV1Ui4y1a717/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5375482/Customizing-ListBox-for-a-Smooth-Animated-Navigati), [GitHub](https://github.com/vickyqu115/navigationbar)

- [x] Riot Slider: [Youtube](https://www.youtube.com/watch?v=jyv2fP9TUtY) [BiliBili](https://www.bilibili.com/video/BV1uy421a7yM/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5377407/Analyzing-and-Customizing-the-Detailed-Mechanisms), [GitHub](https://github.com/vickyqu115/riotslider)

## 2.Specifications：
如果你不熟悉WPF平台，可能会会不太适应这样的开发环境。该项目基于.NET 8.0，但由于使用的是WPF，框架目标仅限于Windows，所以只能在Windows环境下开发和运行。IDE可以选择Visual Studio或JetBrains公司提供的Rider。使用Visual Studio时，版本必须要在VS2022以上才可运行.NET 8.0。因此，如果你使用的是VS2022以下版本，需要将.NET版本迁移到与你的IDE版本相匹配。

- [x] OS: Microsoft Windows 11
- [x] IDE: Microsoft Visual Studio 2022
- [x] Version: C# / NET 8.0 / WPF / windows target only
- [x] NuGet: Jamesnet.Wpf

我们推荐使用最新版本的Windows操作系统。不过，如果考虑到将平台扩展到Avalonia UI、Uno Platform、MAUI等，也完全可以考虑将MacOS作为一个次要设备。我们也同时在使用Thinkpad和Macbook。但是请注意，在MacOS或基于Linux的系统上不能使用Visual Studio，因此Rider是唯一的选择。~~vscode~~

## 3. 创建应用程序项目

首先，需要创建一个WPF应用程序项目。

- [x] 项目类型：WPF应用程序

- [x] 项目名称：DemoApp

- [x] 项目版本：.NET 8.0

## 4.分析Slider的主要功能

与Button等简单控件不同，WPF的Slider控件存在许多不同的属性。特别是，这些属性在控件的功能性方面扮演着重要的角色，因此需要仔细研究，其中一些特别行为的主要属性如下：

**Orientation:** 

WPF提供的控件通常具有通用性质。Slider控件中的Orientation属性就是一个例子。通过这个属性，可以将方向指定为水平或垂直方向。

Orientation属性也可以在StackPanel控件中找到。StackPanel的默认Orientation值是Vertical，但Slider的默认Orientation值是Horizontal。因此，通常情况下，Slider是被默认为水平状态，所以大多数人可能会忽略这个Orientation的功能。

下面我们通过一个简化的Slider部分代码来帮助理解Orientation功能。

```xaml
<Style TargetType="{x:Type Slider}">
    <Setter Property="Template" Value="{StaticResource SliderHorizontal}"/>
    <Style.Triggers>
    	<Trigger Property="Orientation" Value="Vertical">
    	    <Setter Property="Template" Value="{StaticResource SliderVertical}"/>
    	</Trigger>
    </Style.Triggers>
</Style>
```

根据Orientation属性，可以看到触发器（ControlTemplate）中模板的切换。因此，稍微研究一下这个控件的详细配置，就可以很容易地理解Orientation这个属性充当了一个相当重要的角色。

> 这是一个比较有意思的部分。在看到原始代码之前，你能想象到通过Orientation来切换模板的应用吗？其实开源项目就是这样给我们带来各种灵感。并且，通过这段源代码，我们也可以检查到"Style.Trigger"正是切换模板的最佳时机。

在本教程视频中，我们只计划实现水平（Horizontal）方向，因此不会实现通过Orientation进行分支切换的操作。但是我们鼓励大家进行垂直（Vertical）方向的尝试，并通过Fork提交Pull Request请求。这是一个小任务哦～

那么下面我们就来看一下水平/垂直属性各自应用的样子吧！
- [x] Orientation: **Horizontal**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300" style="float:left"/>

> 下面也会涉及到选择范围（SelectionRange，蓝色）区域。

- [x] Orientation: Vertical

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/bbcbeaa9-1763-4435-b92b-be2a71a5ee73" width="300" style="float:left"/>

如上所示，我们可以发现更多通过（ControlTemplate）模板切换的类似控件存在（例如：ScrollViewer等）。

##### **Minimum, Maximum以及Value:**

这些是表示最小范围/最大范围以及值的double类型属性。内部逻辑是根据这些值自动计算控件的大小和比例，进而确定Range和Value值的位置。

由于这些属性都是DependencyProperty，因此可以通过绑定实现动态交互。例如，在MVVM架构中，可以利用这三个值根据特定场景动态更改Range，或通过各种应用实现很多有趣的功能。

##### SelectionStart, SelectionEnd以及IsSelectionRangeEnabled:

这两个属性（SelectionStart/SelectionEnd）的作用是设置特殊区域。但是其实这个区域并不包含任何特殊功能。仅仅是起到为了指定某个区间并在视觉上进行强调的作用。而IsSelectionRangeEnabled属性表示这个区域是否激活。根据这个激活状态，通过触发器切换区域的Visibility属性值（Visible/Collapsed）。

那你可以能会疑问，尽然没有什么实际的功能，还有必要使用这个功能吗？
其实，考虑到其在设计和不同领域的通用性，如果将其与Value值一起应用，可以实现非常有趣的效果，就像下面这样。

```xaml
<Slider Orientation="Horizontal"
        Minimum="0"
        Maximum="100"
        Value="30"
        SelectionStart="0"
        SelectionEnd="{Binding Value, RelativeSource={RelativeSource Self}"
        IsSelectionRangeEnabled="True"/>
```

此外有意思的是，通过SelectionEnd的绑定，Value值的变化可以动态地改变选择（Range）范围。
*这是部分也是WPF开发团队早前的预期的吗？是真的非常酷！实现方式也非常干净利索，是非常好的编程体验。

> 在文章后半部分将会实现一个Riot风格的Slider（CustomControl），以上这个功能将起到非常关键的作用，请留意这一点。

## 5. 提取原始样式的过程

如上所述，由于WPF是通过GitHub仓库以开源方式管理的，因此可以查看所有控件的源代码。但是，由于仓库包含了解决方案和所有项目及文件，因此仅提取特定控件部分的内容几乎是不可能的。

但幸运的是，Visual Studio以GUI形式提供了提取特定控件的基本样式（Template）的功能。因此，我们无需通过逐个查找开源代码这样的过程，就可以轻松简单地利用这一功能来实现提取相关代码了。

> 这也让我们想到了Blazor中的Identity脚手架（尽管性质略有不同，但是可以帮助理解）。

此外，通过Visual Studio提取原始样式后，因为它是以实际可编辑的资源形式链接，所以可以立即自定义设计和功能。因此，不仅仅是Slider，
**所有控件的原始样式和模板的提取，对于WPF研究/学习具有非常高的应用价值**

> Infragistics、Syncfusion、ArticPro等并不是所有的商业组件都提供功能。每个公司公开的范围和政策各不相同，大多数情况下，相比于公开ControlTemplate，更倾向于通过DataTemplate模块化来促进自定义。所以大家也可以看一下，目前正在使用的组件是否具有这样的功能呢？

##### 提取方法和步骤：Visual Studio

 - [x] 提取基本控件（Slider）样式（Edit a Copy...）
 - [x] 提取到当前文件（This document）
 - [x] 提取到App.xaml文件（Application）
 - [x] 创建新的ResourceDictionary文件并提取（Resource Dictionary）

提取过程仅能在Partial形式的UserControl设计区进行，并通过选择控件、右击来进行。在这个过程中，可以“指定样式名称/确定提取样式的复制位置”。

> 可以试着查找一下VScode或Rider是否也提供了这样的方法。

我们来逐步查看一下这个过程

- [x] 提取样式命令：Slider > Right click > Edit Template > Edit a Copy...

<img width="365" alt="image" src="https://github.com/vickyqu115/riotslider/assets/52397976/0d7f2e38-f616-4260-a256-f3e4eb5c9f09" style="float:left">

> 如果没有可提取的样式，这个选项将不会被激活。

 - [x] 提取样式选项窗口：Create ControlTemplate Resource (Window)

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/d895d1fd-f709-4909-a968-3cd4692550ac" style="float: left; width: 500px"/>

> Name (Key) & 定义Define in 选项

通常，指定Name在测试或管理层面上来说都是很正确的选择。如果选择“Apply to all”，而不指定名称，那么通过Define选项指定的位置生成的样式将应用在全局。所以需要正确理解这一点后谨慎进行提取。

视频中设置了名称，并将Define位置指定为Application。因此，（如果文件存在）提取的资源将包含在App.xaml文件的Resources区域内。

个人建议在进行此类提取操作时，最好在新项目中以测试性质进行。在现有项目中进行操作的话可能会导致小错误或问题。

## 6. 分析提取的源代码

如教程视频所示，我们成功提取了Slider控件的样式。接下来我们将在App.xaml文件中检查相关资源，并逐一研究##### 重要元素。

##### 检查Orientation分支：

我们之前简要提到了Orientation属性以及触发器和切换，那么现在就是时候查看实际实现的源代码了。

下面的样式是包含在WPF基础样式原版中的提取出的SliderStyle1模板。


```xaml
<Style x:Key="SliderStyle1" TargetType="{x:Type Slider}">
    <Setter Property="Stylus.IsPressAndHoldEnabled" Value="false"/>
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="BorderBrush" Value="Transparent"/>
    <Setter Property="Foreground" 
            Value="{StaticResource SliderThumb.Static.Foreground}"/>
    <Setter Property="Template" Value="{StaticResource SliderHorizontal}"/>
    <Style.Triggers>
        <Trigger Property="Orientation" Value="Vertical">
            <Setter Property="Template" Value="{StaticResource SliderVertical}"/>
        </Trigger>
    </Style.Triggers>
</Style>
```

通过以上内容，我们可以发现它将默认的模板指定为了SliderHorizontal (ControlTemplate)模板的，通过触发器当Orientation属性值为Vertical时，会切换到SliderVertical (ControlTemplate)模板。

> 通过这样将(ControlTemplate)模板模块化管理，即使使用不切换情况下，也有助于一目了然地看到实际样式的结构，这是一种值得尝试的结构管理方式。
我们的项目中也经常会这样处理，通过查看这样的代码获得了非常多的灵感。

因此，与Slider控件相关的功能实质上分别在SliderHorizontal和SliderVertical这两个(ControlTemplate)模板区域内实现了。

下一步，我们来确认一下默认指定的SliderHorizontal (ControlTemplate)模板。

##### 确认ControlTemplate:

我们来看一下Horizontal/Vertical各自专用的模板。这些内容都可以在App.xaml文件内找到。

 - [x] Horizontal专用模板
 - [x] Vertical专用模板


ControlTemplate: **SliderHorizontal**

```xaml
<ControlTemplate x:Key="SliderHorizontal" TargetType="{x:Type Slider}">
    <Border ...>
		...
    </Border>
    <ControlTemplate.Triggers>
		...
    </ControlTemplate.Triggers>
</ControlTemplate>
```



ControlTemplate: **SliderVertical**

```xaml
<ControlTemplate x:Key="SliderVertical" TargetType="{x:Type Slider}">
    <Border ...>
		...
    </Border>
    <ControlTemplate.Triggers>
		...
    </ControlTemplate.Triggers>
</ControlTemplate>
```

如上所示，可以确认到的是Horizontal/Vertical各自的源代码是分支实现的。因此，构造方便的内容是一直的，只是设计层面的方向不同而已。

让我们准确地来查看一下。共同包含的元素如下：

- [ ] Name: TopTick
- [ ] Name: BottomTick
- [ ] Name: TrackBackground
- [x] **Name: PART_SelectionRange**
- [x] **Name: PART_Track**
- [ ] Name: Thumb
- [ ] Trigger: TickPlacement
- [ ] Trigger: IsSelectionRangeEnabled
- [ ] Trigger: IsKeyboardFocused


每个ControlTemplate中都包含了以上这些共同的元素。既然我们已经确认了它们都有相同的配置，那我们就来集中查看一下SliderHorizontal的部分。

##### 命名规则：PART_
在(CustomControl)控件结构中，XAML和Code behind之间的紧密连接是一个非常重要的元素。两者连接时需要通过GetTemplateChild方法来查找控件名称，那这样的方式在可读性层面上看起来就不是很好了。所以为了完善这种开发方式并进行系统化管理，这里我们可以使用“PART_”命名规则。

“PART_”是一个命名规则。其方法是在所有通过GetTemplateChild方法查找的控件名称前加上“PART_”前缀，以便在XAML中推测其功能。因此，当分析(ControlTemplate)控件时，如果发现是以“PART_”开始的控件名称，则可以推测这是一个必需的元素，并预测删除它时可能会产生的副作用。

所以总的来说，了解这个性能对于CustomControl的实现是很有帮助的。此外，这个规则不仅在WPF中很常见，也是XAML共享的其他跨平台中常见的一种结构，因此这一部分内容的重要性是不容忽视的。

_Slider中存在两个PART_控件。_

 - [x] PART_Track
 - [x] PART_SelectionRange

此外，除了上述两个“PART_”控件之外的其他控件在Code behind中并不使用，所以我们通过命名规则确保了这一点。
所以在CustomControl开发中严格遵守这个规则是非常重要的。

##### 测试：更改PART_Track名称后测试其影响

让我们更改PART_Track控件的名称。

```xaml
<Track x:Name="PART_Track1" Grid.Row="1">
    ...
</Track>
```

> 确认Sliderhorizontal区域显示是否正确（x1）

此状态下运行应用程序，就像教程视频中显示的一样，无论如何通过尝试移动Track的Thumb，它都将不会移动。而Thumb不再移动的原因是，则正是由于我们更改了名称，所以导致Code behind区域通过GetTemplateChild无法找到PART_Track控件，当然也就不能实现PART_Track功能了。

但如果将PART_Track1的名称改回原来的PART_Track，功能就能恢复正常了。

> 这种现象在许多其他基本控件中也可以找到，其中比较典型例子就是TextBox的“PART_ContentHost”。

##### 测试: PART_SelectionRange变更名称后的影响

接下来，让我们来测试一下改变PART_SelectionRange 控件的名称后的影响。

```xaml
<Rectangle x:Name="PART_SelectionRange1" .../>
```

> 确认Sliderhorizontal区域显示是否正确（x2）

并且，在Trigger部分，我们可以看到PART_SelectionRange还使用在了这个地方，所以这一部分我们也需要一并修改。

```xaml
<Trigger Property="IsSelectionRangeEnabled" Value="true">
    <Setter Property="Visibility" TargetName="PART_SelectionRange1" Value="Visible"/>
</Trigger>
```

> 确认Sliderhorizontal区域显示是否正确(x3)

此外，在 Slider 中，为了能激活PART_SelectionRange范围以便激活范围区域。

```xaml
<Slider Style="{DynamicResource SliderStyle1}"
        Minimum="0" Maximum="100"
        SelectionStart="0" SelectionEnd="50"
        IsSelectionRangeEnabled="True"/>
```

必须设置 Minimum/Maximum 以及 SelectionStart/SelectionEnd 和 IsSelectionRange，才能激活范围区域。

 更改前的名称：PART_SelectionRange
在更改之前，可以正常看到 Range 区域。

 更改后的名称：PART_SelectionRange1
现在，范围区域不再可见。

这是因为内部无法找到 PART_SelectionRange 控件，所以没有对象来计算范围区域。

如此，WPF 控件实现上比想象的更为宽松，同时构成了一定的模块化结构。因此，如果正确利用这些特性，可以很好地利用已实现的功能，或者排除不必要的功能。

7. Code behind 检查 (GitHub 开源)
既然我们已经详细查看了 PART_ 控件的命名规则及其影响，现在是时候查看实际类中如何使用这些控件了。

Code behind (类) 区域不再是可以通过提取来检查的区域。因此，需要通过查看 WPF 仓库的官方源代码来进一步研究。具体查找方法建议观看教程视频以获取更详细的信息。

在实际的源代码中，各个 PART_ 控件的名称如下以 string 形式约定：

csharp
Copy code
private const string TrackName = "PART_Track";
private const string SelectionRangeElementName = "PART_SelectionRange";
由于名称是固定定义的，因此这是必须遵守的命名规则。

WPF: OnApplyTemplate
接下来，让我们看看如何从 (ControlTemplate) 模板中获取 Track 和 SelectionRange 的部分。





