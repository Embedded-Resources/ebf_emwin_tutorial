.. vim: syntax=rst

列表轮控件
-----

列表轮（Listwheel）控件在造型和使用上非常类似于安卓的那种选择日期和时间的控件，控件的整个数据区域可通过指针输入设备（PID）进行移动，在数据区域移动的时候释放PID设备，数据区域会减慢运动速度，并通过在吸附位置捕捉一个新项目来停止。此外，列表轮控件的数据可以循环显示，在最后一个数据项之后，
会像一个轮子一样继续显示第一个数据项。

列表轮控件可以使用用户绘制机制加上Alpha混合做出很复杂美观的效果，如图 26‑1所示，emwin默认的控件外观是很扁平很普通的。

|Listwh002|

图 26‑1 列表轮控件美化后的外观

列表轮控件支持4种通知代码，以区分各种不同的操作动作，见表格 26‑1。

表格 26‑1 列表轮支持的通知代码

=========================== ========================================
通知代码                    描述
=========================== ========================================
WM_NOTIFICATION_CLICKED     控件已被点击
WM_NOTIFICATION_RELEASED    控件已被释放
WM_NOTIFICATION_MOVED_OUT   控件已被点击，但指针已被移出框且没有释放
WM_NOTIFICATION_SEL_CHANGED 项目已在对齐位置对齐
=========================== ========================================

这4种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击列表轮后，窗口管理器会让表格控件向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的控件动作以及实现各种功能。列表轮控件
不支持任何的按键消息。

创建列表轮控件
~~~~~~~

列表轮控件API
^^^^^^^^

表格 26‑2 列表轮控件API

=========================== ======================================
函数名                      描述
=========================== ======================================
LISTWHEEL_AddString()       添加一个新的字符串
LISTWHEEL_CreateEx()        创建一个列表轮控件
LISTWHEEL_CreateIndirect()  从对话框资源表中创建列表轮控件
LISTWHEEL_CreateUser()      使用额外的字节作为用户数据创建列表轮
LISTWHEEL_GetBkColor()      获取当前背景颜色
LISTWHEEL_GetFont()         获取当前字体
LISTWHEEL_GetItemFromPos()  返回与给定位置匹配的项目的索引
LISTWHEEL_GetItemText()     返回请求项的文本
LISTWHEEL_GetLBorder()      返回左边框的大小，以像素为单位
LISTWHEEL_GetLineHeight()   返回一项的高度
LISTWHEEL_GetNumItems()     返回数据项数
LISTWHEEL_GetPos()          返回当前参与项目的项目索引
LISTWHEEL_GetRBorder()      返回右边框的大小，以像素为单位
LISTWHEEL_GetSel()          返回当前选择的项目
LISTWHEEL_GetSnapPosition() 返回捕捉位置，以像素为单位
LISTWHEEL_GetTextAlign()    返回用于绘制数据项的文本对齐方式
LISTWHEEL_GetTextColor()    返回文本颜色
LISTWHEEL_GetUserData()     检索用户数据
LISTWHEEL_IsMoving()        返回控件是否在移动
LISTWHEEL_MoveToPos()       将列表轮移动到指定位置
LISTWHEEL_OwnerDraw()       绘制控件件的默认函数
LISTWHEEL_SetBkColor()      设置当前背景颜色
LISTWHEEL_SetDeceleration() 设置轮的减速度
LISTWHEEL_SetFont()         设置当前字体
LISTWHEEL_SetLBorder()      设置左边框的大小，以像素为单位
LISTWHEEL_SetLineHeight()   设置一个数据项的高度
LISTWHEEL_SetOwnerDraw()    设置用于绘制控件的所有者绘图函数
LISTWHEEL_SetPos()          将列表轮设置为给定位置
LISTWHEEL_SetRBorder()      设置右边框的大小，以像素为单位
LISTWHEEL_SetSel()          设置当前选定的项
LISTWHEEL_SetSnapPosition() 从控件的顶部设置对齐位置，以像素为单位
LISTWHEEL_SetText()         设置控件的文本
LISTWHEEL_SetTextAlign()    设置文本对齐方式
LISTWHEEL_SetTextColor()    设置文本颜色
LISTWHEEL_SetTimerPeriod()  设置定时器周期
LISTWHEEL_SetUserData()     设置额外用户数据
LISTWHEEL_SetVelocity()     设置轮环的移动速度
=========================== ======================================

