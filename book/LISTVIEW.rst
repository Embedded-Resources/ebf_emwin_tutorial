.. vim: syntax=rst

表格控件
----

表格（LISTVIEW）控件类似我们平常接触到的execl表格，主要是用于多种类型数据的选择和管理。表格控件包含了一个HEADER控件，可以用来管理某一列数据。表格控件可以不需要父窗口单独创建，也可以作为框架窗口的子控件创建。在表格控件具有输入焦点时，当表格视图内的项目被选中，它们会高亮显示。

表格控件的外观如图 25‑1所示。

|LISTVI002|

图 25‑1 表格控件外观

表格控件支持5种通知代码，以区分各种不同的操作动作，见表格 25‑1。

表格 25‑1 表格控件支持的通知代码

============================== ========================================
通知代码                       描述
============================== ========================================
WM_NOTIFICATION_CLICKED        控件被点击
WM_NOTIFICATION_RELEASED       控件已被释放
WM_NOTIFICATION_MOVED_OUT      控件已被点击，但指针已被移出框且没有释放
WM_NOTIFICATION_SCROLL_CHANGED 可选滚动条的滚动位置已更改
WM_NOTIFICATION_SEL_CHANGED    列表框的选择已更改
============================== ========================================

这5种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击表格后，窗口管理器会让表格控件向父窗口发送WM_NOTIFY_PARENT消息，并且会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

同时表格控件也支持输入焦点，它可以接收如表格 25‑2所示的4种按键消息。

表格 25‑2 表格控件支持的按键消息

============= ======================================================
按键消息      描述
============= ======================================================
GUI_KEY_UP    向上移动选择栏
GUI_KEY_DOWN  向下移动选择栏
GUI_KEY_RIGHT 如果列宽的总和大于表格视图的内部区域，则内容向左滚动
GUI_KEY_LEFT  如果列宽的总和大于表格视图的内部区域，则内容滚动到右侧
============= ======================================================

创建表格控件
~~~~~~

表格控件API
^^^^^^^

表格 25‑3 表格控件API

============================== ==================================
函数名                         描述
============================== ==================================
LISTVIEW_AddColumn()           添加一列数据
LISTVIEW_AddRow()              添加一行数据
LISTVIEW_CompareDec()          比较功能，用于比较2个整数值
LISTVIEW_CompareText()         比较功能，用于比较2个字符串
LISTVIEW_CreateAttached()      附加表格控件到窗口
LISTVIEW_CreateEx()            创建一个表格控件
LISTVIEW_CreateIndirect()      从对话框资源表中创建表格控件
LISTVIEW_CreateUser()          使用额外的字节作为用户数据创建表格
LISTVIEW_DecSel()              减量选择
LISTVIEW_DeleteColumn()        删除给定的列
LISTVIEW_DeleteRow()           删除给定的行
LISTVIEW_DisableRow()          将给定行的状态设置为禁用
LISTVIEW_DisableSort()         禁用控件的排序
LISTVIEW_EnableCellSelect()    启用/禁用给定控件的单元格选择模式
LISTVIEW_EnableRow()           将给定行的状态设置为启用
LISTVIEW_EnableSort()          启用控件的排序
LISTVIEW_GetBkColor()          返回当前背景颜色
LISTVIEW_GetFont()             返回当前字体
LISTVIEW_GetHeader()           返回附加的HEADER控件的句柄
LISTVIEW_GetItemRect()         复制指定项目的矩形坐标
LISTVIEW_GetItemText()         将指定项目的文本复制到给定的缓冲区
LISTVIEW_GetItemTextLen()      返回给定单元格的字符长度
LISTVIEW_GetItemTextSorted()   将指定项目的文本复制到给定的缓冲区
LISTVIEW_GetNumColumns()       返回列数
LISTVIEW_GetNumRows()          返回行数
LISTVIEW_GetSel()              返回所选行的索引
LISTVIEW_GetSelCol()           返回所选列的索引
LISTVIEW_GetSelUnsorted()      返回未排序状态的选定行的索引
LISTVIEW_GetTextColor()        返回文本颜色
LISTVIEW_GetUserData()         检索用户数据集
LISTVIEW_GetUserDataRow()      返回给定行的用户数据
LISTVIEW_GetWrapMode()         返回当前用于换行的模式
LISTVIEW_IncSel()              增量选择
LISTVIEW_InsertRow()           在给定位置插入新行
LISTVIEW_OwnerDraw()           用于绘制控件单元格的默认函数
LISTVIEW_SetAutoScrollH()      启用自动使用水平滚动条
LISTVIEW_SetAutoScrollV()      启用自动使用垂直滚动条
LISTVIEW_SetBkColor()          设置背景色
LISTVIEW_SetColumnWidth()      设置列宽
LISTVIEW_SetCompareFunc()      设置给定列的比较功能
LISTVIEW_SetDefaultBkColor()   设置HEADER控件的默认背景颜色
LISTVIEW_SetDefaultFont()      设置HEADER控件的默认字体
LISTVIEW_SetDefaultGridColor() 设置HEADER控件的默认颜色
LISTVIEW_SetDefaultTextColor() 设置HEADER控件的网格线默认颜色
LISTVIEW_SetFixed()            固定给定的列数
LISTVIEW_SetFont()             设置字体
LISTVIEW_SetGridVis()          设置网格线的可见性标志
LISTVIEW_SetHeaderHeight()     设置页眉的高度
LISTVIEW_SetItemBitmap()       将位图设置为控件单元格的背景
LISTVIEW_SetItemBkColor()      设置单元格的背景色
LISTVIEW_SetItemText()         设置单元格文本
LISTVIEW_SetItemTextColor()    设置单元格文本颜色
LISTVIEW_SetLBorder()          设置用于左边框的像素数
LISTVIEW_SetOwnerDraw()        设置用于绘制单元格的自定义函数
LISTVIEW_SetRBorder()          设置用于右边框的像素数
LISTVIEW_SetRowHeight()        设置控件行高
LISTVIEW_SetSel()              设置当前选中的行
LISTVIEW_SetSelCol()           设置当前选定的列
LISTVIEW_SetSelUnsorted()      将当前选择设置为未排序状态
LISTVIEW_SetSort()             设置要排序的列和排序顺序
LISTVIEW_SetTextAlign()        设置列的文本对齐方式
LISTVIEW_SetTextColor()        设置文字颜色
LISTVIEW_SetUserData()         设置额外用户数据集
LISTVIEW_SetUserDataRow()      设置给定行的用户数据
LISTVIEW_SetWrapMode()         设置给定控件的换行模式
============================== ==================================

