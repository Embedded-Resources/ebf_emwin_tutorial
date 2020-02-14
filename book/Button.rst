.. vim: syntax=rst

按钮控件
----

emWin提供了三种按键式的控件，分别是：按钮（Button）、复选框（Checkbox）和单选按钮（Radio button）。本章我们先学习使用按钮控件。

按钮控件主要用于触屏设备的交互，可以说是用户最重要的界面元素之一。按钮控件具有输入焦点，所以也可以响应GUI_KEY_SPACE和GUI_KEY_ENTER键盘消息。按钮中可以显示文本，也可以用位图贴图，甚至可以更改按钮形状。

emWin中的按钮样式如图 18‑1所示。

|Button002|

图 18‑1 不同效果的按钮样式

图 18‑1中左侧是不带皮肤的经典按钮样式，右侧是V5.28版本以后自带皮肤效果的按钮样式。

按钮控件支持三种通知代码，以区分不同的按钮动作。如表格 18‑1所示。

表格 18‑1 按钮支持的通知代码

========================= ========================================
通知代码                  描述
========================= ========================================
WM_NOTIFICATION_CLICKED   按钮已被点击
WM_NOTIFICATION_RELEASED  按钮已被释放
WM_NOTIFICATION_MOVED_OUT 按钮已被按下，且从按钮控件中移出而未释放
========================= ========================================

这三种通知代码是作为WM_NOTIFY_PARENT消息的一部分发送到其父窗口的。当用户点击按钮后，窗口管理器向父窗口发送WM_NOTIFY_PARENT消息时，会在消息结构的Data.v成员中附加相应的通知代码，用户程序可以读取此成员来检测不同的按钮动作以及实现各种功能。

按钮控件支持输入焦点，支持键盘或其他类似键盘的外部输入设备对按钮的控制。如果按钮控件已被聚焦，那它可以接收表格 18‑2中的按键消息。

表格 18‑2 按钮控件支持的键盘消息

============= ==================================================
按键          描述
============= ==================================================
GUI_KEY_ENTER 按下此键，按钮控件会被按下并立即释放，然后作出反应
GUI_KEY_SPACE 按下此键，按钮控件状态变为按下状态

              按下此键，按钮控件状态变为释放状态 ============= ==================================================

创建按钮控件
~~~~~~

按钮控件API
^^^^^^^

按钮控件的 API 函数较多，这里我们只列出一些比较常用的，见表格 18‑3。完整的API函数请查阅emWin官方手册。

表格 18‑3 按钮API函数

============================ ================================
函数名                       描述
============================ ================================
BUTTON_CreateEx()            创建一个按钮控件
BUTTON_CreateIndirect()      从对话框资源表中创建按钮控件
BUTTON_CreateUser()          使用额外字节作为用户数据创建按钮
BUTTON_GetBitmap()           返回按钮贴图的指针
BUTTON_GetBkColor()          返回按钮的背景颜色
BUTTON_GetFont()             返回按钮的字体
BUTTON_GetText ()            检索指定按钮的文本
BUTTON_GetTextAlign()        返回按钮的文本格式
BUTTON_GetUserData()         检索指定按钮的用户数据
BUTTON_IsPressed()           返回按钮是否被按下
BUTTON_SetBitmap()           设置按钮在使用时的位图贴图
BUTTON_SetBkColor()          设置按钮的背景颜色
BUTTON_SetFocusColor()       设置按钮被聚焦时的颜色
BUTTON_SetFocusable()        设置是否使用输入聚焦功能
BUTTON_SetFont()             设置按钮字体
BUTTON_SetFrameColor()       设置按钮边框颜色
BUTTON_SetPressed()          设置按钮的状态为按下或释放
BUTTON_SetStreamedBitmapEx() 设置按钮在使用时的流位图贴图
BUTTON_SetText()             设置按钮文本
BUTTON_SetTextAlign()        设置按钮文本格式
BUTTON_SetTextColor()        设置按钮文本颜色
BUTTON_SetTextOffset()       调整按钮文本位置，考虑对齐情况
BUTTON_SetUserData()         设置按钮的额外字节
============================ ================================

按钮控件创建函数
^^^^^^^^

