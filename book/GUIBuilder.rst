.. vim: syntax=rst

GUIBuilder的使用
-------------

GUIBuilder是Segger公司为emWin开发的一款界面编辑软件工具，用于显示界面的前期设计，或在不了解 C
语言的情况下设计界面。emWin的控件在GUIBuilder可以直接通过拖放来放置和调整大小，而不必编写源代码。可以按上下文菜单添加其他属性，可以通过编辑小部件的属性来微调。设计好的界面可以保存为 C
文件，直接添加进工程中使用。利用好GUIBuilder可以更快的完成emWin界面的初步设计。主界面见图 7‑1。

|GUIBui002|

图 7‑1 GUIBuilder主界面

GUIBuilder可以从Segger官方的模拟器工程中获得，也可以在STM32的CUBE库中找到。GUIBuilder在CUBE库中的路径如下：

STM32Cube_FW_F4_V1.24.1\Middlewares\ST\STemWin\Software\GUIBuilder.exe

使用步骤
~~~~

1) 创建框架窗口。首先点击GUIBuilder控件选择栏中的FrameWin控件，创建一个框架窗口。见图 7‑2；

|GUIBui003|

图 7‑2 添加FrameWin控件

2) 修改框架窗口尺寸。在控件属性框中，修改FrameWin控件的尺寸为800*480，见图 7‑3；

|GUIBui004|

图 7‑3 修改FrameWin控件大小

3) 设置标题栏高度。右键FrameWin控件，选择Set title height，在控件属性框中新增选项中设置标题栏高度为24，见图 7‑4；

|GUIBui005|

图 7‑4 设置标题栏高度

4) 设置标题内容。右键FrameWin控件，选择Set title text，输入“EmbeddedFire STM32F429”，见图 7‑5；

|GUIBui006|

图 7‑5 设置标题内容

5) 设置标题字体。同样右键FrameWin控件，选择Set font，在弹出的选择框中选择“GUI_FONT_24_ASCII”，见图 7‑6；

|GUIBui007|

图 7‑6 设置标题字体

6) 使用同样的方法添加一个按键和一个多选框，见图 7‑7；

|GUIBui008|

图 7‑7 添加按钮和多选框

7) 最后，保存并生成C文件。点击左上角菜单栏File->Save即可，GUIBuilder会自动生成对应的C文件。不过无法选择保存路径，生成的C文件会保存在GUIBuilder所在的文件夹中，见图 7‑8和图 7‑9；

|GUIBui009|

图 7‑8 保存C文件

|GUIBui010|

图 7‑9 C文件路径

GUIBuilder生成的C文件完整内容见代码清单 7‑1，由于篇幅限制，省略了部分注释代码。

代码清单 7‑1 FrameWin.c文件内容

1 #include "DIALOG.h"

2

3 /\*

4 \*

5 \* Defines

6 \*

7 \\*

8 \*/

9 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

10 #define ID_BUTTON_0 (GUI_ID_USER + 0x01)

11 #define ID_CHECKBOX_0 (GUI_ID_USER + 0x02)

12

13 /\*

14 \*

15 \* Static data

16 \*

17 \\*

18 \*/

19

20 /\*

21 \*

22 \* \_aDialogCreate

23 \*/

24 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

25 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

26 480, 0, 0x64, 0 },

27 { BUTTON_CreateIndirect, "Button", ID_BUTTON_0, 90, 60, 120, 40, 0,

28 0x0, 0 },

29 { CHECKBOX_CreateIndirect, "Checkbox", ID_CHECKBOX_0, 260, 65, 80,

30 30, 0, 0x0, 0 },

31 // USER START (Optionally insert additional widgets)

32 // USER END

33 };

34

35 /\*

36 \*

37 \* Static code

38 \*

39 \\*

40 \*/

41

42 /\*

43 \*

44 \* \_cbDialog

45 \*/

46 static void \_cbDialog(WM_MESSAGE \* pMsg)