表格控件创建函数
^^^^^^^^

LISTVIEW_CreateEx()
'''''''''''''''''''

在指定位置创建指定大小的表格控件。

代码清单 25‑1 函数原型

1 LISTVIEW_Handle LISTVIEW_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id);

1) x0：表格控件在父坐标中的最左侧像素；

2) y0：表格控件在父坐标中的最顶侧像素；

3) xSize：表格的水平尺寸，以像素为单位；

4) ySize：表格的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以表格控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：预留，未使用；

8) Id：控件ID号。

返回值：创建成功后返回已创建的表格控件句柄，创建失败则返回0。

LISTVIEW_CreateIndirect()
'''''''''''''''''''''''''

从对话框资源表中创建滑块控件。

代码清单 25‑2 函数原型

1 LISTVIEW_Handle LISTVIEW_CreateIndirect(const GUI_WIDGET_CREATE_INFO

2 \*pCreateInfo, WM_HWIN

3 hWinParent, int x0, int y0,

4 WM_CALLBACK \*cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：表格控件在父坐标中的最左边像素；

4) y0：表格控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的para参数，其中的Flags参数的可选值与LISTVIEW_CreateEx()函数的ExFlags参数相同，但是LISTVIEW_CreateEx()函数的ExFlags参数目前并没有被使用。

表格控件基础实验
~~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个表格控件，通过API函数直接创建的实验可参考官方例程WIDGET_ListView.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\WIDGET_ListView.c

代码分析
^^^^

(1) 创建对话框

代码清单 25‑3 创建对话框（ListviewDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_LISTVIEW_0 (GUI_ID_USER + 0x01)

4 #define ID_BUTTON_0 (GUI_ID_USER + 0x02)

5 #define ID_BUTTON_1 (GUI_ID_USER + 0x03)

6 #define ID_BUTTON_2 (GUI_ID_USER + 0x04)

7 #define ID_BUTTON_3 (GUI_ID_USER + 0x05)

8 #define ID_TEXT_0 (GUI_ID_USER + 0x06)

9 #define ID_TEXT_1 (GUI_ID_USER + 0x07)

10 #define ID_TEXT_2 (GUI_ID_USER + 0x08)

11 #define ID_TEXT_3 (GUI_ID_USER + 0x09)

12

13 /\* 资源表 \*/

14 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

15 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

16 480, 0, 0x0, 0 },

17 { LISTVIEW_CreateIndirect, "Listview", ID_LISTVIEW_0, 200, 15, 400,

18 190, 0, 0x0, 0 },