列表轮控件创建函数
^^^^^^^^^

LISTWHEEL_CreateEx()
''''''''''''''''''''

在指定位置创建指定大小的列表轮控件。

代码清单 26‑1 函数原型

1 LISTWHEEL_Handle LISTWHEEL_CreateEx(int x0, int y0, int xSize, int

2 ySize, WM_HWIN hParent, int

3 WinFlags, int ExFlags, int Id,

4 const GUI_ConstString \*ppText);

1) x0：列表轮控件在父坐标中的最左侧像素；

2) y0：列表轮控件在父坐标中的最顶侧像素；

3) xSize：列表轮的水平尺寸，以像素为单位；

4) ySize：列表轮的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以列表轮控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：预留，未使用；

8) Id：控件ID号；

9) ppText：指向包含要显示的元素的字符串指针数组的指针。

返回值：创建成功后返回已创建的表格控件句柄，创建失败则返回0。如果使用参数ppText，则数组的最后一个元素需要为NULL。

LISTWHEEL_CreateIndirect()
''''''''''''''''''''''''''

从对话框资源表中创建滑块控件。

代码清单 26‑2 函数原型

1 LISTWHEEL_Handle LISTWHEEL_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN

3 hWinParent, int x0, int y0,

4 WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：列表轮控件在父坐标中的最左边像素；

4) y0：列表轮控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数，其中的Flags参数的可选值与LISTWHEEL_CreateEx()函数的WinFlags参数相同。

列表轮基础实验
~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个列表轮控件，通过API函数直接创建的实验可参考官方例程MEMDEV_ListWheelEffects，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\\ MEMDEV_ListWheelEffects

代码分析
^^^^

(1) 创建对话框

代码清单 26‑3 创建对话框（ListwheelDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_LISTWHEEL_0 (GUI_ID_USER + 0x01)

4 #define ID_LISTWHEEL_1 (GUI_ID_USER + 0x02)

5 #define ID_LISTWHEEL_2 (GUI_ID_USER + 0x03)

6

7 /\* 资源表 \*/

8 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

9 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

10 480, 0, 0x0, 0 },

11 { LISTWHEEL_CreateIndirect, "", ID_LISTWHEEL_0, 210, 50, 120, 178,

12 WM_CF_MEMDEV, 0x0, 0 },

13 { LISTWHEEL_CreateIndirect, "", ID_LISTWHEEL_1, 330, 50, 140, 178,

14 WM_CF_MEMDEV, 0x0, 0 },

15 { LISTWHEEL_CreateIndirect, "", ID_LISTWHEEL_2, 470, 50, 120, 178,

16 WM_CF_MEMDEV, 0x0, 0 },

17 };

18

19 /*\*

20 \* @brief 以对话框方式间接创建控件

21 \* @note 无

22 \* @param 无

23 \* @retval hWin：资源表中第一个控件的句柄

24 \*/

25 WM_HWIN CreateFramewin(void)

26 {

27 WM_HWIN hWin;

28

29 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

30 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

31 return hWin;

32 }

在代码清单 26‑3中我们定义了4个ID：1个框架窗口ID和3个列表轮控件ID。当然也可以使用emWin预定义好的列表轮控件ID，但最多只有GUI_ID_LISTWHEEL0到GUI_ID_LISTWHEEL3，共4个ID可供使用。

(2) 对话框回调函数

1. WM_INIT_DIALOG消息

