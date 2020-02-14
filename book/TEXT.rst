.. vim: syntax=rst

文本控件
----

文本（TEXT）控件通常用于在对话框或窗口中显示各种文本字段，或者是与其他控件配合使用。虽然前面章节讲到的文本显示和数值显示API函数同样可以在对话框中使用，但是多多少少有些不方便，而且独立的控件便于统一管理和控制。文本控件在对话框中的显示效果如图 21‑1。

|TEXT002|

图 21‑1 文本控件在对话框中的显示

文本控件支持3种通知代码，以区分不同的操作动作，见表格 21‑1。

表格 21‑1 文本控件支持的通知代码

========================= ============================================
通知代码                  描述
========================= ============================================
WM_NOTIFICATION_CLICKED   文本控件已被点击
WM_NOTIFICATION_RELEASED  文本控件已被释放
WM_NOTIFICATION_MOVED_OUT 文本控件已被点击，但指针已被移出框且没有释放
========================= ============================================

实际上文本控件的这些通知代码基本上用不到，就连GUIBuilder生成的代码框架也没有添加这些通知代码。文本控件不支持获得输入焦点，并且不会对键盘输入做出反应。

创建文本控件
~~~~~~

文本控件API
^^^^^^^

表格 21‑2 文本控件API

========================== ======================================
函数名                     描述
========================== ======================================
TEXT_CreateEx()            创建文本控件
TEXT_CreateIndirect()      从对话框资源表中创建文本控件
TEXT_CreateUser()          使用额外的字节作为用户数据创建文本控件
TEXT_GetBkColor()          返回当前背景色
TEXT_GetDefaultFont()      返回默认字体
TEXT_GetDefaultTextColor() 返回默认文本颜色
TEXT_GetDefaultWrapMode()  返回默认文本换行模式
TEXT_GetFont()             返回当前字体
TEXT_GetNumLines()         返回文本控件中当前显示的行数
TEXT_GetText()             返回当前文本内容
TEXT_GetTextAlign()        返回当前文本对齐方式
TEXT_GetTextColor()        返回当前文本颜色
TEXT_GetUserData()         检索用户数据集
TEXT_GetWrapMode()         返回当前文本换行模式
TEXT_SetBkColor()          返回当前文本背景颜色
TEXT_SetDec()              设置要显示的数值
TEXT_SetDefaultFont()      设置默认字体
TEXT_SetDefaultTextColor() 设置默认文本颜色
TEXT_SetDefaultWrapMode()  设置默认文本换行模式
TEXT_SetFont()             设置文本字体
TEXT_SetText()             设置想要显示的文本
TEXT_SetTextAlign()        设置文本对齐方式
TEXT_SetTextColor()        设置当前文本颜色
TEXT_SetUserData()         设置额外用户数据集
TEXT_SetWrapMode()         设置文本换行模式
========================== ======================================

文本控件创建函数
^^^^^^^^

TEXT_CreateEx()
'''''''''''''''

在指定位置创建指定大小的文本控件。

代码清单 21‑1 函数原型

1 TEXT_Handle TEXT_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int ExFlags,

3 int Id, const char \*pText);

1) x0：文本控件在父坐标中的最左侧像素；

2) y0：文本控件在父坐标中的最顶侧像素；

3) xSize：文本的水平尺寸，以像素为单位；

4) ySize：文本的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以文本控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：文本的对齐方式，可选值见表格 21‑3；

8) Id：控件ID号；

9) pText：指向要显示的文本的指针。

返回值：创建成功后返回已创建的文本控件句柄，创建失败则返回0。

表格 21‑3 ExFlags参数可选值

=============== ============
可选值          描述
=============== ============
TEXT_CF_LEFT    水平左对齐
TEXT_CF_RIGHT   水平右对齐
TEXT_CF_HCENTER 水平中心对齐
TEXT_CF_TOP     垂直顶部对齐
TEXT_CF_BOTTOM  垂直底部对齐
TEXT_CF_VCENTER 垂直中心对齐
=============== ============

TEXT_CreateIndirect()
'''''''''''''''''''''

从对话框资源表中创建文本控件。

代码清单 21‑2对话框方式创建文本控件函数

1 TEXT_Handle TEXT_CreateIndirect(const GUI_WIDGET_CREATE_INFO \*

2 pCreateInfo, WM_HWIN hWinParent, int

3 x0, int y0, WM_CALLBACK \* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：文本控件在父坐标中的最左边像素；

4) y0：文本控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数。其中的Flags参数的可选值与表格 21‑3相同。

文本控件基础实验
~~~~~~~~

代码分析
^^^^

(1) 创建对话框

在本实验中，我们通过对话框的方式来创建文本控件，这样可以方便管理各种界面元素，见代码清单 21‑3。

代码清单 21‑3 创建对话框（TextDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_TEXT_0 (GUI_ID_USER + 0x01)

4 #define ID_TEXT_1 (GUI_ID_USER + 0x02)

5 #define ID_TEXT_2 (GUI_ID_USER + 0x03)

6 #define ID_TEXT_3 (GUI_ID_USER + 0x04)

7 #define ID_TEXT_4 (GUI_ID_USER + 0x05)

8

9 /\* 资源表 \*/

10 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

11 { FRAMEWIN_CreateIndirect, "", ID_FRAMEWIN_0, 0, 0, 800, 480, 0,

12 0x0, 0 },