47 {

48 WM_HWIN hItem;

49 int NCode;

50 int Id;

51

52 switch (pMsg->MsgId) {

53 case WM_INIT_DIALOG:

54 //

55 // Initialization of 'Framewin'

56 //

57 hItem = pMsg->hWin;

58 FRAMEWIN_SetTitleHeight(hItem, 24);

59 FRAMEWIN_SetText(hItem, "EmbeddedFire STM32F429");

60 FRAMEWIN_SetFont(hItem, GUI_FONT_24_ASCII);

61 //

62 // Initialization of 'Checkbox'

63 //

64 hItem = WM_GetDialogItem(pMsg->hWin, ID_CHECKBOX_0);

65 CHECKBOX_SetText(hItem, "Check");

66 break;

67 case WM_NOTIFY_PARENT:

68 Id = WM_GetId(pMsg->hWinSrc);

69 NCode = pMsg->Data.v;

70 switch (Id) {

71 case ID_BUTTON_0: // Notifications sent by 'Button'

72 switch (NCode) {

73 case WM_NOTIFICATION_CLICKED:

74 break;

75 case WM_NOTIFICATION_RELEASED:

76 break;

77 }

78 break;

79 case ID_CHECKBOX_0: // Notifications sent by 'Checkbox'

80 switch (NCode) {

81 case WM_NOTIFICATION_CLICKED:

82 break;

83 case WM_NOTIFICATION_RELEASED:

84 break;

85 case WM_NOTIFICATION_VALUE_CHANGED:

86 break;

87 }

88 break;

89 }

90 break;

91 default:

92 WM_DefaultProc(pMsg);

93 break;

94 }

95 }

96

97 /\*

98 \*

99 \* Public code

100 \*

101 \\*

102 \*/

103 /\*

104 \*

105 \* CreateFramewin

106 \*/

107 WM_HWIN CreateFramewin(void);

108 WM_HWIN CreateFramewin(void)

109 {

110 WM_HWIN hWin;

111

112 hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(

113 \_aDialogCreate), \_cbDialog, WM_HBKWIN, 0, 0);

114 return hWin;

115 }

从代码清单 7‑1我们可以看出，GUIBuilder生成的只是一个代码框架，整个界面需要实现的操作和控件自身的行为逻辑以及控件之间的通信等等都需要我们自己完成。

添加GUIBuilder代码到STM32
~~~~~~~~~~~~~~~~~~~~

我们以FreeRTOS_emWin移植模版为例，讲解如何添加GUIBuilder生成的C文件到工程，添加过程其实非常的简单。

1) 首先，用FrameWin.c替换掉原来模版中的MainTask.c，见图 7‑10；

|GUIBui011|

图 7‑10 替换MainTask.c

2) 然后添加头文件GUI.h和MainTask.h，并在最后添加MainTask函数即可完成添加，见代码清单 7‑2。

代码清单 7‑2 添加MainTask函数

1 // USER START (Optionally insert additional public code)

2 void MainTask(void)

3 {

4 CreateFramewin();

5

6 while (1) {

7 GUI_Delay(100);

8 }

9 }

10 // USER END

添加GUIBuilder代码到模拟器
~~~~~~~~~~~~~~~~~~

添加生成的C文件到模拟器的步骤与第4章添加BASIC_HelloWorld.c文件的步骤一致。同时，C文件需要添加的内容也与添加到STM32工程中时几乎相同，需要在FrameWin.c文件中添加GUI.h和MainTask函数。唯一不同的是，模拟器中必须添加emWin初始化函数GUI_Init()，
否则无法运行代码。见代码清单 7‑3。

代码清单 7‑3 模拟器中的MainTask函数

1 // USER START (Optionally insert additional public code)

2 void MainTask(void)

3 {

4 GUI_Init();

5

6 CreateFramewin();

7

8 while (1) {

9 GUI_Delay(100);

10 }

11 }

12 // USER END

.. |GUIBui002| image:: media\GUIBui002.png
   :width: 5.09843in
   :height: 3.59843in
.. |GUIBui003| image:: media\GUIBui003.png
   :width: 5.04167in
   :height: 1.53535in
.. |GUIBui004| image:: media\GUIBui004.png
   :width: 5.09055in
   :height: 3.59449in
.. |GUIBui005| image:: media\GUIBui005.png
   :width: 5.09055in
   :height: 3.59449in
.. |GUIBui006| image:: media\GUIBui006.png
   :width: 5.09055in
   :height: 3.59449in
.. |GUIBui007| image:: media\GUIBui007.png
   :width: 5.09055in
   :height: 3.59449in
.. |GUIBui008| image:: media\GUIBui008.png
   :width: 5.09055in
   :height: 3.59449in
.. |GUIBui009| image:: media\GUIBui009.png
   :width: 5.76806in
   :height: 1.85526in
.. |GUIBui010| image:: media\GUIBui010.png
   :width: 1.40625in
   :height: 2.17388in
.. |GUIBui011| image:: media\GUIBui011.png
   :width: 2.09349in
   :height: 0.71866in
