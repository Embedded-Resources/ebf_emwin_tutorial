.. vim: syntax=rst

编辑框控件
===========

编辑框（EDIT）控件是emWin提供的一种用来获取外部输入信息的控件。编辑框控件在我们平时接触到的交互界面中可以说是非常常见，例如网页或者APP中的登录界面里输入账号和密码的地方就是类似的控件。

编辑框控件的外观如图 22‑1所示。

|EDIT002|

图 22‑1 编辑框控件外观

编辑框控件支持4种通知代码，以区分不同的控件行为，见表格 22‑1。

表格 22‑1 编辑框支持的通知代码

============================= ======================================
通知代码                      描述
============================= ======================================
WM_NOTIFICATION_CLICKED       编辑框已被点击
WM_NOTIFICATION_RELEASED      编辑框已被释放
WM_NOTIFICATION_MOVED_OUT     编辑框已被单击，指针已移出控件且未释放
WM_NOTIFICATION_VALUE_CHANGED 编辑框的值或内容已更改
============================= ======================================

这4种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击编辑框后，窗口管理器向父窗口发送WM_NOTIFY_PARENT消息时，会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

编辑框控件支持输入焦点，支持键盘或其他类似键盘的外部输入设备对编辑框的控制。如果编辑框控件已被聚焦，那它可以接收表格 22‑2中的按键消息。

表格 22‑2 编辑框支持的按键消息

================= =====================================================
按键消息          描述
================= =====================================================
GUI_KEY_UP        递增当前字符，如果当前字符为’A’，则变为’B’
GUI_KEY_DOWN      递减当前字符，与上一条消息相反
GUI_KEY_RIGHT     将光标向右移动一个字符
GUI_KEY_LEFT      将光标向左移动一个字符
GUI_KEY_BACKSPACE 如果编辑框工作在文本模式，则删除光标之前的字符
GUI_KEY_DELETE    如果编辑框工作在文本模式，则删除光标之后的字符
GUI_KEY_INSERT    如果编辑框工作在文本模式，则在GUI_EDIT_MODE_OVERWRITE

                  和GUI_EDIT_MODE_INSERT之间切换编辑模式 ================= =====================================================

创建编辑框控件
~~~~~~~

编辑框控件API
^^^^^^^^

表格 22‑3 编辑框控件API

========================== ====================================
函数名                     描述
========================== ====================================
EDIT_AddKey()              添加按键输入
EDIT_CreateEx()            创建编辑框
EDIT_CreateIndirect()      从对话框资源表中创建编辑框控件
EDIT_CreateUser()          使用额外的字节作为用户数据创建编辑框
EDIT_EnableBlink()         启用/禁用光标闪烁
EDIT_EnableInversion()     启用/禁用反向光标（默认为反向）
EDIT_GetBkColor()          返回编辑框背景颜色
EDIT_GetCursorCharPos()    返回光标位置的字符数
EDIT_GetCursorPixelPos()   返回光标的像素位置
EDIT_GetDefaultBkColor()   返回默认背景颜色
EDIT_GetDefaultFont()      返回默认字体
EDIT_GetDefaultTextAlign() 返回默认文本对齐方式
EDIT_GetDefaultTextColor() 返回默认文本颜色
EDIT_GetFloatValue()       以浮点值的形式返回当前值
EDIT_GetFont()             返回当前字体
EDIT_GetNumChars()         返回给定编辑框的字符数
EDIT_GetText()             返回用户输入的字符
EDIT_GetTextAlign()        返回当前文本对齐方式
EDIT_GetTextColor()        放回当前文本颜色
EDIT_GetUserData()         检索用户数据集
EDIT_GetValue()            返回当前的数值
EDIT_SetBinMode()          设置编辑框为二进制编辑模式
EDIT_SetBkColor()          设置当前背景颜色
EDIT_SetCursorAtChar()     将编辑框的光标设置到指定字符位置
EDIT_SetCursorAtPixel()    将编辑框的光标设置到指定像素位置
EDIT_SetDecMode()          设置编辑框为十进制编辑模式
EDIT_SetDefaultBkColor()   设置默认背景颜色
EDIT_SetDefaultFont()      设置默认字体
EDIT_SetDefaultTextAlign() 设置默认文本对齐方式
EDIT_SetDefaultTextColor() 设置默认文本颜色
EDIT_SetFloatMode()        设置编辑框为浮点数编辑模式
EDIT_SetFloatValue()       如果使用浮点编辑模式，则设置浮点值
EDIT_SetFocusable()        设置编辑框的可聚焦性
EDIT_SetFont()             设置当前字体
EDIT_SetHexMode()          设置编辑框为十六进制数编辑模式
EDIT_SetInsertMode()       启用或禁用插入模式
EDIT_SetMaxLen()           设置编辑框的最大字符数
EDIT_SetpfAddKeyEx()       向编辑框中添加字符
EDIT_SetSel()              设置当前选项
EDIT_SetText()             设置当前文本
EDIT_SetTextAlign()        设置文本对齐方式
EDIT_SetTextColor()        设置当前文本颜色
EDIT_SetTextMode()         设置编辑框为文本编辑模式
EDIT_SetValue()            设置当前数值
EDIT_SetUlongMode()        设置编辑框为无符号长十进制编辑模式
EDIT_SetUserData()         设置额外用户数据集
GUI_EditBin()              在当前光标位置编辑二进制值
GUI_EditDec()              在当前光标位置编辑十进制值
GUI_EditFloat()            在当前光标位置编辑浮点值
GUI_EditHex()              在当前光标位置编辑十六进制值
GUI_EditString()           在当前光标位置编辑字符串
========================== ====================================

