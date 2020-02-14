.. vim: syntax=rst

滑块控件
----

滑块（Slider）控件通常用于在一定范围内修改数值，类似手机上用来调整屏幕亮度或音量的控件。滑块控件由一个滑动条和条旁边的刻度标记组成，这些刻度标记可用于在拖动滑块时吸附滑块。

滑块控件的外观如图 27‑1所示。

|Slider002|

图 27‑1 滑块控件默认外观

滑块控件支持3种通知代码，以区分各种不同的操作动作，见表格 27‑1。

表格 27‑1 滑块控件支持的通知代码

============================= ====================
通知代码                      描述
============================= ====================
WM_NOTIFICATION_CLICKED       滑块控件已被点击
WM_NOTIFICATION_RELEASED      滑块控件已被释放
WM_NOTIFICATION_VALUE_CHANGED 滑块控件的值已被更改
============================= ====================

这3种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器会让滑块控件向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

同时滑块控件也支持输入焦点，它可以接收如表格 27‑2所示的按键消息。

表格 27‑2 滑块控件支持的按键消息

============= ============================
按键消息      描述
============= ============================
GUI_KEY_RIGHT 将当前滑块控件的值加一个刻度
GUI_KEY_LEFT  将当前滑块控件的值减一个刻度
============= ============================

创建滑块控件
~~~~~~

滑块控件API
^^^^^^^

表格 27‑3 滑块控件API

============================= ==================================
函数名                        描述
============================= ==================================
SLIDER_CreateEx()             创建一个滑块控件
SLIDER_CreateIndirect()       从对话框资源表中创建滑块控件
SLIDER_CreateUser()           使用额外的字节作为用户数据创建滑块
SLIDER_Dec()                  减少滑块的值
SLIDER_GetBkColor()           返回当前背景颜色
SLIDER_GetFocusColor()        返回当前聚焦颜色
SLIDER_GetRange()             返回指定滑块的范围
SLIDER_GetUserData()          检索用户数据集
SLIDER_GetValue()             返回滑块当前的值
SLIDER_Inc()                  增加滑块的值
SLIDER_SetBkColor()           设置当前背景颜色
SLIDER_SetDefaultFocusColor() 设置默认聚焦颜色
SLIDER_SetFocusColor()        设置聚焦颜色
SLIDER_SetNumTicks()          设置滑块的刻度线数量
SLIDER_SetRange()             设置滑块的范围
SLIDER_SetUserData()          设置额外用户数据集
SLIDER_SetValue()             设置滑块当前的值
SLIDER_SetWidth()             设置滑块的宽度
============================= ==================================

滑块控件创建函数
^^^^^^^^

SLIDER_CreateEx()
'''''''''''''''''

在指定位置创建指定大小的滑块控件。

代码清单 27‑1 函数原型

1 SLIDER_Handle SLIDER_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id);

1) x0：滑块控件在父坐标中的最左侧像素；

2) y0：滑块控件在父坐标中的最顶侧像素；

3) xSize：滑块的水平尺寸，以像素为单位；

4) ySize：滑块的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以滑块控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：特殊创建标志。用于调整滑块方向，默认水平方向，可通过可选值SLIDER_CF_VERTICAL设置为垂直方向；

8) Id：控件ID号。

返回值：创建成功后返回已创建的滑块控件句柄，创建失败则返回0。

SLIDER_CreateIndirect()
'''''''''''''''''''''''

从对话框资源表中创建滑块控件。

代码清单 27‑2 函数原型

1 SLIDER_Handle SLIDER_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN hWinParent,

3 int x0, int y0, WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：滑块控件在父坐标中的最左边像素；

4) y0：滑块控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数，其中的Flags参数的可选值与SLIDER_CreateEx()函数的ExFlags参数相同。

滑块控件基础实验
~~~~~~~~

代码分析
^^^^

(1) 创建对话框

代码清单 27‑3 创建对话框（SliderDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_SLIDER_0 (GUI_ID_USER + 0x01)

4 #define ID_SLIDER_1 (GUI_ID_USER + 0x02)

5 #define ID_SLIDER_2 (GUI_ID_USER + 0x03)