BUTTON_CreateEx()
'''''''''''''''''

在指定位置创建指定大小的按钮控件。

代码清单 18‑1 按钮创建函数

1 BUTTON_Handle BUTTON_CreateEx(int x0, int y0, int xSize, int ySize,

2 WM_HWIN hParent, int WinFlags, int

3 ExFlags, int Id);

1) x0：按钮控件在父坐标中的最左侧像素；

2) y0：按钮控件在父坐标中的最顶侧像素；

3) xSize：按钮的水平尺寸，以像素为单位；

4) ySize：按钮的垂直尺寸，以像素为单位；

5) hParent：父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：窗口创建标志。由于控件本质上是窗口，所以按钮控件在创建时可以使用几乎所有的窗口创建标志。这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：预留，未使用；

8) 控件ID号。

返回值：创建成功后返回已创建的按钮控件句柄，创建失败则返回0。

BUTTON_CreateIndirect()
'''''''''''''''''''''''

代码清单 18‑2 对话框方式创建按钮函数

1 BUTTON_Handle BUTTON_CreateIndirect(const GUI_WIDGET_CREATE_INFO \*

2 pCreateInfo, WM_HWIN hWinParent,

3 int x0, int y0, WM_CALLBACK \* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：按钮控件在父坐标中的最左边像素；

4) y0：按钮控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

此函数的第一个参数指向的是代码清单 15‑3的资源表结构，但不使用其中的Flag参数和para参数。

按钮控件基础实验
~~~~~~~~

接下来我们来讲解如何以对话框的方式间接创建一个按钮控件，通过API函数直接创建的实验可参考官方例程WIDGET_ButtonSimple.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\WIDGET_ButtonSimple.c

代码分析
^^^^

(1) 创建对话框

代码清单 18‑3 创建对话框（ButtonDLG.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_BUTTON_0 (GUI_ID_USER + 0x01)

4 #define ID_BUTTON_1 (GUI_ID_USER + 0x02)

5 #define ID_BUTTON_2 (GUI_ID_USER + 0x03)

6

7 /\* 资源表 \*/

8 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

9 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

10 480, 0, 0x0, 0 },

11 { BUTTON_CreateIndirect, "Button0", ID_BUTTON_0, 10, 30, 160, 48,

12 0, 0x0, 0 },

13 { BUTTON_CreateIndirect, "Button1", ID_BUTTON_1, 10, 138, 160, 48,

14 0, 0x0, 0 },

15 { BUTTON_CreateIndirect, "Button2", ID_BUTTON_2, 50, 246, 80, 80,

16 0, 0x0, 0 },

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

如代码清单 18‑3所示，创建资源表和步骤和框架窗口实验一样，首先需要定义控件ID。在代码清单 18‑3中我们按照先后顺序定义了四个ID：一个框架窗口ID和三个按钮控件ID。

接着定义一个GUI_WIDGET_CREATE_INFO类型的资源表数组，其中包含一个框架窗口控件和两个按钮控件。需要注意的是，和上一章的框架窗口一样，以对话框方式创建的按钮控件无法在资源表中设置类似WM_CF_MEMDEV这样的窗口创建标志，因为资源表结构中并没有和窗口创建标志等效的参数。如果需要
使用窗口创建标志，可以尝试调用WM_SetCreateFlags函数来设置。

三个按钮控件的资源表参数设置除了拥有自己的起始坐标和尺寸和没有用到ExFlags参数和Para参数以外，与框架窗口资源表几乎是相同的。这里需要说明的是，按钮控件的起始坐标是相对于对话框（这里是框架窗口控件）的客户窗口的。

资源表定义完成后，调用GUI_CreateDialogBox函数创建一个非阻塞式对话框，通过GUI_COUNTOF可以得到资源表数组的元素个数，_cbDialog是对话框回调函数，在其中初始化控件以及处理子控件的消息。这个对话框以桌面窗口作为它的父窗口。

创建成功后GUI_CreateDialogBox函数会返回资源表中第一个控件的句柄，此时屏幕上并不会显示对话框，只有在下一次刷新屏幕时才会显示出来。

(2) 对话框回调函数

对话框创建成功之后还不够，还需要编写一个用来初始化控件和处理子控件消息的对话框回调函数，见代码清单 18‑4。

代码清单 18‑4 对话框回调函数（ButtonDLG.c文件）

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE \*pMsg)

