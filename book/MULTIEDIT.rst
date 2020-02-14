.. vim: syntax=rst

多行文本控件
------

多行文本（MULTIEDIT）控件，顾名思义，它是一种能够编辑和显示若干行文本的控件。前面讲的文本控件和编辑框控件都只能够显示或编辑一行的文本信息，想要显示好几行的话就要使用多个控件，这样就不太方便了。多行文本控件的特点很适合用来做一些简单的阅读器或是用来显示多种信息。实际上它就是一个简单的文本编辑
器，同时也可以显示静态文本。

多行文本控件支持使用滚动条或不使用滚动条进行内容滚动。控件在编辑模式并打开自动水平滚动条时的外观如图 23‑1所示，它在不同模式下的外观是不同的。

|MULTIE002|

图 23‑1 多行文本控件外观

多行文本控件支持5种通知代码，以区分各种不同的操作动作，见表格 23‑1。

表格 23‑1 多行文本控件支持的通知代码

============================== ========================================
通知代码                       描述
============================== ========================================
WM_NOTIFICATION_CLICKED        控件已被点击
WM_NOTIFICATION_RELEASED       控件已被释放
WM_NOTIFICATION_MOVED_OUT      控件已被点击，但指针已被移出框且没有释放
WM_NOTIFICATION_SCROLL_CHANGED 滚动条的滚动位置已更改
WM_NOTIFICATION_VALUE_CHANGED  控件内的文本已被更改
============================== ========================================

这5种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器会让多行文本向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

同时多行文本控件也支持输入焦点，它可以接收如表格 23‑2表格 24‑2所示的按键消息。

表格 23‑2 多行文本控件支持的按键消息

================= =======================================
按键消息          描述
================= =======================================
GUI_KEY_UP        将光标向上移动一行
GUI_KEY_DOWN      将光标向下移动一行
GUI_KEY_RIGHT     将光标向右移动一个字符
GUI_KEY_LEFT      将光标向左移动一个字符
GUI_KEY_END       将光标移动到当前行的末尾
GUI_KEY_HOME      将光标移动到当前行的开头
GUI_KEY_BACKSPACE 在读/写模式下，删除光标之前的字符
GUI_KEY_DELETE    在读/写模式下，删除光标下面的字符
GUI_KEY_INSERT    在插入和覆盖模式之间切换
GUI_KEY_ENTER     在读/写模式下，在当前位置插入新行('\n')

                  在只读模式下，光标移动到下一行的开头 GUI_KEY_PGUP      向上滚动页面 GUI_KEY_PGDOWN    向下滚动页面 ================= =======================================

创建多行文本控件
~~~~~~~~

多行文本控件API
^^^^^^^^^

表格 23‑3 多行文本控件API

============================= ======================================
函数名                        描述
============================= ======================================
MULTIEDIT_AddKey()            按键输入函数
MULTIEDIT_AddText()           在当前光标位置添加文本
MULTIEDIT_CreateEx()          创建一个多行文本
MULTIEDIT_CreateIndirect()    从对话框资源表中创建多行文本控件
MULTIEDIT_CreateUser()        使用额外的字节作为用户数据创建多行文本
MULTIEDIT_EnableBlink()       启用/禁用光标闪烁
MULTIEDIT_GetBkColor()        放回当前背景颜色
MULTIEDIT_GetCursorCharPos()  返回光标所在的字符位置
MULTIEDIT_GetCursorPixelPos() 返回光标所在的像素位置
MULTIEDIT_GetFont()           返回当前字体
MULTIEDIT_GetPrompt()         返回提示符文本
MULTIEDIT_GetText()           返回当前文本
MULTIEDIT_GetTextColor()      返回当前文本颜色
MULTIEDIT_GetTextSize()       返回当前文本使用的缓冲区大小
MULTIEDIT_GetUserData()       检索用户数据集
MULTIEDIT_SetAutoScrollH()    激活自动使用水平滚动条
MULTIEDIT_SetAutoScrollV()    激活自动使用垂直滚动条
MULTIEDIT_SetBkColor()        设置当前背景颜色
MULTIEDIT_SetBufferSize()     设置用于文本和提示符的缓冲区大小
MULTIEDIT_SetCursorCharPos()  将光标设置到给定的字符位置
MULTIEDIT_SetCursorOffset()   将光标设置为给定字符
MULTIEDIT_SetCursorPixelPos() 将光标设置为给定的像素位置
MULTIEDIT_SetFocusable()      设置指定控件的可聚焦性
MULTIEDIT_SetFont()           设置当前字体
MULTIEDIT_SetInsertMode()     启用/禁用插入模式
MULTIEDIT_SetMaxNumChars()    设置包括提示符在内的最大字符数
MULTIEDIT_SetPasswordMode()   启用/禁用密码模式
MULTIEDIT_SetPrompt()         设置提示符文本
MULTIEDIT_SetReadOnly()       启用/禁用只读模式
MULTIEDIT_SetText()           设置当前文本
MULTIEDIT_SetTextAlign()      设置文本对齐方式
MULTIEDIT_SetTextColor()      设置文本颜色
MULTIEDIT_SetUserData()       设置额外用户数据集
MULTIEDIT_SetWrapWord()       启用/禁用自动换行
MULTIEDIT_SetWrapNone()       启用/禁用非换行模式
MULTIEDIT_ShowCursor()        启用/禁用光标
============================= ======================================

