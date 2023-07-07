---
title: Epicor ERP Customization
date: 2023-05-01
tags: 
- ERP
- Epicor
categories: 
- ERP
- Epicor
---

# Basic Customization
## 初始化客户化功能
### 1. 客户化功能权限
(1). 进入（System Setup > Security Maintenance > User Account Security Maintenance ）菜单

(2). 选择你需要授权的用户ID

(3). 在Options\Tools Options下勾选Customize Privileges选项

(4). 保存并退出ERP重新登录

### 2. 启用开发模式
勾选菜单栏内的开发者模式图标，如下图所示
<img src = '/images/001.jpg' ><br>

### 3. 开启客户化功能
(1) 当开启开发者模式后，在点击需要客户化定制的功能后，系统会弹出Select Customization选项框，如下图所示
<img src = '/images/002.jpg' ><br>
    ① - <br>
    ② 表单名 <br>
    ③ Base Only, 当勾选该项时，则只打开该表单最初始的状态 <br>
    ④ 显示该表单下所有客户化和个性化的相关选择 <br>
    ⑤ 如果需要删除某客户化，选择后点击该按钮即可 <br>
    ⑥ 公司名 <br>
    ⑦ 表单功能描述 <br>
    ⑧ 产品ID <br>
    ⑨ 代码类型（Customization/Personalization）<br>
    ⑩ 国家/组代码 <br>
    ⑪ 父项关联 <br>
    ⑫ 如果自定义是一个正在进行的工作，则会选中WIP复选框。这表示该自定义项尚未准备好显示在菜单上，因此无法通过菜单维护进行选择。 <br>
    ⑬ 从文件内导入客户化表单 <br>
(2) 选择OK后，表单会先以Run Mode启动
(3) 在菜单栏选择Tools > Options取消勾选Memory Cache
(4) 在菜单栏选择Tools > Customization开启客户化定制

### 4. 客户化定制工具
#### (1) Tree View
<img src = '/images/003.jpg' ><br>
    ① 当前页面中选择的元素名称 <br>
    ② 当前页面中选择的元素的表单名称 <br>
    ③ Tree View（可以帮助定位复杂自定义项的元素）<br>
    ④ 表单右侧显示所选项目的属性 <br>

#### (2) Wizards 
略

#### (3) Script Editor
- 用于在当前表单上输入自定义代码的界面。
- 使用此工作表可以输入要在自定义程序中运行的任何高级自定义。

#### (4) Tools Menu
<img src = '/images/004.jpg' ><br>
    ② Test Code - 运行自定义代码，可以检测代码是否存在错误 <br>
    ③ Toolbox - 显示在当前窗体添加元素的控件 <br>
    ④ Assembly Reference Manager - 显示应用程序中使用的所有.dll文件的列表 <br>
    ⑤ Object Explorer - 查看可在自定义代码中使用的各种对象、方法和属性，可以使用此工具查看框架对象、数据对象和适配器程序集。 <br>
    ⑥ Options - <br>
    ⑦ Custom XML Editor  - 显示自定义项的XML代码，使用此疑难解答工具上的工作表可以更正自定义项中出现的任何问题。 <br>
    ⑧ Data Tools - 创建和编辑数据的自定义视图或外键视图，这些视图可以在自定义图纸上访问，也可以在仪表板界面中显示。 <br>
    ⑨ Wizards - 将图像列添加到显示所选应用程序图像的当前网格中，使用自定义代码向导可以在脚本编辑器中创建有效的主要代码段。 <br>
    ⑩ String Manager - 指定如何将特定的自定义文本翻译成不同的语言，这些字符串将显示在翻译实用程序中。 <br>

#### (5) Hide Elements
可以使用“可见”特性隐藏图元。使用此功能可以将元素保留在界面上，同时仍然对用户隐藏它——实际上是禁用它。<br>
Select behavior > Visible > False