6 #define ID_EDIT_0 (GUI_ID_USER + 0x04)

7 #define ID_EDIT_1 (GUI_ID_USER + 0x05)

8

9 /\* 资源表 \*/

10 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

11 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

12 480, 0, 0x0, 0 },

13 { SLIDER_CreateIndirect, "Slider", ID_SLIDER_0, 100, 40, 600, 40,

14 0, 0x0, 0 },

15 { SLIDER_CreateIndirect, "Slider", ID_SLIDER_1, 100, 105, 40, 300,

16 8, 0x0, 0 },

17 { SLIDER_CreateIndirect, "Slider", ID_SLIDER_2, 220, 125, 480, 40,

18 0, 0x0, 0 },

19 { EDIT_CreateIndirect, "Edit", ID_EDIT_0, 40, 40, 50, 40, 0, 0x4,

20 0 },

21 { EDIT_CreateIndirect, "Edit", ID_EDIT_1, 40, 105, 50, 40, 0, 0x3,

22 0 },

23 };

24

25 /*\*

26 \* @brief 以对话框方式间接创建控件

27 \* @note 无

28 \* @param 无

29 \* @retval hWin：资源表中第一个控件的句柄

30 \*/

31 WM_HWIN CreateFramewin(void)

32 {

33 WM_HWIN hWin;

34

35 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

36 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

37 return hWin;

38 }

在代码清单 27‑3中我们定义了6个ID：一个框架窗口ID、3个滑块控件ID和2个编辑框控件。当然也可以使用emWin预定义好的滑块控件ID，但最多只有GUI_ID_SLIDER0到GUI_ID_SLIDER9，共10个ID可供使用。

我们重点关注滑块控件资源表的倒数第三个参数Flags，这个参数决定了滑块是水平方向还是垂直方向，默认水平，可使用SLIDER_CF_VERTICAL或十进制数8设置为垂直方向。

(2) 对话框回调函数

代码清单 27‑4 对话框回调函数（SliderDLG.c文件）

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE\* pMsg)

8 {

9 WM_HWIN hItem;

10 WM_HWIN hSlider;

11 WM_HWIN hEdit;

12 int NCode;

13 int Id;

14 int value;

15

16 switch (pMsg->MsgId) {

17 case WM_INIT_DIALOG:

18 /\* 初始化Framewin控件 \*/

19 hItem = pMsg->hWin;

20 FRAMEWIN_SetTitleHeight(hItem, 32);

21 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

22 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

23 /\* 初始化Slider0 \*/

24 hItem = WM_GetDialogItem(pMsg->hWin, ID_SLIDER_0);

25 SLIDER_SetRange(hItem, 0, 1000);

26 /\* 初始化Slider1 \*/

27 hItem = WM_GetDialogItem(pMsg->hWin, ID_SLIDER_1);

28 SLIDER_SetRange(hItem, 0, 100);

29 SLIDER_SetWidth(hItem, 20);

30 /\* 初始化Slider2 \*/

31 hItem = WM_GetDialogItem(pMsg->hWin, ID_SLIDER_2);

32 SLIDER_SetSkinClassic(hItem);

33 SLIDER_SetWidth(hItem, 30);

34 /\* 初始化Edit0 \*/

35 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_0);

36 EDIT_SetText(hItem, "0000");

37 EDIT_SetTextAlign(hItem, GUI_TA_HCENTER \| GUI_TA_VCENTER);

38 EDIT_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

39 EDIT_SetDecMode(hItem, 0, 0, 1000, 0, GUI_EDIT_NORMAL);

40 /\* 初始化Edit1 \*/

41 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_1);

42 EDIT_SetText(hItem, "000");

43 EDIT_SetTextAlign(hItem, GUI_TA_HCENTER \| GUI_TA_VCENTER);

44 EDIT_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

45 EDIT_SetDecMode(hItem, 0, 0, 100, 0, GUI_EDIT_NORMAL);

46 break;

47 case WM_NOTIFY_PARENT:

48 Id = WM_GetId(pMsg->hWinSrc);

49 NCode = pMsg->Data.v;