编辑框控件创建函数
^^^^^^^^^

EDIT_CreateEx()
'''''''''''''''

在指定位置创建具有指定大小的编辑框控件。

代码清单 22‑1 编辑框创建函数

1 EDIT_Handle EDIT_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int ExFlags,

3 int Id, int MaxLen);

1) x0：编辑框控件在父坐标中的最左侧像素；

2) y0：编辑框控件在父坐标中的最顶侧像素；

3) xSize：编辑框的水平尺寸，以像素为单位；

4) ySize：编辑框的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以编辑框控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：预留，未使用；

8) Id：控件ID；

9) MaxLen：最大字符数。

返回值：创建成功后返回已创建的编辑框控件句柄，创建失败则返回0。

EDIT_CreateIndirect()
'''''''''''''''''''''

从对话框资源表中创建编辑框控件。

代码清单 22‑2 对话框方式创建编辑框控件

1 EDIT_Handle EDIT_CreateIndirect(const GUI_WIDGET_CREATE_INFO \*

2 pCreateInfo, WM_HWIN hWinParent, int

3 x0, int y0, WM_CALLBACK \* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：编辑框控件在父坐标中的最左边像素；

4) y0：编辑框控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，结构中原本可选的Flags参数和para参数也全都用上。其中Flags与EDIT_SetTextAlign()函数的Align参数相同，para与EDIT_CreateEx()函数中的MaxLen参数相同。

编辑框控件基础实验
~~~~~~~~~

代码分析
^^^^

(1) 创建对话框

在本实验中，我们通过对话框的方式来创建文本控件，这样可以方便管理各种界面元素，见代码清单 22‑3。

代码清单 22‑3 创建对话框（EditDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_EDIT_0 (GUI_ID_USER + 0x01)

4 #define ID_EDIT_1 (GUI_ID_USER + 0x02)

5 #define ID_EDIT_2 (GUI_ID_USER + 0x03)

6 #define ID_EDIT_3 (GUI_ID_USER + 0x04)

7

8

9 /\* 资源表 \*/

10 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

11 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

12 480, 0, 0x0, 0 },

13 { EDIT_CreateIndirect, "Edit0", ID_EDIT_0, 120, 80, 360, 50, 0,

14 0x64, 0 },

15 { EDIT_CreateIndirect, "Edit1", ID_EDIT_1, 120, 150, 165, 50, 0,

16 0x64, 0 },

