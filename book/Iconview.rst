.. vim: syntax=rst

图标控件
----

图标（Iconview）控件可用来设置基于图标的菜单，这些菜单在手机和电脑上非常常见，使用也很频繁。 它可以显示图标列表，其中每个图标都可以选择使用文本标记。 图标控件支持透明度和Alpha混合，因此桌面背景可以不受遮挡的显示内容。
当前被选中的图标可以用纯色或alpha混合效果突出显示。如有需要还可以使用滚动条显示更多图标。

图标控件没有固定外观，具体显示什么样的内容完全由用户决定，不过它的结构是不变的，例如带透明度的图标控件如图 30‑1所示。

|Iconvi002|

图 30‑1 带透明度的图标控件样式

图标控件支持5种通知代码，以区分各种不同的操作动作，见表格 30‑1。

表格 30‑1 图标控件支持的通知代码

============================== ========================================
通知代码                       描述
============================== ========================================
WM_NOTIFICATION_CLICKED        控件已被点击
WM_NOTIFICATION_RELEASED       控件已被点击并释放
WM_NOTIFICATION_MOVED_OUT      控件已被点击，但指针已被移出框且没有释放
WM_NOTIFICATION_SCROLL_CHANGED 滚动条的滚动位置已更改
WM_NOTIFICATION_SEL_CHANGED    控件的选择值已更改
============================== ========================================

这5种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器会让图标控件向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

同时图标控件也支持输入焦点，它可以接收如所示的按键消息。

表格 30‑2 图标控件支持的按键消息

============= ==========================
按键消息      描述
============= ==========================
GUI_KEY_RIGHT 将所选内容移到下一个图标
GUI_KEY_LEFT  将所选内容移至上一个图标
GUI_KEY_DOWN  向下移动选择
GUI_KEY_UP    向上移动选择
GUI_KEY_HOME  将所选内容移到第一个图标
GUI_KEY_END   将所选内容移到最后一个图标
============= ==========================

创建图标控件
~~~~~~

图标控件API
^^^^^^^

表格 30‑3 图标控件API

=================================== ====================================
函数名                              描述
=================================== ====================================
ICONVIEW_AddBitmapItem()            向图标控件添加新图标
ICONVIEW_AddStreamedBitmapItem()    使用流位图将新图标添加到控件
ICONVIEW_CreateEx()                 创建一个图标控件
ICONVIEW_CreateIndirect()           从对话框资源表中创建图标控件
ICONVIEW_CreateUser()               使用额外字节作为用户数据创建图标控件
ICONVIEW_GetBkColor()               返回当前背景颜色
ICONVIEW_GetFont()                  范围当前字体
ICONVIEW_DeleteItem()               删除现有项目
ICONVIEW_EnableStreamAuto()         启用对流位图的完全支持
ICONVIEW_GetItemBitmap()            返回指向给定项目的位图的指针
ICONVIEW_GetItemText()              检索指定项的文本
ICONVIEW_GetItemUserData()          从特定项目中检索先前存储的用户数据
ICONVIEW_GetNumItems()              返回项目数
ICONVIEW_GetReleasedItem()          返回已被释放项目的索引
ICONVIEW_GetSel()                   返回当前选定图标的索引
ICONVIEW_GetTextColor()             返回文本颜色
ICONVIEW_GetUserData()              检索用户数据集
ICONVIEW_InsertBitmapItem()         在控件的给定位置中插入新图标
ICONVIEW_InsertStreamedBitmapItem() 在控件的给定位置中插入新流位图图标
ICONVIEW_OwnerDraw()                绘制控件的默认函数
ICONVIEW_SetBitmapItem()            设置要由特定项目使用的位图
ICONVIEW_SetBkColor()               设置背景颜色
ICONVIEW_SetFont()                  设置文本字体
ICONVIEW_SetFrame()                 设置边框和图标之间的框架大小
ICONVIEW_SetIconAlign()             设置图标对齐方式
ICONVIEW_SetItemText()              设置指定选项的文本
ICONVIEW_SetItemUserData()          将用户数据存储在特定项目中
ICONVIEW_SetOwnerDraw()             设置自定义的绘图函数
ICONVIEW_SetSel()                   设置当前选择
ICONVIEW_SetSpace()                 在x或y方向上设置图标之间的间隔
ICONVIEW_SetStreamedBitmapItem()    设置要由特定项目使用的流位图
ICONVIEW_SetTextAlign()             设置文本对齐方式
ICONVIEW_SetTextColor()             设置文本颜色
ICONVIEW_SetUserData()              设置额外用户数据集
ICONVIEW_SetWrapMode()              设置控件换行模式
=================================== ====================================