50 switch (Id) {

51 case ID_SLIDER_0: // Notifications sent by 'Slider0'

52 switch (NCode) {

53 case WM_NOTIFICATION_CLICKED:

54 break;

55 case WM_NOTIFICATION_RELEASED:

56 break;

57 case WM_NOTIFICATION_VALUE_CHANGED:

58 /\*

59 滑块的值被改变，将改变后的值更新到EDIT

60 控件中 \*/

61 hSlider = WM_GetDialogItem(pMsg->hWin, ID_SLIDER_0);

62 hEdit = WM_GetDialogItem(pMsg->hWin, ID_EDIT_0);

63 value = SLIDER_GetValue(hSlider);

64 EDIT_SetValue(hEdit, value);

65 break;

66 }

67 break;

68 case ID_SLIDER_1: // Notifications sent by 'Slider1'

69 switch (NCode) {

70 case WM_NOTIFICATION_CLICKED:

71 break;

72 case WM_NOTIFICATION_RELEASED:

73 break;

74 case WM_NOTIFICATION_VALUE_CHANGED:

75 /\*

76 滑块的值被改变，将改变后的值更新到EDIT

77 控件中 \*/

78 hSlider = WM_GetDialogItem(pMsg->hWin, ID_SLIDER_1);

79 hEdit = WM_GetDialogItem(pMsg->hWin, ID_EDIT_1);

80 value = SLIDER_GetValue(hSlider);

81 EDIT_SetValue(hEdit, value);

82 break;

83 }

84 break;

85 case ID_EDIT_0: // Notifications sent by 'EDIT0'

86 switch (NCode) {

87 case WM_NOTIFICATION_CLICKED:

88 break;

89 case WM_NOTIFICATION_RELEASED:

90 break;

91 case WM_NOTIFICATION_VALUE_CHANGED:

92 break;

93 }

94 break;

95 case ID_EDIT_1: // Notifications sent by 'EDIT1'

96 switch (NCode) {

97 case WM_NOTIFICATION_CLICKED:

98 break;

99 case WM_NOTIFICATION_RELEASED:

100 break;

101 case WM_NOTIFICATION_VALUE_CHANGED:

102 break;

103 }

104 break;

105 }

106 break;

107 default:

108 WM_DefaultProc(pMsg);

109 break;

110 }

111 }

1. WM_INIT_DIALOG消息

在代码清单 27‑4中，我们设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

滑块控件相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取滑块控件的句柄。成功获取到句柄后可以开始进一步设置滑块控件。我们重点关注滑块控件的设置，编辑框控件只是辅助显示滑块数值，它的初始化设置在第22
章 已经讲过就不再赘述了。

首先使用SLIDER_SetRange设置ID_SLIDER_0的范围0~1000，ID_SLIDER_1的范围0~100，SLIDER_SetWidth函数设置ID_SLIDER_1的宽度为20像素，ID_SLIDER_2的宽度为30像素，SLIDER_SetSkinClassic函数将ID_SL
IDER_2设置为经典皮肤。

滑块控件功能比较单一，所以需要设置的地方也比较少。

2. WM_NOTIFY_PARENT消息

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。

滑块控件的值需要随着滑块的滑动一直更新，所以我们在通知代码WM_NOTIFICATION_VALUE_CHANGED中，使用SLIDER_GetValue函数获取当前滑块的值，然后使用EDIT_SetValue函数把得到的值送到编辑框中。这样就实现了滑动滑块控件的同时更新编辑框中数值的功能。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

滑块控件基础实验的实验现象如图 27‑2和图 27‑3所示，可以看到滑动滑块后编辑框内的数值会跟着相应改变，ID_SLIDER_2的外观和前两个滑块不同。

|Slider003|

图 27‑2 滑块控件基础实验初始状态

|Slider004|

图 27‑3 滑块控件基础实验滑动后的现象

.. |Slider002| image:: media\Slider002.png
   :width: 1.87477in
   :height: 0.41661in
.. |Slider003| image:: media\Slider003.png
   :width: 5.76806in
   :height: 3.46228in
.. |Slider004| image:: media\Slider004.png
   :width: 5.76806in
   :height: 3.46228in