19 { BUTTON_CreateIndirect, "Button0", ID_BUTTON_0, 199, 215, 95, 30,

20 0, 0x0, 0 },

21 { BUTTON_CreateIndirect, "Button1", ID_BUTTON_1, 302, 215, 95, 30,

22 0, 0x0, 0 },

23 { BUTTON_CreateIndirect, "Button2", ID_BUTTON_2, 404, 215, 95, 30,

24 0, 0x0, 0 },

25 { BUTTON_CreateIndirect, "Button3", ID_BUTTON_3, 506, 215, 95, 30,

26 0, 0x0, 0 },

27 { TEXT_CreateIndirect, "Text0", ID_TEXT_0, 200, 255, 400, 32, 0,

28 0x64, 0 },

29 { TEXT_CreateIndirect, "Text1", ID_TEXT_1, 200, 290, 400, 32, 0,

30 0x64, 0 },

31 { TEXT_CreateIndirect, "Text2", ID_TEXT_2, 200, 325, 400, 32, 0,

32 0x64, 0 },

33 { TEXT_CreateIndirect, "Text3", ID_TEXT_3, 200, 360, 400, 32, 0,

34 0x64, 0 },

35 };

36

37 /*\*

38 \* @brief 以对话框方式间接创建控件

39 \* @note 无

40 \* @param 无

41 \* @retval hWin：资源表中第一个控件的句柄

42 \*/

43 WM_HWIN CreateFramewin(void)

44 {

45 WM_HWIN hWin;

46

47 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

48 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

49 return hWin;

50 }

在代码清单 25‑3中我们定义了10个ID：1个框架窗口ID、1个表格控件ID、4个按钮控件和4个文本控件，其中按钮控件用于增加和删除表格中的行列数据，文本控件用来显示某一行表格的数据。当然也可以使用emWin预定义好的表格控件ID，但最多只有GUI_ID_LISTVIEW0到GUI_ID_LIST
VIEW3，共4个ID可供使用。

(2) 对话框回调函数

1. WM_INIT_DIALOG消息

本实验的对话框回调函数代码较多，我们把回调函数按不同的消息分开来分析。首先是WM_INIT_DIALOG消息，见代码清单 25‑4。

代码清单 25‑4 WM_INIT_DIALOG消息（ListviewDLG.c文件）

1 /\* 代填入表格控件的数据 \*/

2 static const char \* \_Table[][4] = {

3 { "A00", "Item AAA", "123-A", "378" },

4 { "A01", "Item BBB", "123-B", "308" },

5 { "A02", "Item CCC", "123-C", "344" },

6 { "A03", "Item DDD", "123-D", "451" },

7 { "A04", "Item EEE", "123-E", "364" },

8 { "A05", "Item FFF", "123-F", "194" },

9 { "A06", "Item GGG", "123-G", "774" },

10 { "A07", "Item HHH", "123-H", "339" }

11 };

12 /\* WM_INIT_DIALOG消息 \*/

13 case WM_INIT_DIALOG:

14 {

15 /\* 初始化Framewin控件 \*/

16 hItem = pMsg->hWin;

17 FRAMEWIN_SetTitleHeight(hItem, 32);

18 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

19 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

20 /\* 初始化Listview控件 \*/

21 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

22 LISTVIEW_SetHeaderHeight(hItem, 20);

23 LISTVIEW_AddColumn(hItem, 70, "Col 0", GUI_TA_HCENTER \|

24 GUI_TA_VCENTER);

25 LISTVIEW_AddColumn(hItem, 70, "Col 1", GUI_TA_HCENTER \|

26 GUI_TA_VCENTER);

27 LISTVIEW_AddColumn(hItem, 70, "Col 2", GUI_TA_HCENTER \|

28 GUI_TA_VCENTER);

29 LISTVIEW_AddColumn(hItem, 70, "Col 3", GUI_TA_HCENTER \|

30 GUI_TA_VCENTER);

31 for (i = 0; i < GUI_COUNTOF(_Table); i++) {

32 LISTVIEW_AddRow(hItem, \_Table[i]);/\* 添加数据 \*/

33 }

34 LISTVIEW_SetGridVis(hItem, 1);

35 LISTVIEW_SetFont(hItem, GUI_FONT_16_ASCII);

36 LISTVIEW_SetAutoScrollH(hItem, 1);

37 LISTVIEW_SetAutoScrollV(hItem, 1);

38

39 /\* 初始化Button0 \*/

40 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_0);

41 BUTTON_SetText(hItem, "Add Row");

42 BUTTON_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

43 /\* 初始化Button1 \*/

44 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_1);