图标控件创建函数
^^^^^^^^

ICONVIEW_CreateEx()
'''''''''''''''''''

在指定位置创建指定大小的图标控件。

代码清单 30‑1 函数原型

1 ICONVIEW_Handle ICONVIEW_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id, int xSizeItems, int

4 ySizeItems);

1)  x0：图标控件在父坐标中的最左侧像素；

2)  y0：图标控件在父坐标中的最顶侧像素；

3)  xSize：图标控件的水平尺寸，以像素为单位；

4)  ySize：图标控件的垂直尺寸，以像素为单位；

5)  hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6)  WinFlags：窗口创建标志。由于控件本质上是窗口，所以图标控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7)  ExFlags：额外创建标志，用于选择是否使用滚动条；

8)  Id：控件ID号；

9)  xSizeItem：单个图标的水平尺寸，以像素为单位；

10) ySizeItem：单个图标的垂直尺寸，以像素为单位。

返回值：创建成功后返回已创建的图标控件句柄，创建失败则返回0。

ICONVIEW_CreateIndirect()
'''''''''''''''''''''''''

从对话框资源表中创建图标控件。

代码清单 30‑2 函数原型

1 ICONVIEW_Handle ICONVIEW_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN

3 hWinParent, int x0, int y0,

4 WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：图标控件在父坐标中的最左边像素；

4) y0：图标控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，把结构中的para参数分为高16位和低16位，分别对应ySizeItem和xSizeItem。其中的Flags参数可选值与ICONVIEW_CreateEx()函数的ExFlags参数相同，用于设置是否启用滚动条。

图标制作
^^^^

图标控件所用图标的制作过程与控件贴图和显示位图的有些许不同，我们讲解一下专用于这个控件的图标制作步骤。

1) 添加图像文件。点击File->Open添加一张PNG格式的图标文件，也可直接将图片拖到转换器空白处添加，见图 30‑2；

|Iconvi003|

图 30‑2 添加PNG文件

2) 保存C文件。点击File->Save As，在弹出来的保存对话框中，选择需要保存的路径，这里我们保存到图标文件夹，修改文件名，选择保存类型为.C文件。见图 30‑3；

|Iconvi004|

图 30‑3 保存C文件

3) 选择C文件的颜色格式。在保存对话框中点击确定后，会弹出一个选择颜色格式的窗口，这里我们选择High color with Alpah(565)，见图 30‑4。

|Iconvi005|

图 30‑4 选择颜色格式

通过上述步骤，即可成功制作1个PNG格式图标的C数组文件。如果需要生成.dta格式的流位图，在保存文件时选择保存类型为.dta文件即可。

而背景图片通常由于分辨率比较高、色彩深度较大等原因，制作出来的C文件体积会很大，根本无法放进芯片的内部FLASH中。对于背景图片，建议的做法是将图片源文件存放在外部FLASH或SD卡中，系统启动时再加载到RAM，这样就不用担心内部FLASH空间不足的问题了。

图标控件基础实验
~~~~~~~~

接下来我们通过一个实验来讲解如何创建一个只包含基本功能的图图标控件，完整的图标控件演示实验可参考官方例程WIDGET_Iconview.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\WIDGET_IconView

代码分析
^^^^

本实验与其他实验稍有不同，由于图标控件自身特性的限制，使用对话框的方式间接创建出来的图标控件整体效果不太好，所以这次实验我们使用直接创建的方式。

(1) MainTask函数

既然是用直接方式创建图标控件，那就不再需要对话框资源表了，直接在GUI主任务中创建，见代码清单 30‑3。

代码清单 30‑3 MainTask函数（IconviewDLG.c文件）

1 /\* 引用图像数据 \*/

2 extern GUI_CONST_STORAGE GUI_BITMAP bmsetting;

3 extern GUI_CONST_STORAGE GUI_BITMAP bmclock;

4 extern GUI_CONST_STORAGE GUI_BITMAP bmcalender;

5 extern GUI_CONST_STORAGE GUI_BITMAP bmmusic;

6 extern GUI_CONST_STORAGE GUI_BITMAP bmmessege;