8 {

9 WM_HWIN hItem;

10 int NCode;

11 int Id;

12 static U8 button_3_flag = 0, button_4_flag = 0;

13

14 switch (pMsg->MsgId) {

15 case WM_INIT_DIALOG:

16 /\* 初始化Framewin控件 \*/

17 hItem = pMsg->hWin;

18 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

19 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

20 FRAMEWIN_SetTitleHeight(hItem, 32);

21 /\* 初始化Button0 \*/

22 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_0);

23 BUTTON_SetFont(hItem, GUI_FONT_20B_1);

24 /\* 初始化Button1 \*/

25 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_1);

26 WM_SetCallback(hItem, \_cbButton1);

27 /\* 初始化Button2 \*/

28 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_2);

29 WM_SetCallback(hItem, \_cbButton2);

30 break;

31 case WM_NOTIFY_PARENT:

32 Id = WM_GetId(pMsg->hWinSrc);

33 NCode = pMsg->Data.v;

34 switch (Id) {

35 case ID_BUTTON_0: // Notifications sent by 'Button0'

36 switch (NCode) {

37 case WM_NOTIFICATION_CLICKED:

38 break;

39 case WM_NOTIFICATION_RELEASED:

40 LED1_TOGGLE;

41 break;

42 }

43 break;

44 case ID_BUTTON_1: // Notifications sent by 'Button1'

45 switch (NCode) {

46 case WM_NOTIFICATION_CLICKED:

47 break;

48 case WM_NOTIFICATION_RELEASED:

49 LED2_TOGGLE;

50 break;

51 }

52 break;

53 case ID_BUTTON_2: // Notifications sent by 'Button2'

54 switch (NCode) {

55 case WM_NOTIFICATION_CLICKED:

56 BEEP_ON;

57 break;

58 case WM_NOTIFICATION_RELEASED:

59 BEEP_OFF;

60 break;

61 }

62 break;

63 }

64 break;

65 default:

66 WM_DefaultProc(pMsg);

67 break;

68 }

69 }

1. WM_INIT_DIALOG消息

如代码清单 18‑4所示，首先从消息结构中获取框架窗口的句柄，为初始化窗口做准备。成功获取框架窗口后设置标题栏文本为“STemWIN@EmbedFire STM32F429”，设置框架窗口的标题栏高度为32像素，字体大小32像素。

按钮的相关API函数几乎都是使用句柄来操作的，但在建立对话框资源表的时候并没有定义它的句柄，那就需要通过WM_GetDialogItem函数来自动建立并获取按钮的句柄。不光是按钮控件，所有对话框中的子控件全部都得使用此函数获取自己的句柄。我们将Button0保持系统默认的样式，设置按钮文本的字体大小
为20B，其余基本参数在资源表中已经配好了，不需要额外的初始化配置。同时使用修改控件回调函数的方式自定义Button1和button2的外观，用WM_SetCallback函数进行重定向。

2. WM_NOTIFY_PARENT消息

这个消息是对话框回调函数的重点，所有对话框子控件的具体行为逻辑都在此消息中设置和处理。在此消息中以控件ID来区分各个不同的控件。使用WM_GetId函数从消息结构中提取出发送此条消息的源控件ID号，并从消息结构的Data.v成员参数中提取通知代码。然后使用switch语句嵌套的方式判断当前是哪个控件
发送的消息以及附加的通知代码类型。

在代码清单 18‑4中，通过判断按钮的通知代码可以知道按钮当前是已被按下还是已被释放。我们让Button0在被释放的时候反转LED1，Button1被释放时反转LED2，Button2被按下时开启蜂鸣器，被释放时关闭蜂鸣器。这样便实现了一种简单的通过emWin按钮控件控制外设的功能。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(3) 按钮自定义回调函数代码分析

按钮控件本质上也是窗口，所以其内部也是有默认的回调函数的，通过WM_SetCallback函数可以把按钮回调函数重定向为自定义回调，这样就能实现各种各样的按钮效果。自定义回调函数见代码清单 18‑5。

代码清单 18‑5 按钮自定义回调函数

1 /*\*

2 \* @brief Button1控件回调函数

3 \* @note 无

4 \* @param pMsg：消息指针：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbButton1(WM_MESSAGE \*pMsg)

8 {

9 GUI_RECT Rect;

10

11 switch (pMsg->MsgId) {

12 case WM_PAINT:

13 /\* 获取客户区坐标 \*/

14 WM_GetClientRect(&Rect);

15 /\* 判断按钮是否被按下 \*/