多行文本控件创建函数
^^^^^^^^^^

MULTIEDIT_CreateEx()
''''''''''''''''''''

在指定位置创建具有指定大小的多行文本控件。

代码清单 23‑1 函数原型

1 MULTIEDIT_HANDLE MULTIEDIT_CreateEx(int x0, int y0, int xSize, int

2 ySize, WM_HWIN hParent, int

3 WinFlags, int ExFlags, int Id, int

4 BufferSize, const char \*pText);

1)  x0：多行文本控件在父坐标中的最左侧像素；

2)  y0：多行文本控件在父坐标中的最顶侧像素；

3)  xSize：多行文本的水平尺寸，以像素为单位；

4)  ySize：多行文本的垂直尺寸，以像素为单位；

5)  hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6)  WinFlags：窗口创建标志。由于控件本质上是窗口，所以多行文本控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7)  ExFlags：多行文本模式选择，参数可选值见表格 23‑4；

8)  Id：控件ID号；

9)  BufferSize：初始的文本缓冲区大小；

10) pText：需要显示的文本字符串。

返回值：创建成功后返回已创建的多行文本控件句柄，创建失败则返回0。

表格 23‑4 ExFlags参数可选值

============================ ==================
可选值                       描述
============================ ==================
MULTIEDIT_CF_AUTOSCROLLBAR_H 自动使用水平滚动条
MULTIEDIT_CF_AUTOSCROLLBAR_V 自动使用垂直滚动条
MULTIEDIT_CF_INSERT          启用插入模式
MULTIEDIT_CF_READONLY        启用只读模式
============================ ==================

MULTIEDIT_CreateIndirect()
''''''''''''''''''''''''''

从对话框资源表中创建多行文本控件。

代码清单 23‑2 函数原型

1 MULTIEDIT_HANDLE MULTIEDIT_CreateIndirect(const

2 GUI_WIDGET_CREATE_INFO\*

3 pCreateInfo, WM_HWIN

4 hWinParent, int x0, int y0,

5 WM_CALLBACK \* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：多行文本控件在父坐标中的最左边像素；

4) y0：多行文本控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，其中可选参数para与缓冲区大小参数BufferSize等效，参数Flags与多行文本模式选择参数ExFlags相同。

多行文本控件基础实验
~~~~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个多行文本控件，通过API函数直接创建的实验可参考官方例程WIDGET_Multiedit.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\WIDGET_Multiedit.c

代码分析
^^^^

(1) 创建对话框

代码清单 23‑3 创建对话框（MultieditDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_MULTIEDIT_0 (GUI_ID_USER + 0x01)

4

5 /\* 资源表 \*/

6 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

7 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

8 480, 0, 0x0, 0 },

9 { MULTIEDIT_CreateIndirect, "Multiedit0", ID_MULTIEDIT_0, 125, 40,

10 530, 140, 0, 0x0, 0 },

11 };

12

13 /*\*

14 \* @brief 以对话框方式间接创建控件

15 \* @note 无

16 \* @param 无

17 \* @retval hWin：资源表中第一个控件的句柄

18 \*/

19 WM_HWIN CreateFramewin(void)

20 {

21 WM_HWIN hWin;

22

23 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

24 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

25 return hWin;

26 }