7 extern GUI_CONST_STORAGE GUI_BITMAP bmfolder;

8 extern GUI_CONST_STORAGE GUI_BITMAP bmnotebook;

9 extern GUI_CONST_STORAGE GUI_BITMAP bmsearch;

10 extern GUI_CONST_STORAGE GUI_BITMAP bmhome;

11 extern GUI_CONST_STORAGE GUI_BITMAP bmimage;

12

13 /\* 用于图标的创建 \*/

14 typedef struct {

15 const GUI_BITMAP \*pBitmap;

16 const char \*pText;

17 } BITMAP_ITEM;

18

19 static BITMAP_ITEM \_abitmapItem[] = {

20 {&bmhome, "home"},

21 {&bmfolder, "folder"},

22 {&bmclock, "Clock"},

23 {&bmmessege, "messege"},

24 {&bmcalender, "calender"},

25 {&bmsearch, "search"},

26 {&bmmusic, "music"},

27 {&bmimage, "image"},

28 {&bmsetting, "Setting"},

29 };

30

31 /*\*

32 \* @brief GUI主任务

33 \* @note 无

34 \* @param 无

35 \* @retval 无

36 \*/

37 void MainTask(void)

38 {

39 U8 i;

40 WM_HWIN hWin;

41

42 /\* 加载BMP图片数据到内存设备 \*/

43 LoadBMP_UsingMEMDEV("0:/image/wallpaper.bmp");

44

45 /\* 在指定位置创建ICONVIEW控件 \*/

46 hWin = ICONVIEW_CreateEx(30, /\* 相对于父窗口坐标的最左像素 \*/

47 20, /\* 相对于父窗口坐标的最上像素 \*/

48 770, /\* 水平尺寸 \*/

49 272, /\* 垂直尺寸 \*/

50 WM_HBKWIN, /\* 父窗口句柄 \*/

51 WM_CF_SHOW \| WM_CF_HASTRANS,/\* 窗口创建标志 \*/

52 0, /\* 不设置自动滚动条 \*/

53 ID_ICONVIEW_0, /\* 控件ID \*/

54 64, /\* 单个图标的水平尺寸 \*/

55 64 + 16); /\* 单个图标的垂直尺寸 \*/

56 /\* 向控件中添加新图标 \*/

57 for (i = 0; i < GUI_COUNTOF(_abitmapItem); i++) {

58 ICONVIEW_AddBitmapItem(hWin, \_abitmapItem[i].pBitmap, \_abitmapItem[i].pText);

59 ICONVIEW_SetTextColor(hWin, i, GUI_WHITE);

60 }

61 /\* 设置初始选择的图标，-1表示不选中任何图标 \*/

62 ICONVIEW_SetSel(hWin, -1);

63 /\* 设置图标标题的字体 \*/

64 ICONVIEW_SetFont(hWin, GUI_FONT_16B_1);

65 /\* 设置标题的对齐方式 \*/

66 ICONVIEW_SetTextAlign(hWin, GUI_TA_HCENTER \| GUI_TA_BOTTOM);

67 /\* 设置图标被选中时的背景色 \*/

68 ICONVIEW_SetBkColor(hWin, ICONVIEW_CI_SEL, GUI_MAKE_COLOR((0x40uL << 24) \| 0xFFFFFF));

69 /\* 设置图标的对齐方式 \*/

70 ICONVIEW_SetIconAlign(hWin, ICONVIEW_IA_HCENTER \| ICONVIEW_IA_TOP);

71 /\* 设置图标在X和Y方向上的间距 \*/

72 ICONVIEW_SetSpace(hWin, GUI_COORD_X, 70);

73 ICONVIEW_SetSpace(hWin, GUI_COORD_Y, 50);

74

75 /\* 重定向桌面窗口回调函数 \*/

76 WM_SetCallback(WM_HBKWIN, \_cbBkWindow);

77

78 while (1) {

79 GUI_Delay(50);

80 }

81 }

如代码清单 30‑3所示，首先需要引用所有用到的图标图像数据结构，接着定义一个包含图像数据结构和图像名称的结构体BITMAP_ITEM，然后定义一个BITMAP_ITEM类型的数组_abitmapItem[]，将实验用到的图像和各自的名称一一对应，这样添加或者修改图标图像就会比较方便，具体添加多少个
图标也是由这个数组决定。