本实验的对话框回调函数代码较多，我们把回调函数按不同的消息分开来分析。首先是WM_INIT_DIALOG消息，见代码清单 26‑4。

代码清单 26‑4 WM_INIT_DIALOG消息（ListwheelDLG.c文件）

1 static char \*_apYear[] = {

2 "1990","1991", "1992", "1993", "1994", "1995", "1996",

3 "1997", "1998", "1999", "2000", "2001", "2002", "2003",

4 "2004", "2005", "2006", "2007", "2008", "2009", "2010",

5 "2011", "2012", "2013", "2014", "2015", "2016", "2017",

6 "2018", "2019", "2020",

7 };

8

9 static char \*_apMonth[] = {

10 "January","February","March",

11 "April","May","June",

12 "July","August","September",

13 "October","November","December",

14 };

15

16 static char \*_apDay[] = {

17 "01", "02", "03", "04",

18 "05", "06", "07", "08",

19 "09", "10", "11", "12",

20 "13", "14", "15", "16",

21 "17", "18", "19", "20",

22 "21", "22", "23", "24",

23 "25", "26", "27", "28",

24 "29", "30", "31",

25 };

26

27 case WM_INIT_DIALOG:

28 {

29 /\* 初始化框架窗口控件 \*/

30 hItem = pMsg->hWin;

31 FRAMEWIN_SetTitleHeight(hItem, 32);

32 FRAMEWIN_SetFont(hItem, GUI_FONT_32_1);

33 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

34

35 /\* 初始化LISTWHEEL \*/

36 for (int i = 0; i < 3; i++) {

37 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_0 + i);

38 /\* 每个选项的高度 \*/

39 LISTWHEEL_SetLineHeight(hItem, 34);

40 /\* 滚轮吸附的位置 \*/

41 LISTWHEEL_SetSnapPosition(hItem, (178 - 34) / 2);

42 /\* 字体大小 \*/

43 LISTWHEEL_SetFont(hItem, GUI_FONT_32B_ASCII);

44 /\* 文本对齐方式 \*/

45 LISTWHEEL_SetTextAlign(hItem, GUI_TA_HCENTER \| GUI_TA_VCENTER);

46 /\* 文本颜色 \*/

47 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_UNSEL, 0x191919);

48 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x007dfe);

49 /\* 停止速度 \*/

50 LISTWHEEL_SetDeceleration(hItem, 35);

51 /\* 绑定用户绘制函数 \*/

52 LISTWHEEL_SetOwnerDraw(hItem, \_OwnerDraw);

53 /\* 初始选项 \*/

54 LISTWHEEL_SetSel(hItem, 0);

55 }

56 /\* 添加LISTWHEEL0文本项 \*/

57 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_0);

58 for (int i = 0; i < GUI_COUNTOF(_apYear); i++) {

59 LISTWHEEL_AddString(hItem, \*(_apYear + i));

60 }

61 /\* 添加LISTWHEEL1文本项 \*/

62 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_1);

63 for (int i = 0; i < GUI_COUNTOF(_apMonth); i++) {

64 LISTWHEEL_AddString(hItem, \*(_apMonth + i));

65 }

66 /\* 添加LISTWHEEL2文本项 \*/

67 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_2);

68 for (int i = 0; i < GUI_COUNTOF(_apDay); i++) {

69 LISTWHEEL_AddString(hItem, \*(_apDay + i));

70 }

71 break;

72 }

如代码清单 26‑4所示，首先定义了3个字符串数组用来存放三个列表轮的选项文本，分别是年份_apYear、月份_apMonth、日期_apDay。设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

本实验中我们把3个列表轮控件的属性设置成完全一样的，那就需要使用for循环依次初始化3个列表轮控件，其中LISTWHEEL_SetLineHeight函数设置的高度是包含字体高度的，LISTWHEEL_SetSnapPosition函数将列表轮停止时的选项吸附位置设置在中间，使用LISTWHEEL_
SetOwnerDraw函数设置一个用户绘制函数，用来美化控件。列表轮控件的基本属性设置好后，使用LISTWHEEL_AddString函数向各个控件添加文本选项。