#### (6) ToolBox
Select Tools > Toolbox <br>
<img src = '/images/005.jpg' > <br>
- Pointer - 单击此按钮可将鼠标返回到其正常的指针箭头。使用此箭头可以单击并拖动选定图元上的尺寸控制柄。也可以单击并拖动某个元素，将其移动到表单上的新位置。
- EpiLabel - 单击此工具可以为图元创建标签。选中此工具后，在窗体上单击并拖动鼠标指针。此时会显示一个带有调整大小手柄的标签。松开鼠标按钮以放下标签；在所选字段中输入标签的文本。
- EpiTextBox - 单击此工具可创建一个文本框。选中此工具后，在窗体上单击并拖动鼠标指针。此时会出现一个带有调整大小手柄的文本框。释放鼠标按钮将文本框放到表单上。通过定义文本框的EpiBinding属性，可以将文本框绑定到数据库中的字段。
- EpiCheckBox - 选择此工具以创建一个复选框。选中此工具后，在窗体上单击并拖动鼠标指针。此时会出现一个带有调整大小手柄的复选框。松开鼠标按钮，将复选框放到表单上。使用手柄使复选框达到所需的大小。还可以通过定义数据库中的列的EpiBinding属性，将复选框绑定到该列。
- EpiRadioButton - 使用此工具创建一个新的单选按钮（圆形字段，选中后，其中间有一个黑点）。选中此工具后，在窗体上单击并拖动鼠标指针。此时会出现一个带有标签和调整大小手柄的按钮。松开鼠标按钮，将按钮放到窗体上。
- EpiButton - 单击此工具可创建一个新按钮。选中此工具后，在窗体上单击并拖动鼠标指针。此时会出现一个带有调整大小手柄的按钮。松开鼠标按钮，将按钮放到窗体上。使用手柄使按钮达到所需大小。
- EpiUltraCombo - 使用此工具可以创建下拉列表。下拉列表在主字段中显示一个选定的选项，但当用户单击向下箭头按钮时，他们可以从列表中选择另一个选项。您可以通过定义数据库属性的EpiBinding属性将此列表链接到数据库属性，也可以选择它从特定表中检索数据。
- EpiCombo - Combo元素派生自UltraCombo控件。使用此控件可以在不使用适配器的情况下从数据对象中检索数据；这提高了自定义的性能。
- BAQCombo - 使用此工具可以创建一个下拉列表，其中显示所选业务活动查询（BAQ）中的信息。BAQ是可以创建和修改的查询工具；它们会引入您在BAQ设计器中定义的一组数据。在表单上绘制BAQCombo后，指示特定的BAQ和保存数据的ValueMember列。然后定义DisplayMember列，自定义项用于在窗体上的运行时显示数据。
- EpiGroupBox - 单击此工具可创建一个分组框；使用组框将相关元素放置在界面的一个部分中。
- EpiUltraGrid - 使用此工具可以创建栅格。选中此工具后，在窗体上单击并拖动鼠标指针以创建网格。然后，您可以将网格绑定到希望显示的数据。
- EpiUltraComboPlus - 选择此工具可创建导航工具栏。用户可以利用这些工具栏在当前会话期间移动他们拉到窗体上的所有记录。它会自动将“开始”、“上一记录”、“下一记录”和“结束”按钮放在主下拉列表旁边。通过定义UltraComboPlus框的EpiBinding属性，可以将其绑定到数据库列。
- EpiDateTimeEditor - 单击此工具可创建日期/时间字段。选中此工具后，在表单上单击并拖动鼠标指针以创建日期字段。向下箭头按钮也会自动创建；当用户在运行模式下单击此按钮时，将显示“日历”窗口。
- EpiTimeEditor - 使用此工具可以创建时间字段。选中此工具后，在窗体上单击并拖动鼠标指针，以创建一个显示时间值的字段。
- EpiNumericEditor - 单击此工具可创建一个数字字段。选中此工具后，在窗体上单击并拖动鼠标指针以创建字段。此字段仅显示数字数据。
- EpiCurrencyEditor - 选择此工具可在表单上设置货币字段。此字段仅显示货币数据。
- EpiCurrencyConver - 当您需要利用源货币值，然后将此源货币值转换为其他货币的等值时，请在表单上使用此控件。如果您正在开发一个需要以不同货币显示金额的自定义项，请确保对您的自定义项进行此控制。
- EpiShape - 使用此工具可以在界面上创建一个椭圆形图形。将形状放置在界面上后，可以调整其大小并更改其颜色。也可以输入显示在形状内部的文本。
- EpiPictureBox - 单击此工具可以创建一个框架，然后可以显示图形文件。然后，您可以编写自定义代码，在图片框中显示所需的图像。放置图片框时，可以调整其手柄的大小，使其具有包含所选图形文件所需的尺寸。
- EpiRetrieverCombo - 使用此工具可以创建一个下拉列表，从您选择的检索器组合中提取数据。选择此选项并将控件绘制到窗体上时，“选择检索器组合”窗口将显示并加载可用的检索器组合（例如JobEntryCombo）。选择所需的检索器组合，单击“确定”，然后在表单上放置自定义控件。当表单处于运行模式时，此下拉列表将填充所选检索器组合中的数据。
- EpiDayView - 利用此工具将工作表添加到您的自定义中。在运行时，用户单击“日”选项卡可显示一张工作表，其中列出了特定工作日内的所有可用时间。在该特定日期分配的任务和事件显示在此工作表上。
- EpiMonthViewMulti - 使用此工具可以将“日历”窗格添加到您的自定义项中。在运行期间，用户单击此日历窗格上的控件可导航到当前年度、前几年或未来几年中的不同月份。
- EpiMonthViewSingle - 单击此工具可将月份工作表添加到您的自定义项中。在运行期间，用户单击“月份”选项卡可显示一张工作表，其中列出了特定月份的所有可用天数。本月每天分配的任务和事件显示在此工作表上。
- EpiWeekView - 使用此工具可以将周工作表添加到您的自定义项中。在运行时，用户单击此“周”选项卡以显示一张工作表，其中列出了特定一周内的所有可用天数。本周每天分配的任务和事件显示在此工作表上。