在GUI主任务MainTask函数刚开始的时候，需要把背景图片从外部存储器加载到emWin内存设备中，以备之后使用。这么做可以加快较高分辨率和图像质量的图片的显示速度，大家目前并不需要了解其中的具体细节，之后会在图片显示章节详细讲解。

接下来是创建图标控件的重点，使用ICONVIEW_CreateEx函数创建图标控件，函数的前4个参数分别对应了控件的起始坐标和尺寸，第5个参数是控件的父窗口句柄，我们选择桌面窗口WM_HBKWIN作为图标控件的父窗口，还有单个图标的垂直尺寸这个参数比较重要，这个参数是包含图标下方文本信息的尺寸的，需
要根据图标尺寸和实际字体大小来调整。然后用一个for循环，在循环体中调用ICONVIEW_AddBitmapItem函数和ICONVIEW_SetTextColor函数为图标控件添加图标位图和字体。

然后是一些图标和文本的设置，如代码清单 30‑3所示，使用ICONVIEW_SetSel函数设置初始状态不选中任何图标，设置图标文本的字体大小为16B，对齐方式是水平中心对齐和底部对齐，设置图标被选中时的背景颜色为带透明度的白色，这里需要注意一点， ICONVIEW_SetBkColor函数的背景颜
色如果带透明度，不知道是BUG还是什么，在系统颜色模式已经是ARGB模式的情况下，它的透明度通道的参数值定义依然是完全不透明为0x00，完全透明为0xFF，这与ARGB模式下的透明度参数值定义相反，所以为了统一，我们使用GUI_MAKE_COLOR宏把它转一下。

最后，使用WM_SetCallback函数重定向桌面窗口的回调函数，因为我们的背景图片要显示在桌面窗口上。

(2) 桌面窗口回调函数

其实不光是显示背景图片，图标控件的点击图标创建新窗口这个最重要的功能也必须在桌面窗口回调函数中完成，因为和对话框方式类似，图标控件把桌面窗口作为了它的父窗口，那它的很多行为就都需要由桌面窗口来管理，见代码清单 30‑4。

代码清单 30‑4 桌面窗口回调函数（IconviewDLG.c文件）

1 /*\*

2 \* @brief 桌面窗口回调函数

3 \* @note 无

4 \* @param pMsg：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbBkWindow(WM_MESSAGE \*pMsg)

8 {

9 int id;

10 int NCode;

11

12 switch (pMsg->MsgId) {

13 case WM_PAINT:

14 /\* 将背景图片从内存设备写入LCD \*/

15 GUI_MEMDEV_WriteOpaqueAt(hBMP, 0, 0);

16 break;

17 case WM_NOTIFY_PARENT:

18 id = WM_GetId(pMsg->hWinSrc);

19 NCode = pMsg->Data.v;

20 switch (id) {

21 case ID_ICONVIEW_0:

22 switch (NCode) {

23 case WM_NOTIFICATION_CLICKED:

24 break;

25 case WM_NOTIFICATION_RELEASED:

26 switch (ICONVIEW_GetSel(pMsg->hWinSrc)) {

27 case 0:

28 FUN_ICON0Clicked();

29 break;

30 default:

31 break;

32 }

33 break;

34 }

35 break;

36 }

37 break;

38 default:

39 WM_DefaultProc(pMsg);

40 break;

41 }

42 }

1. WM_PAINT消息

背景图片在回调函数的WM_PAINT消息中绘制。因为之前的MainTask函数中已经把背景图片加载到内存设备中了，所以现在我们只需要调用GUI_MEMDEV_WriteOpaqueAt函数即可很快的将背景图片显示出来。同样的，这里目前大家不需要了解其中的具体细节，之后会在图片显示章节详细讲解。

2. WM_NOTIFY_PARENT消息

WM_NOTIFY_PARENT消息和对话框方式的处理方法相同，在此消息中以控件ID来区分各个不同的控件。首先使用ICONVIEW_GetSel函数获取当前被点击图标的索引号，接着根据这个索引号，去调用相应的应用窗口创建函数FUN_ICON0Clicked。所有图标对应的窗口创建函数都在此处调用，由
于篇幅所限，我们只展示了第一个“home”图标的创建函数。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(3) 应用窗口创建函数

代码清单 30‑5 应用窗口创建函数（IconviewDLG.c文件）