16 if (BUTTON_IsPressed(pMsg->hWin)) {

17 /\* 绘制按下状态的按钮图形 \*/

18 GUI_SetColor(GUI_GRAY_C8);

19 GUI_FillRoundedRect(Rect.x0, Rect.y0, Rect.x1, Rect.y1, 5);

20 GUI_SetBkColor(GUI_GRAY_C8);

21 GUI_SetColor(0xA0ECECEC);

22 GUI_DrawRoundedRect(Rect.x0, Rect.y0, Rect.x1, Rect.y1, 5);

23 } else {

24 /\* 绘制释放状态的按钮图形 \*/

25 GUI_SetColor(GUI_WHITE);

26 GUI_FillRoundedRect(Rect.x0, Rect.y0, Rect.x1, Rect.y1, 5);

27 GUI_SetBkColor(GUI_WHITE);

28 GUI_SetColor(0xA0ECECEC);

29 GUI_DrawRoundedRect(Rect.x0, Rect.y0, Rect.x1, Rect.y1, 5);

30 }

31 /\* 绘制按钮文本 \*/

32 GUI_SetColor(GUI_BLACK);

33 GUI_SetFont(&GUI_Font20B_ASCII);

34 GUI_DispStringInRect("Button1", &Rect, GUI_TA_HCENTER \|

35 GUI_TA_VCENTER);

36 break;

37 default:

38 BUTTON_Callback(pMsg);

39 }

40 }

41

42 /*\*

43 \* @brief Button2控件回调函数

44 \* @note 无

45 \* @param pMsg：消息指针：消息指针

46 \* @retval 无

47 \*/

48 static void \_cbButton2(WM_MESSAGE \*pMsg)

49 {

50 GUI_RECT Rect;

51

52 switch (pMsg->MsgId) {

53 case WM_PAINT:

54 /\* 获取客户区坐标 \*/

55 WM_GetClientRect(&Rect);

56 /\* 判断按钮是否被按下 \*/

57 if (BUTTON_IsPressed(pMsg->hWin)) {

58 /\* 绘制按下状态的按钮图形 \*/

59 GUI_SetColor(GUI_RED);

60 GUI_SetBkColor(GUI_RED);

61 GUI_FillEllipse(Rect.x1 / 2, Rect.y1 / 2, Rect.x1 / 2,

62 Rect.y1 / 2);

63

64 GUI_SetColor(GUI_BLACK);

65 GUI_DrawEllipse(Rect.x1 / 2, Rect.y1 / 2, Rect.x1 / 2,

66 Rect.y1 / 2);

67 } else {

68 /\* 绘制释放状态的按钮图形 \*/

69 GUI_SetColor(GUI_BLUE);

70 GUI_SetBkColor(GUI_BLUE);

71 GUI_FillEllipse(Rect.x1 / 2, Rect.y1 / 2, Rect.x1 / 2,

72 Rect.y1 / 2);

73

74 GUI_SetColor(GUI_BLACK);

75 GUI_DrawEllipse(Rect.x1 / 2, Rect.y1 / 2, Rect.x1 / 2,

76 Rect.y1 / 2);

77 }

78 /\* 绘制按钮文本 \*/

79 GUI_SetFont(&GUI_Font20B_ASCII);

80 GUI_SetColor(GUI_WHITE);

81 GUI_DispStringInRect("Button2", &Rect, GUI_TA_HCENTER \|

82 GUI_TA_VCENTER);

83 break;

84 default:

85 BUTTON_Callback(pMsg);

86 }

87 }

在代码清单 18‑5中，我们给Button1和Button2分别自定义了各自的回调函数，实现圆角矩形和圆形两种不同的按钮样式。在自定义回调函数中定义了一个GUI_RECT类型的变量Rect，用来存放按钮客户区的坐标，以便之后进行绘图操作。

1. WM_PAINT消息

既然是自定义按钮样式，那就得在WM_PAINT消息内对按钮进行重新绘制。在自定义回调函数的WM_PAINT消息中，首先通过WM_GetClientRect函数获取按钮客户区的左上点坐标和右下点坐标，这个坐标实际上是从对话框资源表中设置的按钮大小得来的，然后通过BUTTON_IsPressed函数判断
按钮是被按下还是被释放，为1时按钮被按下，为0时按钮被释放，接着根据不同的按钮状态绘制对应不同状态的外观样式。

