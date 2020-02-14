.. vim: syntax=rst

下拉框控件
-----

下拉框（Dropdown）控件是一种用于在列表中选择一个或多个元素的控件，我们经常会在网页或者手机APP中见到这种控件。下拉框控件默认会处于非展开状态并显示当前选定的项目，如果用户点击展开了下拉框控件，则会出现一个列表供用户选择新的项目。

下拉框控件默认开启皮肤色，外观如图 24‑1所示。

|Dropdo002|

图 24‑1 下拉框外观

下拉框控件支持5种通知代码，以区分各种不同的操作动作，见表格 24‑1。

表格 24‑1 下拉框控件支持的通知代码

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 通知代码                       | 描述
     - |

   * - WM_NOTIFICATION_CLICKED
     - 下拉框已被点击                             |

   * - WM_NOTIFICATION_RELEASED
     - 下拉框已被释放                             |

   * - WM_NOTIFICATION_MOVED_OUT
     - 下拉框已被点击，但指针已被移出框且没有释放 |

   * - WM_NOTIFICATION_SCROLL_CHANGED
     - 展开的下拉框的滚动条位置已被更改           |

   * - WM_NOTIFICATION_SEL_CHANGED
     - 下拉框列表的选择已被更改                   |


这5种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器会让下拉框向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

同时下拉框控件也支持输入焦点，它可以接收如表格 24‑2所示的按键消息。

表格 24‑2 下拉框支持的按键消息

============= ====================================
按键消息      描述
============= ====================================
GUI_KEY_ENTER 从展开的下拉框中选择一项并关闭该列表
GUI_KEY_SPACE 展开下拉框
============= ====================================

创建下拉框控件
~~~~~~~

下拉框控件API
^^^^^^^^

表格 24‑3 下拉框控件API

=================================== ====================================
函数名                              描述
=================================== ====================================
DROPDOWN_AddString()                向下拉框控件添加一个新元素
DROPDOWN_Collapse()                 关闭下拉框
DROPDOWN_CreateEx()                 创建一个下拉框
DROPDOWN_CreateIndirect()           从对话框资源表中创建下拉框控件
DROPDOWN_CreateUser()               使用额外的字节作为用户数据创建下拉框
DROPDOWN_DecSel()                   递减选择前一个元素
DROPDOWN_DecSelExp()                在展开的下拉框中递减选择前一个元素
DROPDOWN_DeleteItem()               删除下拉框中的一个元素
DROPDOWN_Expand()                   展看对话框
DROPDOWN_GetBkColor()               返回背景颜色
DROPDOWN_GetColor()                 返回按钮或箭头使用的颜色
DROPDOWN_GetDefaultFont()           返回默认字体
DROPDOWN_GetFont()                  返回当前字体
DROPDOWN_GetItemDisabled()          返回给定项目的状态
DROPDOWN_GetItemText()              返回项目文本
DROPDOWN_GetListbox()               以展开状态返回附加下拉框的句柄
DROPDOWN_GetNumItems()              返回下拉框中的项数
DROPDOWN_GetSel()                   返回当前选定元素的编号
DROPDOWN_GetSelExp()                返回处于展开状态的当前选定元素的编号
DROPDWON_GetTextColor()             返回文本颜色
DROPDOWN_GetUserData()              检索用户数据集
DROPDOWN_IncSel()                   递增选择后一个元素
DROPDOWN_IncSelExp()                在展开的下拉框中递增选择后一个元素
DROPDOWN_InsertString()             插入字符串到下拉框
DROPDOWN_SetAutoScroll()            启用自动滚动条
DROPDOWN_SetBkColor()               设置背景颜色
DROPDOWN_SetColor()                 按钮或箭头使用的颜色
DROPDOWN_SetDefaultColor()          设置默认按钮或箭头使用的颜色
DROPDOWN_SetDefaultFont()           设置默认字体
DROPDOWN_SetDefaultScrollbarColor() 设置下拉框滚动条的默认颜色
DROPDOWN_SetFont()                  设置文本字体
DROPDOWN_SetItemDisabled()          设置给定项目的状态
DROPDOWN_SetItemSpacing()           设置下拉框的元素间距
DROPDOWN_SetListHeight()            设置下拉框展开的高度，以像素为单位
DROPDOWN_SetScrollbarColor()        设置下拉框滚动条颜色
DROPDOWN_SetScrollbarWidth()        设置下拉框滚动条宽度
DROPDOWN_SetSel()                   设置当前选择的元素
DROPDOWN_SetSelExp()                设置展开状态选择的元素
DROPDOWN_SetTextAlign()             设置文本对齐方式
DROPDOWN_SetTextColor()             设置文本颜色
DROPDOWN_SetTextHeight()            设置在下拉框关闭状态的文本高度
DROPDOWN_SetUpMode()                设置下拉框为向上展看模式
DROPDOWN_SetUserData()              设置额外用户数据集
=================================== ====================================