# Advanced Customization
## Customizing Alternate Interfaces
1. use the /MESC argument with MES programe<br>
<img src = '/images/006.jpg' > <br>
2. 打开MES，注意表单名称的字段显示:Menu.Mes.MESMenu,选择Base
3. 系统监视器层将显示“选择自定义”窗口，注意表单名称的字段显示：App.SysMonitorEntry.SysMonitorForm
4. 输入Employee ID，点击Log in
5. 将显示另一个“选择自定义”窗口，注意表单名称的字段显示：Menu.Mes.EmployeeSelect
6. 要自定义程序，请单击MES菜单上的按钮即可
7. 点击按钮后，会显示Process Calling，记录显示的Process Key and Calling App values，后面需要使用

## Styling Specific Controls 
[仅经典模式下可用样式定义功能]
1. 在Customization Tools Dialog窗口内定义控制样式
2. Appearance > Use App Styling > True
3. Misc > StyleSetName > [Style Name]
4. Save
5. 创建控制样式
6. 打开客户表单，切换到主菜单目录，通过Options菜单，选择Styling > Runtime Styler
7. 在Runtime Stylist窗口下，点击Add按钮，并输入StyleSet Name，点击OK
8. 根据需求设置对应的样式

## Customization Form Wizards
待完善

## Example Codes
```c#
// 在客户化界面获取当前菜单的Menu ID
txtMenuID.Text = ((EpiDataView)oTrans.EpiDataViews[“CallContextClientData”]).dataView.Table.Rows[0][“ProcessID”].ToString();

// convert time from string to time
// Epicor标准数据库中内置了一个转换函数ice.StringTime
select Ice.StringTime(SysTime,'00:00:00') as TranTime from Erp.PartTran
```