代码清单 18‑5中需要说明的一点是，因为获取到的坐标参数不是圆的坐标参数，所以无法使用GUI_DrawCircle函数来画圆，只能使用椭圆绘制函数GUI_DrawlEllipse进行绘制。

2. default消息

除了 WM_PAINT 消息以外，其它没用上的所有消息都需要还给按钮控件默认的回调函数BUTTON_Callback进行处理，否则整个按钮功能就得乱套了。

实验现象
^^^^

经过上述实验操作，我们得到了三个具有不同功能的按钮，同时其中两个不同于emWin默认样式。实际实验效果如图 18‑2所示。

|Button003|

图 18‑2 按钮控件基础实验结果

按钮贴图实验
~~~~~~

看完上面的基础实验，可能有人就会问了：如果我想自定义的按钮样式太复杂，直接绘制的实现效果不够理想甚至根本实现不了怎么办？没关系，emWin还提供了另外的解决办法，那就是直接给按钮控件贴图。其实除了按钮控件，其他很多的控件都可以贴图，而且贴图的实现方式有好几种。本小节先讲解按钮控件最基础的贴图方法，其
他控件的基础贴图会放到相应章节讲解。

按钮可以使用位图或者流位图来贴图，本实验我们以位图为例演示如何为按钮控件贴图。实验使用如图 18‑3所示的两种贴图，左边是按钮被关闭时的效果，右边是按钮被打开时的效果。

|Button004|

图 18‑3 实验选用的贴图

这些贴图的C文件制作步骤实际上跟显示位图章节中讲解的几乎是一样的，在此就不再赘述了。唯一的差别就是在保存格式的时候选择HighColor(565)而不是TrueColor，因为按钮控件的贴图一般不需要那么丰富的色彩，而且过高的色彩深度反而会给系统造成负担，可能导致系统流畅度降低。

.. _代码分析-1:

代码分析
^^^^

本实验的功能相对比较简单，主要是在按钮控件基础实验的代码上新增两个按钮，并在这两个按钮控件上进行位图贴图。以下代码分析均隐藏了部分和本次实验无关的代码。完整代码请参考本章相关例程。

(1) 创建对话框

代码清单 18‑6 创建对话框

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

**3 #define ID_BUTTON_3 (GUI_ID_USER + 0x04)**

4

5 /\* 资源表 \*/

6 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

7 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

8 480, 0, 0x0, 0 },

**9 { BUTTON_CreateIndirect, "", ID_BUTTON_3, 244, 30, 165, 48, 0, 0x0,**

**10 0 },**

11 };

12

13 /\* 声明位图文件 \*/

14 extern GUI_CONST_STORAGE GUI_BITMAP bmBUTTON_OFF;

15 extern GUI_CONST_STORAGE GUI_BITMAP bmBUTTON_ON;

16

17 /*\*

18 \* @brief 以对话框方式间接创建控件

19 \* @note 无

20 \* @param 无

21 \* @retval hWin：资源表中第一个控件的句柄

22 \*/

23 WM_HWIN CreateFramewin(void)

24 {

25 WM_HWIN hWin;

26

27 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

28 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

29 return hWin;

30 }

在代码清单 18‑6粗体部分中，增加了ID_BUTTON_3按钮ID，并在对话框资源表中添加Button3的相关基础属性，因为要贴图，所以按钮上的文本信息就不需要填了。不过被贴图的按钮的尺寸必须大于等于贴图本身的尺寸，否则贴图显示不正常。接着使用extern关键字声明存放在其他文件中的贴图，被声明的
贴图结构如代码清单 18‑7所示，由于篇幅限制就不列出贴图数据了。

代码清单 18‑7 按钮贴图数据结构（BUTTON_Bitmap.c文件）

1 GUI_CONST_STORAGE GUI_BITMAP bmBUTTON_OFF = {

2 165, // xSize

3 48, // ySize

4 330, // BytesPerLine

5 16, // BitsPerPixel

6 (unsigned char \*)_acBUTTON_OFF, // Pointer to picture data

7 NULL, // Pointer to palette

8 GUI_DRAW_BMP565

9 };

10

11 GUI_CONST_STORAGE GUI_BITMAP bmBUTTON_ON = {

12 165, // xSize

13 48, // ySize

14 330, // BytesPerLine

15 16, // BitsPerPixel

16 (unsigned char \*)_acBUTTON_ON, // Pointer to picture data

17 NULL, // Pointer to palette

18 GUI_DRAW_BMP565

19 };