45 BUTTON_SetText(hItem, "Del Row");

46 BUTTON_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

47 /\* 初始化Button2 \*/

48 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_2);

49 BUTTON_SetText(hItem, "Add Column");

50 BUTTON_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

51 /\* 初始化Button3 \*/

52 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_3);

53 BUTTON_SetText(hItem, "Del Column");

54 BUTTON_SetFont(hItem, GUI_FONT_COMIC18B_ASCII);

55 /\* 初始化Text0 \*/

56 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

57 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

58 TEXT_SetText(hItem, "Col 0: ");

59 TEXT_SetFont(hItem, GUI_FONT_24_ASCII);

60 /\* 初始化Text1 \*/

61 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_1);

62 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

63 TEXT_SetText(hItem, "Col 1: ");

64 TEXT_SetFont(hItem, GUI_FONT_24_ASCII);

65 /\* 初始化Text2 \*/

66 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_2);

67 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

68 TEXT_SetText(hItem, "Col 2: ");

69 TEXT_SetFont(hItem, GUI_FONT_24_ASCII);

70 /\* 初始化Text3 \*/

71 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_3);

72 TEXT_SetTextAlign(hItem, GUI_TA_LEFT \| GUI_TA_VCENTER);

73 TEXT_SetText(hItem, "Col 3: ");

74 TEXT_SetFont(hItem, GUI_FONT_24_ASCII);

75 break;

76 }

在代码清单 25‑4中，首先需要定义一个二维数组，用来存放准备添加到表格控件中的字符串文本。设置框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

表格控件相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取表格控件的句柄。成功获取到句柄后可以开始进一步设置表格控件。

我们重点关注代码清单 25‑4中表格控件的设置，其他控件的初始化设置在之前的章节已经介绍过就不再赘述了。

首先使用LISTVIEW_SetHeaderHeight函数设置表格控件的首行高度为20像素。然后使用LISTVIEW_AddColumn函数添加4列，列宽度为70像素，列中的文本对齐方式是水平和垂直居中对齐。用一个for循环调用LISTVIEW_AddRow函数将二维数组中的文本信息添加到表格控件
中，其实表格控件中的文本不仅限于使用数组添加，还可以使用其他方式添加。接下来LISTVIEW_SetGridVis函数开启网格线，设置表格中的文本字体为16，并且启用水平和垂直两个方向的自动滚动条。

2. WM_NOTIFY_PARENT消息

代码清单 25‑5 WM_NOTIFY_PARENT消息（ListviewDLG.c文件）

1 /\* 表格数据缓冲区 \*/

2 typedef struct {

3 char Col0[10];

4 char Col1[10];

5 char Col2[10];

6 char Col3[10];

7 } \_ListviewItem;

8 \_ListviewItem ListviewItem;

9

10 int Listview_RowNum;

11

12 /*WM_NOTIFY_PARENT消息 \*/

13 case WM_NOTIFY_PARENT:

14 {

15 Id = WM_GetId(pMsg->hWinSrc);

16 NCode = pMsg->Data.v;

17 switch (Id) {

18 case ID_LISTVIEW_0: // Notifications sent by 'Listview'

19 switch (NCode) {

20 case WM_NOTIFICATION_CLICKED:

21 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

22 Listview_RowNum = LISTVIEW_GetSel(hItem);

23 LISTVIEW_GetItemText(hItem, 0, Listview_RowNum,

24 ListviewItem.Col0, 10);

25 LISTVIEW_GetItemText(hItem, 1, Listview_RowNum,

26 ListviewItem.Col1, 10);

27 LISTVIEW_GetItemText(hItem, 2, Listview_RowNum,

28 ListviewItem.Col2, 10);

29 LISTVIEW_GetItemText(hItem, 3, Listview_RowNum,

30 ListviewItem.Col3, 10);

31 break;

32 case WM_NOTIFICATION_RELEASED:

33 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

34 sprintf(buf, "Col 0: %s", ListviewItem.Col0);

35 TEXT_SetText(hItem, buf);

36 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_1);

37 sprintf(buf, "Col 1: %s", ListviewItem.Col1);

38 TEXT_SetText(hItem, buf);

39 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_2);

40 sprintf(buf, "Col 2: %s", ListviewItem.Col2);

41 TEXT_SetText(hItem, buf);

42 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_3);

43 sprintf(buf, "Col 3: %s", ListviewItem.Col3);

44 TEXT_SetText(hItem, buf);

45 break;

46 case WM_NOTIFICATION_SEL_CHANGED:

47 break;

48 }

49 break;

50 case ID_BUTTON_0: // Notifications sent by 'Add Row'