在代码清单 23‑3中我们定义了2个ID：一个框架窗口ID和一个多行文本控件ID。当然也可以使用emWin预定义好的多行文本控件ID号，但最多只有GUI_ID_MULTIEDIT0到GUI_ID_MULTIEDIT3，共4个ID可供使用。

比较重要的是多行文本控件资源表的倒数第二个参数Para，即文本缓冲区大小。代码清单 23‑3中此参数值为0x0，这个值表示的并不是缓冲区大小为0，而是使用控件内部预设的默认值。不过官方手册上并没有说明默认值具体是多少，如果想自定义缓冲区大小可以使用相应的API函数进行设置。

(2) 对话框回调函数

代码清单 23‑4 对话框回调函数（MultiEditDLG.c文件）

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

12

13 switch (pMsg->MsgId) {

14 case WM_INIT_DIALOG:

15 /\* 初始化Framewin控件 \*/

16 hItem = pMsg->hWin;

17 FRAMEWIN_SetTitleHeight(hItem, 32);

18 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

19 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

20 /\* 初始化MULTIEDIT控件 \*/

21 hItem = WM_GetDialogItem(pMsg->hWin, ID_MULTIEDIT_0);

22 MULTIEDIT_SetReadOnly(hItem, 1);

23 MULTIEDIT_ShowCursor(hItem, 0);

24 MULTIEDIT_SetBufferSize(hItem, 500);

25 MULTIEDIT_SetAutoScrollV(hItem, 1);

26 MULTIEDIT_SetFont(hItem, GUI_FONT_COMIC24B_ASCII);

27 MULTIEDIT_SetBkColor(hItem, MULTIEDIT_CI_READONLY, GUI_BLACK);

28 MULTIEDIT_SetTextColor(hItem, MULTIEDIT_CI_READONLY, GUI_GREEN)

29 ;

30 MULTIEDIT_SetTextAlign(hItem, GUI_TA_LEFT);

31 /\* 显示内容 \*/

32 MULTIEDIT_AddText(hItem,

33 "\\\\\r\n");

34 MULTIEDIT_AddText(hItem, "www.wildfire.com\r\nwww.fireBBS.

35 cn\r\n");

36 MULTIEDIT_AddText(hItem, "STemWIN Version: ");

37 MULTIEDIT_AddText(hItem, GUI_GetVersionString());

38 MULTIEDIT_AddText(hItem,

39 "\r\n\\\\\r\n")

40 ;

41 break;

42 case WM_NOTIFY_PARENT:

43 Id = WM_GetId(pMsg->hWinSrc);

44 NCode = pMsg->Data.v;

45 switch (Id) {

46 case ID_MULTIEDIT_0: // Notifications sent by 'Multiedit'

47 switch (NCode) {

48 case WM_NOTIFICATION_CLICKED:

49 break;

50 case WM_NOTIFICATION_RELEASED:

51 break;

52 case WM_NOTIFICATION_VALUE_CHANGED:

53 break;

54 }

55 break;

56 }

57 break;

58 default:

59 WM_DefaultProc(pMsg);

60 break;

61 }

62 }

1. WM_INIT_DIALOG消息

在上述代码中，设置了对话框也就是框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

和其他控件一样，多行文本控件相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取多行文本控件的句柄。

成功获取到句柄后就可以对控件进一步设置。在代码清单 23‑4中将多行文本控件设置为只读模式，打开光标，设置文本和提示符缓冲区为500个字符，启用自动垂直滚动条。使用MULTIEDIT_SetBkColor函数和MULTIEDIT_SetTextColor函数分别设置控件背景颜色为黑色，字体颜色为绿色
，这两个函数的由于在前面已经设置多行文本为只读模式了，所以在设置控件颜色的时候需要对应不同的模式，最后设置文本对齐方式为左对齐。多行文本控件的外观和功能设置好之后，使用MULTIEDIT_AddText函数添加需要显示的字符串文本。

2. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

多行文本控件基础实验的实验现象如图 23‑2所示，可以看到类似一个串口调试助手接收窗口的效果。

|MULTIE003|

图 23‑2 多行文本控件基础实验实验现象

.. |MULTIE002| image:: media\MULTIE002.png
   :width: 1.04154in
   :height: 0.83323in
.. |MULTIE003| image:: media\MULTIE003.png
   :width: 4.93307in
   :height: 2.96063in
