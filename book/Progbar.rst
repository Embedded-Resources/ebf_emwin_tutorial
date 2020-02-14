.. vim: syntax=rst

进度条控件
==============

进度条（Progbar）控件也是一种用户数据可视化的控件。类似的控件其实在我们生活中非常常见，例如某些网页的加载过程、下载软件的下载进度和手机的电池电量等等都有出现进度条。

进度条控件的外观如图 28‑1所示。

|Progba002|

图 28‑1 进度条控件外观

进度条控件不支持任何的通知代码、聚焦操作和按键消息，也就是说无法在对话框回调函数中通过通知代码操作进度条控件，控件自己也无法响应外部键盘的动作。

创建进度条控件
~~~~~~~

进度条控件API
^^^^^^^^

表格 28‑1 进度条控件API

======================== ======================================
函数名                   描述
======================== ======================================
PROGBAR_CreateEx()       创建一个进度条控件
PROGBAR_CreateIndirect() 从对话框资源表中创建进度条控件
PROGBAR_CreateUser()     使用额外字节作为用户数据创建进度条
PROGBAR_GetBarColor()    返回进度条颜色
PROGBAR_GetFont()        返回文本字体
PROGBAR_GetMinMax()      返回最大最小值
PROGBAR_GetTextColor()   返回文本颜色
PROGBAR_GetUserData()    检索用户数据集
PROGBAR_GetValue()       返回当前进度值
PROGBAR_SetBarColor()    设置进度条颜色
PROGBAR_SetFont()        设置文本字体
PROGBAR_SetMinMax()      设置最大最小值
PROGBAR_SetText()        设置文本
PROGBAR_SetTextAlign()   设置文本对齐方式
PROGBAR_SetTextColor()   设置文本颜色
PROGBAR_SetTextPos()     设置文本位置
PROGBAR_SetUserData()    设置额外用户数据集
PROGBAR_SetValue()       设置进度值(如果没有文本，则设置百分比)
======================== ======================================

进度条控件创建函数
^^^^^^^^^

PROGBAR_CreateEx()
''''''''''''''''''

在指定位置创建指定大小的进度条控件。

代码清单 28‑1 函数原型

1 PROGBAR_Handle PROGBAR_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id);

1) x0：进度条控件在父坐标中的最左侧像素；

2) y0：进度条控件在父坐标中的最顶侧像素；

3) xSize：进度条的水平尺寸，以像素为单位；

4) ySize：进度条的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以曲线图控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：额外创建标志，用于设置进度条方向，可选值见；

8) Id：控件Id号。

返回值：返回值：创建成功后返回已创建的进度条控件句柄，创建失败则返回0。

表格 28‑2 ExFlags可选参数值

===================== ================================================
ExFlags可选参数值     描述
===================== ================================================
PROGBAR_CF_VERTICAL   将创建一个垂直的进度条，这种进度条不显示任何文本
PROGBAR_CF_HORIZONTAL 将创建一个水平的进度条
===================== ================================================

PROGBAR_CreateIndirect()
''''''''''''''''''''''''

从对话框资源表中创建进度条控件。

代码清单 28‑2 函数原型

1 PROGBAR_Handle PROGBAR_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN hWinParent,

3 int x0, int y0, WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：进度条控件在父坐标中的最左边像素；

4) y0：进度条控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数，其中的Flags参数的可选值与PROGBAR_CreateEx()函数的ExFlags参数相同，用于设置进度条方向。

进度条控件基础实验
~~~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个进度条控件，通过API函数直接创建的实验可参考官方例程WIDGET_Progbar.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\\ WIDGET_Progbar.c

代码分析
^^^^

(1) 创建对话框

代码清单 28‑3 创建对话框（ProgbarDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_PROGBAR_0 (GUI_ID_USER + 0x01)

4

5 /\* 资源表 \*/

6 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

7 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

8 480, 0, 0x0, 0 },

9 { PROGBAR_CreateIndirect, "Progbar", ID_PROGBAR_0, 60, 60, 400, 50,

10 0, 0x0, 0 },

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

在代码清单 28‑3中我们定义了2个ID：1个框架窗口ID和1个进度条控件。当然也可以使用emWin预定义好的滑块控件ID，但最多只有GUI_ID_PROGBAR0到GUI_ID_PROGBAR3，共4个ID可供使用。

(2) 对话框回调函数

代码清单 28‑4 对话框回调函数（ProgbarDLG.c文件）

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE \* pMsg)

8 {

9 WM_HWIN hItem;

10 static U16 progbar_value = 0;

11

12 switch (pMsg->MsgId) {

13 case WM_INIT_DIALOG:

14 /\* 初始化Framewin控件 \*/

15 hItem = pMsg->hWin;

16 FRAMEWIN_SetTitleHeight(hItem, 32);

17 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

18 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

19 /\* 初始化Progbar0 \*/

20 hItem = WM_GetDialogItem(pMsg->hWin, ID_PROGBAR_0);

21 PROGBAR_SetFont(hItem, GUI_FONT_COMIC24B_ASCII);

22 PROGBAR_SetMinMax(hItem, 0, 100);

23 break;

24 case WM_PAINT:

25 hItem = WM_GetDialogItem(pMsg->hWin, ID_PROGBAR_0);

26 progbar_value = PROGBAR_GetValue(hItem);

27 PROGBAR_SetValue(hItem, progbar_value+1);

28 if (progbar_value == 100)

29 PROGBAR_SetValue(hItem, 0);

30 break;

31 default:

32 WM_DefaultProc(pMsg);

33 break;

34 }

35 }

36

1. WM_INIT_DIALOG消息

在代码清单 28‑4中，我们设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

进度条控件相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取进度条控件的句柄。

成功获取到进度条控件句柄后，接着进行进度条控件的初始化设置。使用PROGBAR_SetFont函数设置进度条上的文本字体为COMIC24B，使用PROGBAR_SetMinMax函数设置进度条的最小值为0，最大值为100。

2. WM_PAINT消息

因为进度条控件不支持通知代码，所以也就不能在WM_NOTIFY_PARENT消息中更新进度条状态。在代码清单
28‑4中我们使用WM_PAINT消息更新进度条状态。在WM_PAINT消息中，首先使用PROGBAR_GetValue函数获取进度条当前的值，然后将这个值加1后再通过PROGBAR_SetValue函数输入回进度条，if语句判断进度条的值是否到达100，如果到达100则设值为0，从头开始。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(3) MainTask函数

代码清单 28‑5 MainTask函数（ProgbarDLG.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 WM_HWIN hWin;

10

11 hWin = CreateFramewin();

12

13 while (1) {

14 GUI_Delay(10);

15 WM_InvalidateWindow(hWin);

16 }

17 }

在本实验中，MainTask函数除了调用对话框创建函数以外，还需要在while循环中定时无效化窗口。如代码清单
28‑5所示，MainTask函数每隔10ms调用一次WM_InvalidateWindow函数无效化对话框，目的是使进度条数值更新，因为对话框无效化后会执行回调函数中的MW_PAINT消息，这样就能自动更新进度条的值。

实验现象
^^^^

进度条控件基础实验的实验现象如图 28‑2所示，进度条会从左侧0%一直增加到右侧100%，到100%后又从0%开始，如此反复。

|Progba003|

图 28‑2 进度条控件基础实验现象

.. |Progba002| image:: media\Progba002.png
   :width: 1.04154in
   :height: 0.19789in
.. |Progba003| image:: media\Progba003.png
   :width: 5.76806in
   :height: 3.46228in