17 { EDIT_CreateIndirect, "Edit2", ID_EDIT_2, 315, 150, 165, 50, 0,

18 0x64, 0 },

19 { EDIT_CreateIndirect, "Edit3", ID_EDIT_3, 120, 220, 360, 50, 0,

20 0x64, 0 },

21 { TEXT_CreateIndirect, "", ID_TEXT_0, 120, 290, 370, 50, 0, 0x64,

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

在代码清单 22‑3中我们定义了5个ID：一个框架窗口ID、4个编辑框控件ID和一个文本控件。当然也可以使用emWin预定义好的编辑框控件ID，但最多只有GUI_ID_EDIT0到GUI_ID_EDIT9，共10个ID可供使用。

我们重点关注编辑框控件资源表中的倒数第二个参数MaxLen，这个参数用来设置最大字符数。在代码清单 22‑3中MaxLen的参数值是0x64，表示当前编辑框控件最大可以存放100个字符。其实这是GUIBuilder生成的默认值，可以在回调函数中修改为合适的值。

(2) 对话框回调函数

代码清单 22‑4 对话框回调函数（EditDLG.c文件）

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE\* pMsg)

8 {

9 WM_HWIN hItem;

10 int NCode;

11 int Id;

12 char EditBuff[30] = { 0 };

13

14 switch (pMsg->MsgId) {

15 case WM_INIT_DIALOG:

16 /\* Edit0初始化 \*/

17 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_0);

18 EDIT_SetText(hItem, "STemWIN@EmbedFire STM32F429");

19 EDIT_SetFont(hItem, GUI_FONT_24_ASCII);

20 EDIT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

21 EDIT_EnableBlink(hItem, 500, 1);

22 /\* Edit1初始化 \*/

23 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_1);

24 EDIT_SetFloatMode(hItem, 3.1415926, 0.0, 10.0, 7,

25 GUI_EDIT_NORMAL);

26 EDIT_SetFont(hItem, GUI_FONT_24_ASCII);

27 EDIT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

28 EDIT_EnableBlink(hItem, 500, 1);

29 /\* Edit2初始化 \*/

30 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_2);

31 EDIT_SetMaxLen(hItem, 8);

32 EDIT_SetHexMode(hItem, 232425, 0, 4294967295);

33 EDIT_SetFont(hItem, GUI_FONT_24_ASCII);

34 EDIT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

35 EDIT_EnableBlink(hItem, 500, 1);

36 /\* Edit3初始化 \*/

37 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_3);

38 EDIT_SetMaxLen(hItem, 28);

39 EDIT_SetBinMode(hItem, 123456789, 0, 268435455);

40 EDIT_SetFont(hItem, GUI_FONT_24_ASCII);

41 EDIT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

42 EDIT_EnableBlink(hItem, 500, 1);

43 /\* 初始化Text0 \*/

44 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

45 TEXT_SetFont(hItem, GUI_FONT_COMIC24B_ASCII);

46 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

47 break;

48 case WM_NOTIFY_PARENT:

49 Id = WM_GetId(pMsg->hWinSrc);

50 NCode = pMsg->Data.v;

51 switch (Id) {

52 case ID_EDIT_0: // Notifications sent by 'Edit0'

53 switch (NCode) {

54 case WM_NOTIFICATION_CLICKED:

55 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_0);

56 EDIT_GetText(hItem, EditBuff, 40);

57 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

58 TEXT_SetText(hItem, EditBuff);

59 break;

60 case WM_NOTIFICATION_RELEASED:

61 break;

62 case WM_NOTIFICATION_VALUE_CHANGED:

63 break;

64 }

65 break;

66 case ID_EDIT_1: // Notifications sent by 'Edit1'

67 switch (NCode) {

68 case WM_NOTIFICATION_CLICKED:

69 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_1);

70 EDIT_GetText(hItem, EditBuff, 40);

71 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

72 TEXT_SetText(hItem, EditBuff);

73 break;

74 case WM_NOTIFICATION_RELEASED:

75 break;

76 case WM_NOTIFICATION_VALUE_CHANGED:

77 break;

78 }

79 break;

80 case ID_EDIT_2: // Notifications sent by 'Edit2'

81 switch (NCode) {

82 case WM_NOTIFICATION_CLICKED:

83 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_2);

84 EDIT_GetText(hItem, EditBuff, 40);

85 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

86 TEXT_SetText(hItem, EditBuff);

87 break;

88 case WM_NOTIFICATION_RELEASED:

89 break;

90 case WM_NOTIFICATION_VALUE_CHANGED:

91 break;

92 }

93 break;

94 case ID_EDIT_3: // Notifications sent by 'Edit3'

95 switch (NCode) {

96 case WM_NOTIFICATION_CLICKED:

97 hItem = WM_GetDialogItem(pMsg->hWin, ID_EDIT_3);

98 EDIT_GetText(hItem, EditBuff, 40);

99 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

100 TEXT_SetText(hItem, EditBuff);

101 break;

102 case WM_NOTIFICATION_RELEASED:

103 break;

104 case WM_NOTIFICATION_VALUE_CHANGED:

105 break;

106 }

107 break;

108 }

109 default:

110 WM_DefaultProc(pMsg);

111 break;

112 }

113 }

1. WM_INIT_DIALOG消息

在代码清单 22‑4中我们不再设置框架窗口，直接配置编辑框控件。编辑框控件默认是文本编辑模式，所以我们用ID_EDIT_0来显示字符串文本。EDIT_SetText函数设置需要显示的字符串文本，EDIT_SetFont函数设置字体大小为24，EDIT_SetTextAlign函数设置文本对齐方式为左
侧对齐和垂直居中对齐，EDIT_EnableBlink函数使能光标并且设置闪烁间隔时间为500ms。

设置ID_EDIT_1为浮点数编辑模式，初值为3.1415926，可编辑和显示的最小值为0.0，最大值为10.0，最多可显示小数点后7位，显示模式为正常，即只有当数值为负值时才显示负号，否则显示前导零。设置数值文本的字体为24，对齐方式为左对齐和垂直居中对齐，使能光标并设置闪烁间隔时间为500ms。

设置ID_EDIT_2为十六进制数编辑模式，初值为十进制数232425或十六进制数0x38BE9，最小值为0，最大值为十进制数4294967295或十六进制数0xFFFFFFFF，最大可显示8位十六进制数，然后同样的，设置数值文本的字体为24，对齐方式为左对齐和垂直居中对齐，使能光标并设置闪烁间隔时
间为500ms。

设置ID_EDIT_3为二进制数编辑模式，初值为十进制数123456789，最小值为0，最大值为十进制数268435455，最大可显示28位二进制数，最后设置数值文本的字体为24，对齐方式为左对齐和垂直居中对齐，使能光标并设置闪烁间隔时间为500ms。

从代码清单 22‑4可以看到，编辑框控件的十六进制和二进制编辑模式的相关API是支持输入十进制数的，但是用户从界面上输入的只能是特定进制的数值。

2. WM_NOTIFY_PARENT消息

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。

在代码清单 22‑4中，当对话框收到WM_NOTIFICATION_CLICKED通知代码之后，使用EDIT_GetText函数读取被点击的编辑框中存放的文本，然后通过TEXT_SetText函数显示出来。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

编辑框控件基础实验的实验现象如图 22‑2和图 22‑3，可以看到有三排编辑框，第一排是字符文本模式，第二排左侧是浮点数文本模式，第二排右侧是十六进制文本模式，第三排是二进制文本模式，当点击其中一个编辑框时，下方的文本控件会显示出相应的内容。

|EDIT003|

图 22‑2 编辑框控件基础实验初始现象

|EDIT004|

图 22‑3 编辑框被点击后的现象

.. |EDIT002| image:: media\EDIT002.png
   :width: 1.04154in
   :height: 0.20831in
.. |EDIT003| image:: media\EDIT003.png
   :width: 5.76806in
   :height: 3.46228in
.. |EDIT004| image:: media\EDIT004.png
   :width: 5.76806in
   :height: 3.46228in