上述代码中主要是两个GUI_BITMAP类型的结构体，存储了按钮贴图的尺寸、色彩深度、图像数据等基本信息。

代码清单 18‑6所展示的这一部分与上一个实验相比实际上并没有什么不同，就是一个正常的在对话框中新增控件的操作，真正需要注意的是对话框回调函数中对贴图的处理。

(2) 对话框回调函数

代码清单 18‑8 对话框回调函数

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE\* pMsg)

8 {

9 WM_HWIN hItem;

10 int NCode;

11 int Id;

12 static U8 button_3_flag = 0;

13

14 switch (pMsg->MsgId) {

15 case WM_INIT_DIALOG:

16 /\* 初始化Framewin控件 \*/

17 hItem = pMsg->hWin;

18 FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");

19 FRAMEWIN_SetFont(hItem, GUI_FONT_32_ASCII);

20 FRAMEWIN_SetTitleHeight(hItem, 32);

**21 /\* 初始化Button3 \*/**

**22 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_3);**

**23 BUTTON_SetBitmap(hItem, BUTTON_BI_UNPRESSED, &bmBUTTON_OFF);**

24 break;

25 case WM_NOTIFY_PARENT:

26 Id = WM_GetId(pMsg->hWinSrc);

27 NCode = pMsg->Data.v;

28 switch (Id) {

**29 case ID_BUTTON_3: // Notifications sent by 'Button3'**

**30 /\* 获取控件句柄 \*/**

**31 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_3);**

**32 switch (NCode) {**

**33 case WM_NOTIFICATION_CLICKED:**

**34 break;**

**35 case WM_NOTIFICATION_RELEASED:**

**36 /\* Button3已被释放 \*/**

**37 button_3_flag = ~button_3_flag;**

**38 if (button_3_flag != 0) {**

**39 BUTTON_SetBitmap(hItem, BUTTON_BI_UNPRESSED,**

**40 &bmBUTTON_ON);**

**41 } else**

**42 BUTTON_SetBitmap(hItem, BUTTON_BI_UNPRESSED,**

**43 &bmBUTTON_OFF);**

**44 break;**

**45 }**

**46 break;**

47 }

48 break;

49 default:

50 WM_DefaultProc(pMsg);

51 break;

52 }

53 }

实现按钮控件贴图的关键，就是使用BUTTON_SetBitmap函数。此函数可以用来设置并显示在指定的按钮控件中需要使用的位图。

1. WM_INIT_DIALOG消息

如代码清单 18‑8所示，Button3的初始化贴图在此消息中完成。此消息的粗体部分首先通过WM_GetDialogItem函数来自动建立并获取Button3的句柄。然后使用BUTTON_SetBitmap函数给Button3贴上一个默认为释放状态的按钮图形bmBUTTON_OFF。此函数的第二个参
数可以选择不同的按钮状态，以设置不同的贴图图形，共有三个选项可选：按钮已被按下BUTTON_BI_PRESSED、按钮没有被按下BUTTON_BI_UNPRESSED和按钮已被禁止BUTTON_BI_DISABLED。

2. WM_NOTIFY_PARENT消息

初始化贴图完成后，还需要在此消息中，根据不同的按钮状态切换贴图。本实验选用的按钮贴图分为开和关两种，只需要在按钮以释放的通知代码WM_NOTIFICATION_RELEASED内，根据变量button_3_flag记录的按钮状态调用BUTTON_SetBitmap函数来回切换两种贴图即可。

3. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

实现现象
^^^^

按钮贴图实验的现象如图 18‑4和图 18‑5所示，可以看到在上一个实验的基础上新增的Button3按钮在关闭和打开时的贴图效果。

|Button005|

图 18‑4 按钮贴图未被点击时的效果

|Button006|

图 18‑5 按钮贴图已被点击时的效果

.. |Button002| image:: media\Button002.png
   :width: 2.14557in
   :height: 0.46869in
.. |Button003| image:: media\Button003.png
   :width: 5.76806in
   :height: 3.46228in
.. |Button004| image:: media\Button004.png
   :width: 3.70787in
   :height: 0.43745in
.. |Button005| image:: media\Button005.png
   :width: 5.76806in
   :height: 3.46228in
.. |Button006| image:: media\Button006.png
   :width: 5.76806in
   :height: 3.46228in