2. WM_NOTIFY_PARENT消息

代码清单 26‑5 WM_NOTIFY_PARENT消息（ListwheelDLG.c文件）

1 case WM_NOTIFY_PARENT:

2 {

3 /\* 获取控件ID \*/

4 Id = WM_GetId(pMsg->hWinSrc);

5 /\* 获取通知代码 \*/

6 NCode = pMsg->Data.v;

7 switch (Id) {

8 case ID_LISTWHEEL_0: // Notifications sent by 'Listwheel'

9 switch (NCode) {

10 case WM_NOTIFICATION_CLICKED:

11 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_0);

12 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x191919);

13 break;

14 case WM_NOTIFICATION_RELEASED:

15 break;

16 case WM_NOTIFICATION_SEL_CHANGED:

17 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_0);

18 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x007dfe);

19 /\* 获取停留的位置 \*/

20 U8 index = LISTWHEEL_GetPos(hItem);

21 /\* 选择停留位置的文本 \*/

22 LISTWHEEL_SetSel(hItem, index);

23 break;

24 }

25 break;

26 case ID_LISTWHEEL_1: // Notifications sent by 'Listwheel'

27 switch (NCode) {

28 case WM_NOTIFICATION_CLICKED:

29 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_1);

30 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x191919);

31 break;

32 case WM_NOTIFICATION_RELEASED:

33 break;

34 case WM_NOTIFICATION_SEL_CHANGED:

35 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_1);

36 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x007dfe);

37 /\* 获取停留的位置 \*/

38 U8 index = LISTWHEEL_GetPos(hItem);

39 /\* 选择停留位置的文本 \*/

40 LISTWHEEL_SetSel(hItem, index);

41 break;

42 }

43 break;

44 case ID_LISTWHEEL_2: // Notifications sent by 'Listwheel'

45 switch (NCode) {

46 case WM_NOTIFICATION_CLICKED:

47 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_2);

48 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x191919);

49 break;

50 case WM_NOTIFICATION_RELEASED:

51 break;

52 case WM_NOTIFICATION_SEL_CHANGED:

53 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTWHEEL_2);

54 LISTWHEEL_SetTextColor(hItem, LISTWHEEL_CI_SEL, 0x007dfe);

55 /\* 获取停留位置的文本索引 \*/

56 U8 index = LISTWHEEL_GetPos(hItem);

57 /\* 选择停留位置的文本 \*/

58 LISTWHEEL_SetSel(hItem, index);

59 break;

60 }

61 break;

62 }

63 break;

64 }

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。

代码清单 26‑5中我们用到了两种通知代码，首先是WM_NOTIFICATION_CLICKED，在这个通知代码中设置控件选项被选中时的颜色与未被选中时相同，这样在移动控件滚轮的时候不会出现突出显示选项。然后是WM_NOTIFICATION_SEL_CHANGED，滚轮停下来之后选择中间选项的关键就
在此实现，使用LISTWHEEL_GetPos函数获取滚轮此时吸附的选项的索引，然后根据索引使用LISTWHEEL_SetSel函数选择当前吸附的文本，就可以实现列表轮停止后选择中间选项的效果。

如果想要使用列表轮实现其他功能，例如用它给RTC设置日期和时间，那么可以在代码清单 26‑5的基础上使用LISTWHEEL_GetItemText函数将此时列表轮吸附位置的文本获取下来即可。

3. 其他消息

代码清单 26‑6 default消息（ListviewDLG.c文件）

1 default:

2 WM_DefaultProc(pMsg);

3 break;

如代码清单 26‑6所示，所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(3) 用户绘制函数

代码清单 26‑7 用户绘制函数（ListwheelDLG.c文件）

