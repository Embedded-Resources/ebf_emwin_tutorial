.. vim: syntax=rst

单选按钮控件
------

单选按钮（Radio Button）是一种经常用于选项选择的控件。用户可以选中或者取消选中单选按钮，和复选框不同，单选按钮一次只能选中一个选项。一个单选按钮控件可以包含任意数量的选项按钮，这些选项按钮总是垂直排列的。

单选按钮控件的外观如图 20‑1所示。

|RadioB002|

图 20‑1 单选按钮外观

单选按钮控件支持4种通知代码，以区分不同的控件行为，见表格 20‑1。

表格 20‑1 单选按钮支持的通知代码

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 通知代码                      | 描述
     - |

   * - WM_NOTIFICATION_CLICKED
     - 单选按钮已被点击                    |

   * - WM_NOTIFICATION_RELEASED
     - 单选按钮已被释放                    |

   * - WM_NOTIFICATION_MOVED_OUT
     - 单选按钮已                          | 被点击，但指针已被移出框且没有释放  |

   * - WM_NOTIFICATION_VALUE_CHANGED
     - 单选按钮的值已更改                  |


这4种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器向父窗口发送WM_NOTIFY_PARENT消息时，会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

单选按钮控件支持输入焦点，支持键盘或其他类似键盘的外部输入设备对按钮的控制。如果按钮控件已被聚焦，那它可以接收表格 20‑2中的按键消息。

表格 20‑2 单选按钮支持的按键消息

============= =====================
按键          反应
============= =====================
GUI_KEY_RIGHT 单选按钮选项范围增加1
GUI_KEY_DOWN  单选按钮选项范围增加1
GUI_KEY_LEFT  单选按钮选项范围减少1
GUI_KEY_UP    单选按钮选项范围减少1
============= =====================

创建单选按钮控件
~~~~~~~~

单选按钮控件API
^^^^^^^^^

表格 20‑3 单选按钮控件API

============================ ========================================
函数名                       描述
============================ ========================================
RADIO_CreateEx()             创建一个单选按钮控件
RADIO_CreateIndirect()       从对话框资源表中创建单选按钮控件
RADIO_CreateUser()           使用额外字节作为用户数据创建一个单选控件
RADIO_Dec()                  将按钮选择值减少1
RADIO_GetBkColor()           返回单选按钮背景色
RADIO_GetDefaultFont()       返回单选按钮默认字体
RADIO_GetDefaultTextColor()  返回单选按钮默认文本颜色
RADIO_GetFocusColor()        返回单选按钮默认聚焦颜色
RADIO_GetFont()              返回单选按钮当前字体
RADIO_GetText()              返回单选按钮当前文本
RADIO_GetTextColor()         返回单选按钮当前文本颜色
RADIO_GetUserData()          检索用户数据集
RADIO_GetValue()             返回当前选择的按钮
RADIO_Inc()                  将按钮选择值增加1
RADIO_SetBkColor()           设置单选按钮背景颜色
RADIO_SetDefaultFocusColor() 设置单选按钮默认聚焦颜色
RADIO_SetDefaultFont()       设置单选按钮默认字体
RADIO_SetDefaultImage()      设置要用于新单选按钮的图像
RADIO_SetDefaultTextColor()  设置单选按钮默认文本颜色
RADIO_SetFocusColor()        设置单选按钮聚焦颜色
RADIO_SetFont()              设置单选按钮字体
RADIO_SetGroupId()           设置给定单选按钮的组ID
RADIO_SetImage()             设置用于显示单选按钮的图像
RADIO_SetText()              设置单选按钮文本
RADIO_SetTextColor()         设置单选按钮文本颜色
RADIO_SetUserData()          设置单选按钮的额外数据
RADIO_SetValue()             设置按钮选项值
============================ ========================================

单选按钮控件创建函数
^^^^^^^^^^