下拉框控件创建函数
^^^^^^^^^

DROPDOWN_CreateEx()
'''''''''''''''''''

在指定位置创建指定大小的下拉框控件。

代码清单 24‑1 函数原型

1 DROPDOWN_Handle DROPDOWN_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id);

1) x0：下拉框控件在父坐标中的最左侧像素；

2) y0：下拉框控件在父坐标中的最顶侧像素；

3) xSize：下拉框非展开状态的水平尺寸，以像素为单位；

4) ySize：下拉框非展开状态的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以下拉框控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：下拉框额外的创建标志，可选值见表格 24‑4；

8) Id：控件ID号。

返回值：创建成功后返回已创建的下拉框控件句柄，创建失败则返回0。

表格 24‑4 ExFlags可选值

========================= ==========================
可选参数值                描述
========================= ==========================
0                         无功能
DROPDOWN_CF_AUTOSCROLLBAR 使能自动使用滚动条
DROPDOWN_CF_UP            设置控件展开方向为向上展开
========================= ==========================

DROPDOWN_CreateIndirect()
'''''''''''''''''''''''''

从对话框资源表中创建下拉框控件。

代码清单 24‑2对话框方式创建下拉框函数

1 DROPDOWN_Handle DROPDOWN_CreateIndirect(const GUI_WIDGET_CREATE_INFO\*

2 pCreateInfo, WM_HWIN

3 hWinParent, int x0, int y0,

4 WM_CALLBACK\* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：下拉框控件在父坐标中的最左边像素；

4) y0：下拉框控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数，其中的Flags参数的可选值与表格 24‑4相同。

下拉框控件基础实验
~~~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个下拉框控件，通过API函数直接创建的实验可参考官方例程WIDGET_Dropdown.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\WIDGET_Dropdown.c

代码分析
^^^^

(1) 创建对话框

代码清单 24‑3 创建对话框（DropdownDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_DROPDOWN_0 (GUI_ID_USER + 0x01)

4 #define ID_DROPDOWN_1 (GUI_ID_USER + 0x02)

5 #define ID_TEXT_0 (GUI_ID_USER + 0x03)

6

7 /\* 资源表 \*/

8 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

9 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

10 480, 0, 0x0, 0 },

11 { DROPDOWN_CreateIndirect, "Dropdown1", ID_DROPDOWN_0, 150, 180,

12 130, 25, 0, 0x0, 0 },

13 { DROPDOWN_CreateIndirect, "Dropdown2", ID_DROPDOWN_1, 480, 180,

14 130, 25, 0, 0x0, 0 },

15 { TEXT_CreateIndirect, "", ID_TEXT_0, 315, 180, 130, 25, 0, 0x0, 0

16 },

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

在代码清单 21‑3中我们定义了4个ID：一个框架窗口ID、2个下拉框控件ID和一个文本控件ID。你也可以使用emWin预定义好的下拉框控件ID，但最多只有GUI_ID_DROPDOWN0到GUI_ID_DROPDOWN3，共4个ID可供使用。上述代码中的文本控件，我们用它来显示下拉框中的某个选项。

(2) 对话框回调函数

代码清单 24‑4 对话框回调函数（DropdownDLG.c文件）

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note pMsg：消息指针

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE \* pMsg)