1 /*\*

2 \* @brief 用户绘制函数

3 \* @note 无

4 \* @param pDrawItemInfo：指向WIDGET_ITEM_DRAW_INFO结构的指针

5 \* @retval 默认绘制函数 或 0

6 \*/

7 static int \_OwnerDraw(const WIDGET_ITEM_DRAW_INFO \*pDrawItemInfo)

8 {

9 GUI_RECT aRect;

10

11 switch (pDrawItemInfo->Cmd) {

12 case WIDGET_ITEM_DRAW_OVERLAY:

13 /\* 获取控件坐标 \*/

14 aRect.x0 = pDrawItemInfo->x0;

15 aRect.x1 = pDrawItemInfo->x1;

16 aRect.y1 = pDrawItemInfo->y1;

17 /\* 画分割线 \*/

18 GUI_SetColor(GUI_GRAY_E7);

19 GUI_DrawLine(aRect.x0, (aRect.y1 - 19 - 16) / 2, aRect.x1, (

20 aRect.y1 - 19 - 16) / 2);

21 GUI_DrawLine(aRect.x0, (aRect.y1 + 19 + 16) / 2, aRect.x1, (

22 aRect.y1 + 19 + 16) / 2);

23 break;

24 default:

25 return LISTWHEEL_OwnerDraw(pDrawItemInfo);

26 }

27 return 0;

28 }

列表轮控件可以使用用户绘制函数来绘制或者一些外观，如代码清单 26‑7所示，在用户绘制函数_OwnerDraw的WIDGET_ITEM_DRAW_OVERLAY消息中，获取列表轮控件自身的左上角起始坐标和右下角终点坐标，利用这些坐标值就可以调用GUI_DrawLine函数在控件上画分割线了。

在代码清单 26‑7中，控件的坐标值是从WIDGET_ITEM_DRAW_INFO结构体内获取的，此结构体的原型如代码清单 26‑8所示。

代码清单 26‑8 WIDGET_ITEM_DRAW_INFO原型

1 typedef struct {

2 WM_HWIN hWin;

3 int Cmd;

4 int ItemIndex;

5 int Col;

6 int x0, y0, x1, y1;

7 void \*p;

8 } WIDGET_ITEM_DRAW_INFO;

1) hWin：控件句柄；

2) Cmd：控件绘制消息，具体可选值见表格 26‑3；

3) ItemIndex：需要绘制的项目的索引，从0开始；

4) Col：需要绘制的项目的列索引，从0开始；

5) x0：控件的左上角横坐标；

6) y0：控件的左上角纵坐标；

7) x1：控件的右下角横坐标；

8) y1：控件的右下角纵坐标。

表格 26‑3 Cmd参数可选值

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - Cmd参数可选值          | 描述
     - |

   * - WIDGET_ITEM_GET_XSIZE
     - 返回给定项目的宽度，以像素为单位           |

   * - WIDGET_ITEM_GET_YSIZE
     - 返回给定项目的高度，以像素为单位           |

   * - WIDGET_ITEM_DRAW
     - 在给定位置绘制给定项目                     |

   * - WIDGET_DRAW_BACKGROUND
     - 绘制控件背景                               |

   * - WIDGET_DRAW_OVERLAY
     - 在所有其他绘图操                           | 作完成后发送，允许在控件上方绘制一些覆盖项 |


WIDGET_ITEM_DRAW_INFO结构体中四个坐标参数是相对于控件本身的，例如一个控件长100像素、宽50像素，那么这四个坐标参数分别是：x0和y0都为0，x1为100-1，y1为50-1。

实验现象
^^^^

列表轮基础实验的实验现象如图 26‑2所示，分别上下滑动年月日3个控件可以选择不同的选项，每个控件的选项都是循环显示的。

|Listwh003|

图 26‑2 列表轮基础实验现象

.. |Listwh002| image:: media\Listwh002.png
   :width: 2.07087in
   :height: 1.37795in
.. |Listwh003| image:: media\Listwh003.png
   :width: 5.76806in
   :height: 3.46228in