RADIO_CreateEx()
''''''''''''''''

在指定位置创建具有指定大小的单选小部件。

代码清单 20‑1 单选按钮创建函数

1 RADIO_Handle RADIO_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int ExFlags,

3 int Id, int NumItems, int Spacing);

1)  x0：单选按钮控件在父坐标中的最左侧像素；

2)  y0：单选按钮控件在父坐标中的最顶侧像素；

3)  xSize：单选按钮的水平尺寸，以像素为单位；

4)  ySize：单选按钮的垂直尺寸，以像素为单位；

5)  hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6)  WinFlags：窗口创建标志。由于控件本质上是窗口，所以单选按钮控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7)  ExFlags：预留，未使用；

8)  Id：控件ID号；

9)  NumItems：一个单选按钮控件包含的选项数，默认为2；

10) Spacing：单选按钮控件的每个选项间的垂直间距，以像素为单位。

返回值：创建成功后返回已创建的单选按钮控件句柄，创建失败则返回0。

创建一个单选按钮控件时，请确保给定的控件的垂直尺寸ySize足以显示所有的选项。ySize值应该至少是选项数目NumItems \* 垂直间距Spacing。如果NumItems的给定值小于等于0，则使用默认值2。

RADIO_CreateIndirect()
''''''''''''''''''''''

从对话框资源表中创建单选按钮控件。

代码清单 20‑2对话框方式创建单选按钮函数

1 RADIO_Handle RADIO_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN hWinParent,

3 int x0, int y0, WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：单选按钮控件在父坐标中的最左边像素；

4) y0：单选按钮控件在父坐标中的最顶部像素；

5) cb：单选控件回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，不使用其中的Flag参数，但是使用para参数。

单选按钮控件对para参数的使用情况见表格 20‑4。

表格 20‑4 para参数使用情况

============== ======================================================
para参数的位数 描述
============== ======================================================
bit0 - bit7    一个单选按钮控件包含的选项数目，如果为0，则使用默认值2
bit8 - bit15   每个项目使用的垂直像素数，如果为0，则使用默认像素数
bit16 - bit23  预留，未使用
bit24 - bit31  预留，未使用
============== ======================================================

从上表可知，原本用于设置窗口创建标志的para参数，被单选按钮控件拿来用作自身的一些属性设置，所以从对话框资源表中创建的单选按钮控件需要单独设置窗口创建标志。

单选按钮基础实验
~~~~~~~~

代码分析
^^^^

(1) 创建对话框

在本实验中，我们通过对话框的方式来创建单选按钮控件，这样可以方便管理各种界面元素，见代码清单 20‑3。

代码清单 20‑3 创建对话框（RadioDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_RADIO_0 (GUI_ID_USER + 0x01)

4

5 /\* 资源表 \*/

6 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

7 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

8 480, 0, 0x0, 0 },

9 { RADIO_CreateIndirect, "Radio0", ID_RADIO_0, 60, 40, 120, 400, 0,

10 0x3207, 0 },

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

在上述代码中我们定义了两个ID：一个框架窗口ID和一个单选按钮控件ID。当然我们也可以使用emWin预定义好的单选按钮控件ID，但最多只有GUI_ID_RADIO0到GUI_ID_RADIO7，共8个ID可供使用。

本实验的对话框资源表大部分与其他实验大同小异，就不展开讲了。我们来重点关注一下资源表中单选按钮控件的倒数第二个参数，也就是para参数，它的值为0x3207。根据表格 20‑4可知，单选按钮控件的这个参数值并不是窗口创建标志，而是跟单选按钮控件的布局有关。这个数值的低8位表示单选按钮控件ID_RAD
IO_0中包含7个选项，数值的高8位表示这个ID_RADIO_0中每个选项在垂直方向上占了50像素。与复选框控件不同，单选按钮无法更改圆形框区域的尺寸大小。对于一些分辨率较高或屏幕较小的项目应用来说，可能会显得不太方便。

(2) 对话框回调函数

代码清单 20‑4 对话框回调函数（RadioDLG.c文件）

1 static const GUI_COLOR aColor[] = {GUI_RED, GUI_GREEN, GUI_BLUE,

2 GUI_CYAN, GUI_MAGENTA, GUI_ORANGE};

3 int value = 0;

4 /*\*

5 \* @brief 对话框回调函数

6 \* @note 无

7 \* @param pMsg：消息指针

8 \* @retval 无

9 \*/