13 { TEXT_CreateIndirect, "Text0", ID_TEXT_0, 30, 25, 460, 50, 0,

14 0x0, 0 },

15 { TEXT_CreateIndirect, "Text1", ID_TEXT_1, 30, 120, 100, 40, 0,

16 0x0, 0 },

17 { TEXT_CreateIndirect, "Text2", ID_TEXT_2, 30, 165, 100, 40, 0,

18 0x0, 0 },

19 { TEXT_CreateIndirect, "Text3", ID_TEXT_3, 30, 210, 100, 40, 0,

20 0x0, 0 },

21 { TEXT_CreateIndirect, "Text4", ID_TEXT_4, 350, 120, 250, 130, 0,

22 0x0, 0 },

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

在代码清单 21‑3中我们定义了6个ID：一个框架窗口ID和5个文本控件ID。当然也可以使用emWin预定义好的文本控件ID，但最多只有GUI_ID_TEXT0到GUI_ID_TEXT9，共10个ID可供使用。在对话框或窗口中显示的文本控件，它的起始坐标是相对于客户窗口的。

(2) 对话框回调函数

代码清单 21‑4 对话框回调函数（TextDLG.c文件）

1 static void \_cbDialog(WM_MESSAGE \*pMsg)

2 {

3 WM_HWIN hItem;

4

5 switch (pMsg->MsgId) {

6 case WM_INIT_DIALOG:

7 /\* 初始化Text0 \*/

8 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

9 TEXT_SetFont(hItem, GUI_FONT_COMIC24B_ASCII);

10 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

11 TEXT_SetText(hItem, "STemWIN@EmbedFire STM32F429");

12 /\* 初始化Text1 \*/

13 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_1);

14 TEXT_SetFont(hItem, GUI_FONT_8X16X1X2);

15 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

16 TEXT_SetText(hItem, "Text");

17 /\* 初始化Text2 \*/

18 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_2);

19 TEXT_SetFont(hItem, GUI_FONT_8X16X2X2);

20 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

21 TEXT_SetText(hItem, "Text");

22 /\* 初始化Text3 \*/

23 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_3);

24 TEXT_SetFont(hItem, GUI_FONT_8X16X3X3);

25 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

26 TEXT_SetText(hItem, "Text");

27 /\* 初始化Text4 \*/

28 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_4);

29 TEXT_SetFont(hItem, GUI_FONT_D48X64);

30 TEXT_SetTextAlign(hItem, GUI_TA_HCENTER \| GUI_TA_VCENTER);

31 TEXT_SetDec(hItem, 0, 5, 0, 0, 0);

32 break;

33 default:

34 WM_DefaultProc(pMsg);

35 break;

36 }

37 }

1. WM_INIT_DIALOG消息

在本实验中，我们不再设置框架窗口的标题栏，而是直接设置文本控件。使用TEXT_SetTextAlign 函数把5个文本控件的文本对齐方式全部设置为水平左对齐和垂直居中对齐，全部文本对齐方式见表格
21‑3，使用TEXT_SetFont函数分别给5个控件设置不同的字体，然后TEXT_SetText函数输入想要显示的文本内容，其中最后一个控件使用TEXT_SetDec函数显示一个5位的不含小数和符号的十进制数。关于TEXT_SetDec函数的具体介绍请查看官方手册。

2. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(3) 主任务函数

在对话框回调函数中设置好文本之后，我们将ID_TEXT_4显示的数值不停的累加，实现一个简单的计数功能。这个功能的实现方式很多，本实验讲解其中一种，见代码清单 21‑5。

代码清单 21‑5 主任务函数（TextDLG.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 WM_HWIN hWin;

10 WM_HWIN hItem;

11 U32 temp = 0;

12

13 /\* 创建对话框 \*/

14 hWin = CreateFramewin();

15 /\* 获取子控件句柄 \*/

16 hItem = WM_GetDialogItem(hWin, ID_TEXT_4);

17 while (1) {

18 /\* 使用文本控件显示数值 \*/

19 TEXT_SetDec(hItem, temp, 5, 0, 0, 0);

20 temp++;

21 if (temp > 99999) {

22 temp = 0;

23 }

24 /\* 延时50ms \*/

25 GUI_Delay(50);

26 }

27 }

在前面讲解框架窗口基础实验的时候提到过，对话框创建函数在创建成功后会返回资源表中第一个控件的句柄，在本实验中第一个控件就是框架窗口。如代码清单 21‑5所示，根据返回来的框架窗口句柄，利用WM_GetDialogItem函数和子控件ID号就可以得到对话框内子控件的句柄，即上述代码中的ID_TEXT_
4的句柄。得到子控件句柄后，在while循环中调用TEXT_SetDec函数显示数值temp，并循环累加。这样，一个非常简单的累加计数功能就完成了。

实验现象
^^^^

文本控件基础实验最终的实验现象如图 21‑2所示，可以看到各种文本控件的显示效果，并且对话框右侧的数值会不断累加。

|TEXT003|

图 21‑2 文本控件基础实验现象

.. |TEXT002| image:: media\TEXT002.png
   :width: 2.08307in
   :height: 1.24984in
.. |TEXT003| image:: media\TEXT003.png
   :width: 5.76806in
   :height: 3.46228in