51 switch (NCode) {

52 case WM_NOTIFICATION_CLICKED:

53 break;

54 case WM_NOTIFICATION_RELEASED:

55 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

56 LISTVIEW_AddRow(hItem, \_Table[7]);

57 break;

58 }

59 break;

60 case ID_BUTTON_1: // Notifications sent by 'Del Row'

61 switch (NCode) {

62 case WM_NOTIFICATION_CLICKED:

63 break;

64 case WM_NOTIFICATION_RELEASED:

65 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

66 listview_RowIndex = LISTVIEW_GetNumRows(hItem);

67 if (listview_RowIndex == 1) {

68 break;

69 }

70 listview_RowIndex = listview_RowIndex - 1;

71 LISTVIEW_DeleteRow(hItem, listview_RowIndex);

72 break;

73 }

74 break;

75 case ID_BUTTON_2: // Notifications sent by 'Add column'

76 switch (NCode) {

77 case WM_NOTIFICATION_CLICKED:

78 break;

79 case WM_NOTIFICATION_RELEASED:

80 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

81 listview_ColumnIndex = LISTVIEW_GetNumColumns(hItem);

82 sprintf(buf, "Col %d", listview_ColumnIndex);

83 LISTVIEW_AddColumn(hItem, 40, buf, GUI_TA_HCENTER \|

84 GUI_TA_VCENTER);

85 break;

86 }

87 break;

88 case ID_BUTTON_3: // Notifications sent by 'Del column'

89 switch (NCode) {

90 case WM_NOTIFICATION_CLICKED:

91 break;

92 case WM_NOTIFICATION_RELEASED:

93 hItem = WM_GetDialogItem(pMsg->hWin, ID_LISTVIEW_0);

94 listview_ColumnIndex = LISTVIEW_GetNumColumns(hItem);

95 if (listview_ColumnIndex == 1) {

96 break;

97 }

98 listview_ColumnIndex = listview_ColumnIndex - 1;

99 LISTVIEW_DeleteColumn(hItem, listview_ColumnIndex);

100 break;

101 }

102 break;

103 }

104 break;

105 }

代码清单 25‑5首先定义了1个结构体ListviewItem，用来存放从表格控件中读取的某一行的文本信息。

上述代码中的表格控件ID_LISTVIEW_0用到了2种通知代码，首先来看通知代码WM_NOTIFICATION_CLICKED，当表格控件被点击时，使用LISTVIEW_GetSel函数获取当前被点击的行的索引值，然后LISTVIEW_GetItemText函数根据索引值去除对应行的文本信息，存放
在ListviewItem中。接下来是通知代码WM_NOTIFICATION_RELEASED，在这个通知代码中把刚刚获取到的文本信息通过TEXT_SetText函数送到屏幕上。

ID_BUTTON_0负责向表格控件中添加行数据，在被点击并释放后执行LISTVIEW_AddRow函数添加数据。

ID_BUTTON_1负责删除表格控件中的某一行，在被点击并释放后使用LISTVIEW_GetNumRows函数获取总行数，然后LISTVIEW_DeleteRow函数删除最后一行。

ID_BUTTON_2向表格控件新增列，被点击并释放后执行LISTVIEW_GetNumColumns获取当前列数，然后使用LISTVIEW_AddColumn函数添加新列。

ID_BUTTON_2负责删除表格控件中的某一列，在被点击并释放后使用LISTVIEW_GetNumColumns函数获取当前的总列数，然后LISTVIEW_DeleteColumn函数删除最后一列。

3. 其他消息

代码清单 25‑6 default消息（ListviewDLG.c文件）

1 default:

2 WM_DefaultProc(pMsg);

3 break;

如代码清单 25‑6所示，所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实验现象
^^^^

表格控件基础实验的实验现象如图 25‑2和图 25‑3所示，点击表格控件中的某一行会在下发显示当前行的文本信息，点击“Add Row”按钮可以向表格中增加新的一行数据，“Del Row”按钮可以删除表格的最后一行，“Add Column”按钮可以增加新的一列，“Del
Column”按钮可以删除表格的最后一列。

|LISTVI003|

图 25‑2 表格控件基础实验初始状态

|LISTVI004|

图 25‑3 添加若干行和列之后的状态

.. |LISTVI002| image:: media\LISTVI002.png
   :width: 3.10378in
   :height: 0.90614in
.. |LISTVI003| image:: media\LISTVI003.png
   :width: 5.76806in
   :height: 3.46228in
.. |LISTVI004| image:: media\LISTVI004.png
   :width: 5.76806in
   :height: 3.46228in