1 /*\*

2 \* @brief 应用窗口创建函数

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void FUN_ICON0Clicked(void)

8 {

9 WM_HWIN hWin;

10 WM_HWIN hChild;

11 WM_HWIN hText;

12

13 /\* 创建框架窗口 \*/

14 hWin = FRAMEWIN_CreateEx(100, /\* 相对于父窗口坐标的最左像素 \*/

15 20, /\* 相对于父窗口坐标的最上像素 \*/

16 600, /\* 水平尺寸 \*/

17 400, /\* 垂直尺寸 \*/

18 WM_HBKWIN, /\* 父窗口句柄 \*/

19 WM_CF_SHOW, /\* 窗口创建标志 \*/

20 0, /\* 额外创建标志 \*/

21 ID_FRAMEWIN_0,/\* 控件ID \*/

22 "", /\* 标题栏文本 \*/

23 &_cbICON0); /\* 客户窗口回调函数 \*/

24 FRAMEWIN_SetFont(hWin, GUI_FONT_24B_1);

25 FRAMEWIN_SetText(hWin, "HOME");

26 FRAMEWIN_AddCloseButton(hWin, FRAMEWIN_BUTTON_RIGHT, 0);

27 /\* 设置为模态窗口 \*/

28 WM_MakeModal(hWin);

29 /\* 获取客户窗口句柄 \*/

30 hChild = WM_GetClientWindow(hWin);

31 /\* 在客户窗口中创建子控件 \*/

32 hText = TEXT_CreateEx(0, 0, 180, 34, hChild, WM_CF_SHOW, 0, ID_TEXT_0, "HOME APP");

33 TEXT_SetFont(hText, GUI_FONT_32B_1);

34 TEXT_SetTextColor(hText, GUI_WHITE);

35 }

如代码清单 30‑5所示，新应用窗口的创建依然使用FRAMEWIN_CreateEx函数进行直接创建，有关此函数的介绍见17.1.2
。然后是设置标题栏字体为24B、文本为“HOME”，并且添加一个关闭按钮。最后，通过WM_MakeModal函数把这个窗口设置为模态窗口，这样的话只有窗口内部的范围对触摸动作有反应，窗口外部的其他控件不会响应触摸动作，可在一定程度上避免误触。

在第17章 中我们提到过，框架窗口有主窗口和客户窗口两部分，并且句柄是分开的，子控件放在客户窗口上。所以使用WM_GetClientWindow函数先获取客户窗口的句柄，然后再在这基础上添加子控件。

应用窗口的回调函数如代码清单 30‑6所示。

代码清单 30‑6 应用窗口回调函数（IconviewDLG.c文件）

1 /*\*

2 \* @brief 应用窗口回调函数

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void \_cbICON0(WM_MESSAGE \*pMsg)

8 {

9 switch (pMsg->MsgId) {

10 case WM_PAINT:

11 /\* 设置客户窗口的背景颜色 \*/

12 GUI_SetBkColor(GUI_LIGHTGRAY);

13 GUI_Clear();

14 break;

15 default:

16 WM_DefaultProc(pMsg);

17 break;

18 }

19 }

应用窗口内子控件的行为逻辑就由回调函数_cbICON0进行处理，如代码清单 30‑6所示，我们在回调函数的WM_PAINT消息中使用GUI_SetBkColor函数设置客户窗口的背景颜色。子控件的WM_NOTIFY_PARENT消息也是在这个回调函数中处理。

实验现象
^^^^

图标控件基础实验现象如图 30‑5和图 30‑6所示，图 30‑5是图标控件的初始状态，效果与手机和平板电脑上的很类似，图 30‑6是图标被点击后的现象。

|Iconvi006|

图 30‑5 图标控件实验初始状态

|Iconvi007|

图 30‑6 点击home图标后的现象

.. |Iconvi002| image:: media\Iconvi002.png
   :width: 1.41774in
   :height: 2.26415in
.. |Iconvi003| image:: media\Iconvi003.png
   :width: 4.64525in
   :height: 3.39541in
.. |Iconvi004| image:: media\Iconvi004.png
   :width: 3.89623in
   :height: 3.00811in
.. |Iconvi005| image:: media\Iconvi005.png
   :width: 3.14544in
   :height: 2.3747in
.. |Iconvi006| image:: media\Iconvi006.png
   :width: 5.76806in
   :height: 3.46228in
.. |Iconvi007| image:: media\Iconvi007.png
   :width: 5.76806in
   :height: 3.46228in