8 {

9 WM_HWIN hItem;

10 int NCode;

11 int Id;

12 int value;

13 char Dropdown_buf[8] = {0};

14

15 switch (pMsg->MsgId) {

16 case WM_INIT_DIALOG:

17 /\* 初始化Framewin控件 \*/

18 hItem = pMsg->hWin;

19 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

20 FRAMEWIN_SetTitleHeight(hItem, 32);

21 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

22 /\* 初始化DROPDOWN0 \*/

23 hItem = WM_GetDialogItem(pMsg->hWin, ID_DROPDOWN_0);

24 DROPDOWN_SetListHeight(hItem, 100);

25 DROPDOWN_SetFont(hItem, GUI_FONT_24B_1);

26 DROPDOWN_AddString(hItem, "Item1-0");

27 DROPDOWN_AddString(hItem, "Item1-1");

28 DROPDOWN_AddString(hItem, "Item1-2");

29 DROPDOWN_AddString(hItem, "Item1-3");

30 DROPDOWN_AddString(hItem, "Item1-4");

31 DROPDOWN_AddString(hItem, "Item1-5");

32 DROPDOWN_AddString(hItem, "Item1-6");

33 DROPDOWN_AddString(hItem, "Item1-7");

34 DROPDOWN_SetAutoScroll(hItem, 1);

35 DROPDOWN_SetScrollbarWidth(hItem, 20);

36 /\* 初始化DROPDOWN1 \*/

37 hItem = WM_GetDialogItem(pMsg->hWin, ID_DROPDOWN_1);

38 DROPDOWN_SetListHeight(hItem, 100);

39 DROPDOWN_SetFont(hItem, GUI_FONT_24B_1);

40 DROPDOWN_AddString(hItem, "Item2-0");

41 DROPDOWN_AddString(hItem, "Item2-1");

42 DROPDOWN_AddString(hItem, "Item2-2");

43 DROPDOWN_AddString(hItem, "Item2-3");

44 DROPDOWN_AddString(hItem, "Item2-4");

45 DROPDOWN_AddString(hItem, "Item2-5");

46 DROPDOWN_AddString(hItem, "Item2-6");

47 DROPDOWN_AddString(hItem, "Item2-7");

48 DROPDOWN_SetAutoScroll(hItem, 1);

49 DROPDOWN_SetScrollbarWidth(hItem, 20);

50 DROPDOWN_SetUpMode(hItem, 1);

51 /\* 初始化TEXT \*/

52 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

53 TEXT_SetTextAlign(hItem, GUI_TA_HCENTER \| GUI_TA_VCENTER);

54 TEXT_SetFont(hItem, GUI_FONT_COMIC24B_ASCII);

55 break;

56 case WM_NOTIFY_PARENT:

57 Id = WM_GetId(pMsg->hWinSrc);

58 NCode = pMsg->Data.v;

59 switch (Id) {

60 case ID_DROPDOWN_0: // Notifications sent by 'Dropdown0'

61 switch (NCode) {

62 case WM_NOTIFICATION_CLICKED:

63 break;

64 case WM_NOTIFICATION_RELEASED:

65 break;

66 case WM_NOTIFICATION_SEL_CHANGED:

67 hItem = WM_GetDialogItem(pMsg->hWin, ID_DROPDOWN_0);

68 value = DROPDOWN_GetSel(hItem);

69 DROPDOWN_GetItemText(hItem, value, Dropdown_buf,

70 GUI_COUNTOF(Dropdown_buf));

71 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

72 TEXT_SetText(hItem, Dropdown_buf);

73 break;

74 }

75 break;

76 case ID_DROPDOWN_1: // Notifications sent by 'Dropdown1'

77 switch (NCode) {

78 case WM_NOTIFICATION_CLICKED:

79 break;

80 case WM_NOTIFICATION_RELEASED:

81 break;

82 case WM_NOTIFICATION_SEL_CHANGED:

83 hItem = WM_GetDialogItem(pMsg->hWin, ID_DROPDOWN_1);

84 value = DROPDOWN_GetSel(hItem);

85 DROPDOWN_GetItemText(hItem, value, Dropdown_buf,

86 GUI_COUNTOF(Dropdown_buf));

87 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

88 TEXT_SetText(hItem, Dropdown_buf);

89 break;

90 }

91 break;

92 }

93 break;

94 default:

95 WM_DefaultProc(pMsg);

96 break;

97 }

98 }

99}

1. WM_INIT_DIALOG消息

在代码清单 24‑4中设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

下拉框相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取下拉框的句柄。

成功获取句柄后，设置ID_DROPDOWN_0和ID_DROPDOWN_1展开状态的列表高度为100像素，设置字体大小为24，使能自动使用滚动条并设置滚动条的宽度为20，最后使用DROPDOWN_AddString函数为两个下拉框控件各添加8个选项。而文本控件ID_TEXT_0只设置文本对齐方式为垂
直水平居中及字体24。

2. WM_NOTIFY_PARENT消息

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。

下拉框当前选中选项更改后，会进入通知代码WM_NOTIFICATION_SEL_CHANGED。在此通知代码中使用DROPDOWN_GetSel函数获取当前被选中选项的索引号，接着根据索引号使用DROPDOWN_GetItemText函数返回对应的选项文本信息，然后通过文本控件显示在对话框中。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

下拉框控件基础实验的实验现象如图 24‑2和图 24‑3所示。

|Dropdo003|

图 24‑2 下拉框基础实验现象1

|Dropdo004|

图 24‑3 下拉框基础实验现象2

.. |Dropdo002| image:: media\Dropdo002.png
   :width: 1.24528in
   :height: 1.15833in
.. |Dropdo003| image:: media\Dropdo003.png
   :width: 5.76806in
   :height: 3.46228in
.. |Dropdo004| image:: media\Dropdo004.png
   :width: 5.76806in
   :height: 3.46228in