10 static void \_cbDialog(WM_MESSAGE \*pMsg)

11 {

12 WM_HWIN hItem;

13 int NCode;

14 int Id;

15

16 switch (pMsg->MsgId) {

17 case WM_INIT_DIALOG:

18 /\* 初始化Framewin控件 \*/

19 hItem = pMsg->hWin;

20 FRAMEWIN_SetTitleHeight(hItem, 32);

21 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

22 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

23 /\* 初始化Radio控件0 \*/

24 hItem = WM_GetDialogItem(pMsg->hWin, ID_RADIO_0);

25 RADIO_SetFont(hItem, GUI_FONT_24_ASCII);

26 RADIO_SetText(hItem, "WHITE", 0);

27 RADIO_SetText(hItem, "RED", 1);

28 RADIO_SetText(hItem, "GREEN", 2);

29 RADIO_SetText(hItem, "BLUE", 3);

30 RADIO_SetText(hItem, "CYAN", 4);

31 RADIO_SetText(hItem, "MAGENTA", 5);

32 RADIO_SetText(hItem, "ORANGE", 6);

33 break;

34 case WM_NOTIFY_PARENT:

35 Id = WM_GetId(pMsg->hWinSrc);

36 NCode = pMsg->Data.v;

37 switch (Id) {

38 case ID_RADIO_0: // Notifications sent by 'Radio0'

39 switch (NCode) {

40 case WM_NOTIFICATION_CLICKED:

41 break;

42 case WM_NOTIFICATION_RELEASED:

43 break;

44 case WM_NOTIFICATION_VALUE_CHANGED:

45 hItem = WM_GetDialogItem(pMsg->hWin, ID_RADIO_0);

46 value = RADIO_GetValue(hItem);

47 WM_InvalidateWindow(pMsg->hWin);

48 break;

49 }

50 break;

51 }

52 case WM_PAINT:

53 GUI_SetBkColor(aColor[value]);

54 GUI_Clear();

55 break;

56 default:

57 WM_DefaultProc(pMsg);

58 break;

59 }

60 }

1. WM_INIT_DIALOG消息

在代码清单 20‑4中设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

单选按钮相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取单选按钮的句柄。成功获取到句柄后，使用RADIO_SetFont函数设置ID_RADIO_0的字体大小，RADIO_SetText函数用来设置
单选按钮各个选项的文本。

2. WM_NOTIFY_PARENT消息

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。

调用RADIO_GetValue函数获取单选按钮控件当前的选项编号，如果选项太多分不清编号的话，还可以调用RADIO_GetText函数获取选项文本。在本实验中，我们通过不同的单选按钮选项来切换不同的对话框背景颜色。想要实现这种效果就需要调用WM_InvalidateWindow函数使整个对话框的客
户窗口无效化，因为如果不无效化整个客户窗口就直接在WM_PAINT消息中修改背景颜色，只会修改单选按钮控件xSize和ySize范围内的背景颜色。

3. WM_PAINT消息

代码清单 20‑4在此消息中实现了对话框客户窗口的背景颜色重绘，根据在WM_NOTIFY_PARENT消息中获取到的单选按钮选项值，使用GUI_SetBkColor函数修改背景颜色，背景颜色存放在一个GUI_COLOR类型的数组aColor[]中。在WM_PAINT消息中除了可以使用颜色相关函数以外
，还可以使用数值显示函数、文本显示函数和2D绘图函数等。

4. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

单选按钮控件基础实验的实验现象如图 20‑2和图 20‑3所示，选择不同的选项，对话框的背景就会变为对应的颜色。

|RadioB003|

图 20‑2 初始选项实验现象

|RadioB004|

图 20‑3 选择不同选项时的实验现象

.. |RadioB002| image:: media\RadioB002.png
   :width: 0.82281in
   :height: 0.93738in
.. |RadioB003| image:: media\RadioB003.png
   :width: 5.76806in
   :height: 3.46228in
.. |RadioB004| image:: media\RadioB004.png
   :width: 5.76806in
   :height: 3.46228in
