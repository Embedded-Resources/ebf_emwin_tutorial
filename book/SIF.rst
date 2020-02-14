.. vim: syntax=rst

SIF格式字体显示
---------

SIF格式，又被称为系统独立字体（System Independent Font）格式。也是一种包含字体信息的二进制数据块，可以使用emWin配套的字体转换器生成。上一章讲到的XBF字体所有数据都需要从外部存储器加载，所以在中文字符内容变化或窗口切换时影响系统流畅度。而SIF格式的字体就不存在这个问
题了，因为它在emWin运行时会全部加载在例如RAM中，使用时直接从RAM读取。不过SIF格式字体也有缺点，就是需要消耗大量RAM空间，在RAM较小的系统上无法使用此格式的大规模字库。

生成SIF字库
~~~~~~~

SIF格式字库同样是通过字体转换器生成，全字库的生成步骤与生成XBF格式全字库的步骤完全一致，不过这次我们稍微变化一下，只生成我们需要的部分。具体步骤如下：

1) 选择需要生成的字体类型。首先还是跟XBF格式一样，双击FontCvtST.exe打开字体转换器，在弹出来的对话框中选择Extended, antialised, 4bpp，也就是带4bpp抗锯齿的扩展比例位图字体，然后编码格式选择16Bit UNICODE，见图 38‑1；

|SIF002|

图 38‑1 选择需要生成的字体类型

2) 点击OK后，选择字体为思源黑体，字形常规，大小36，尺寸单位选择Pixels，见图 38‑2；

|SIF003|

图 38‑2 选择字体和大小

3) 失能所有字符。为了让字体转换器只生成我们想要的部分，需要失能所以字符，字体转换器左上角菜单栏左键点击Edit-> Disable all characters，见图 38‑3；

|SIF004|

图 38‑3 失能所有字符

4) 制作模式文件。接着新建一个txt文件，把想要生成的字符输入txt文件并保存为UTF-16 LE编码格式，如果没有这个格式则保存为Unicode，见图 38‑4和图 38‑5；

|SIF005|

图 38‑4 制作模式文件

|SIF006|

图 38‑5 保存为UTF-16 LE编码格式

5) 导入模式文件。将制作好的模式文件导入字体转换器，在字体转换器左上角菜单栏左键单击Edit->Read parttern file，见图 38‑6；

|SIF007|

图 38‑6 导入模式文件

6) 保存字库。导入模式文件之后，点击字体转换器左上角的File，然后选择Save As，设置文件名为思源黑体36_4bpp，保存文件格式为.sif，见图 38‑7和图 38‑8。

|SIF008|

图 38‑7 保存字库

|SIF009|

图 38‑8 选择保存类型

7) 最后，等待字体转换器转换完成。完成后会在窗口左下角显示“Ready”，见图 38‑9。

|SIF010|

图 38‑9 等待转换完成

至此，一个SIF格式的字库就生成完毕了，使用相同的方法再制作一个新宋体18_4bpp.sif，和思源黑体36_4bpp.sif一起放到SD卡的Font目录下。

SIF字体相关API
~~~~~~~~~~

表格 38‑1 SIF字体相关API

==================== ================================================
函数名               描述
==================== ================================================
GUI_SIF_CreateFont() 通过将指针传递给系统独立字体数据来创建和选择字体
GUI_SIF_DeleteFont() 删除由GUI_SIF_CreateFont()创建的字体
==================== ================================================

GUI_SIF_CreateFont()
''''''''''''''''''''

通过将指针传递给系统独立字体数据来设置要使用的字体。

代码清单 38‑1 函数原型

1 void GUI_SIF_CreateFont(const void \*pFontData, GUI_FONT \*pFont, const

2 GUI_SIF_TYPE \*pFontType);

1) pFontData：指向系统独立字体数据的指针；

2) pFont：指向RAM中由该函数填充的GUI_FONT结构的指针；

3) pFontType：字体类型，可选值见表格 38‑2。

表格 38‑2 pFontType可选值

========================= ==========================
pFontType可选值           描述
========================= ==========================
GUI_SIF_TYPE_PROP         比例字体
GUI_SIF_TYPE_PROP_EXT     扩展比例字体
GUI_SIF_TYPE_PROP_FRM     扩展比例字体，带边框
GUI_SIF_TYPE_PROP_AA2     比例字体，带2bpp抗锯齿
GUI_SIF_TYPE_PROP_AA4     比例字体，带4bpp抗锯齿
GUI_SIF_TYPE_PROP_AA2_EXT 扩展比例字体，带2bpp抗锯齿
GUI_SIF_TYPE_PROP_AA4_EXT 扩展比例字体，带4bpp抗锯齿
========================= ==========================

在使用这个函数的时候，emWin需要用字体信息填充GUI_FONT结构。用户需要在参数pFont中传递一个指向这个结构的指针。在使用字体期间，此结构的内容必须保持有效。

该函数不知道应该创建哪种字体。要告诉函数要创建的字体的类型，必须在参数pFontType中传递。这样做是为了避免不必要的代码链接。

GUI_SIF_DeleteFont()
''''''''''''''''''''

删除参数pFont指向的字体。

代码清单 38‑2 函数原型

1 void GUI_SIF_DeleteFont(GUI_FONT \*pFont);

1) pFont：指向要删除的字体的指针。

使用GUI_SIF_CreateFont()创建的字体之后，如果不再使用，应该删除该字体。

SIF格式字体显示实验
~~~~~~~~~~~

代码分析
^^^^

在本实验中我们提供了两种字库存放区域，一种是SPI FLASH的非文件系统区域，一种是SD卡文件系统区域，如代码清单 38‑3。

代码清单 38‑3字库存放位置（GUIFont_Create.h文件）

1 //设置SIF字体存储的位置：

2 //FLASH非文件系统区域（推荐）USE_FLASH_FONT 0

3 //SD卡文件系统区域 USE_SDCARD_FONT 1

4 #define SIF_FONT_SOURCE 1

5

6 /\*

7 （速度最快）字库在FLASH的非文件系统区域，使用前需

8 要往FLASH特定地址拷贝字体文件 \*/

9 #define USE_FLASH_FONT 0

10 /\*

11 （速度中等）字库存储在SD卡文件系统区域，调试比较

12 方便，直接使用读卡器从电脑拷贝字体文件即可 \*/

13 #define USE_SDCARD_FONT 1

上述代码中，SIF_FONT_SOURCE决定着程序从什么位置读取字库数据，为0时，从SPI FLASH中读取字库；为1时，则从SD卡中读取字库。在GUIFont_Create.c文件中，提供了从SPI_FLASH或者SD卡读取字库数据的函数。我们选择从SD卡读取字库。

如果从SPI FLASH中读取字库，请先运行《刷外部FLASH程序（烧录STemWIN资源文件）》例程，将emWin相关的资源烧录到SPI FLASH中，否则字库实验无法进行。

(1) 创建字体

代码清单 38‑4 FONT_SIF_GetData函数（GUIFont_Create.c文件）

1 /*\*

2 \* @brief 加载字体数据到SDRAM

3 \* @note 无

4 \* @param res_name：要加载的字库文件名

5 \* @retval Fontbuffer：已加载好的字库数据

6 \*/

7 void \*FONT_SIF_GetData(const char \*res_name)

8 {

9 uint8_t \*Fontbuffer;

10 GUI_HMEM hFontMem;

11

12 #elif (SIF_FONT_SOURCE == USE_SDCARD_FONT)

13

14 if (storage_init_flag == 0) {

15 /\* 挂载sd卡文件系统 \*/

16 res = f_mount(&fs,FONT_STORAGE_ROOT_DIR,1);

17 storage_init_flag = 1;

18 }

19

20 /\* 打开字库 \*/

21 res = f_open(&fnew , res_name, FA_OPEN_EXISTING \| FA_READ);

22 if (res != FR_OK) {

23 printf("Open font failed! res = %d\r\n", res);

24 while (1);

25 }

26

27 /\* 申请一块动态内存空间 \*/

28 hFontMem = GUI_ALLOC_AllocZero(fnew.fsize);

29 /\* 转换动态内存的句柄为指针 \*/

30 Fontbuffer = GUI_ALLOC_h2p(hFontMem);

31

32 /\* 读取内容 \*/

33 res = f_read(&fnew, Fontbuffer, fnew.fsize, &br);

34 if (res != FR_OK) {

35 printf("Read font failed! res = %d\r\n", res);

36 while (1);

37 }

38 f_close(&fnew);

39

40 return Fontbuffer;

41 #endif

42 }

首先是字库数据加载函数，如代码清单 38‑4所示，由于函数实际代码较长，我们只截取了从SD卡中读取的部分。FONT_SIF_GetData函数负责将字库文件从SD卡加载到SDRAM中，操作步骤其实跟图片显示很类似，都是先申请一块动态内存并转换为指针，然后使用f_read函数把字库读取到动态内存中。

代码清单 38‑5 Create_SIF_Font函数（GUIFont_create.c文件）

1 /\* 字库结构体 \*/

2 GUI_FONT FONT_SIYUANHEITI_36_4BPP;

3 GUI_FONT FONT_XINSONGTI_18_4BPP;

4

5 /\* 字库缓冲区 \*/

6 uint8_t \*SIFbuffer36;

7 uint8_t \*SIFbuffer18;

8

9 /*\*

10 \* @brief 创建SIF字体

11 \* @param 无

12 \* @retval 无

13 \*/

14 void Create_SIF_Font(void)

15 {

16 /\* 获取字体数据 \*/

17 SIFbuffer18 = FONT_SIF_GetData(FONT_XINSONGTI_18_ADDR);

18 SIFbuffer36 = FONT_SIF_GetData(FONT_SIYUANHEITI_36_ADDR);

19

20 /\* 新宋体18 \*/

21 GUI_SIF_CreateFont(SIFbuffer18,/\* 已加载到内存中的字体数据 \*/

22 &FONT_XINSONGTI_18_4BPP,/\* GUI_FONT 字体结构体指针 \*/

23 GUI_SIF_TYPE_PROP_AA4_EXT);/\* 字体类型 \*/

24 /\* 思源黑体36 \*/

25 GUI_SIF_CreateFont(SIFbuffer36,/\* 已加载到内存中的字体数据 \*/

26 &FONT_SIYUANHEITI_36_4BPP,/\* GUI_FONT 字体结构体指针 \*/

27 GUI_SIF_TYPE_PROP_AA4_EXT);/\* 字体类型 \*/

28 }

如代码清单 38‑5所示，字库数据成功加载到SDRAM后，调用GUI_SIF_CreateFont函数创建SIF字体，此函数的第三个参数为字体类型，必须严格等于字库转换器中选择的类型。

(2) 创建并转换待显示文本

由于keil5文本编辑器的BUG问题，SIF格式字体显示实验同样需要多一个单独转字符编码的步骤，具体如何转换字符编码请查看37.4.1 小节，在此不再重复说明。

(3) 创建对话框

转换好代码之后就还是像其他讲解控件的例程一样，创建对话框。

代码清单 38‑6 创建对话框（MainTask.c文件）

1 /\* 控件ID \*/

2 #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)

3 #define ID_TEXT_0 (GUI_ID_USER + 0x01)

4 #define ID_TEXT_1 (GUI_ID_USER + 0x02)

5 #define ID_MULTIEDIT_0 (GUI_ID_USER + 0x03)

6 #define ID_BUTTON_0 (GUI_ID_USER + 0x04)

7 #define ID_BUTTON_1 (GUI_ID_USER + 0x05)

8

9 /\* 资源表 \*/

10 static const GUI_WIDGET_CREATE_INFO \_aDialogCreate[] = {

11 { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,

12 480, 0, 0x0, 0 },

13 { TEXT_CreateIndirect, "Text", ID_TEXT_0, 20, 35, 580, 50, 0, 0x64,

14 0 },

15 { TEXT_CreateIndirect, "Text", ID_TEXT_1, 20, 80, 740, 80, 0, 0x64,

16 0 },

17 { MULTIEDIT_CreateIndirect, "Multiedit", ID_MULTIEDIT_0, 20, 200,

18 480, 130, 0, 0x0, 0 },

19 { BUTTON_CreateIndirect, "Button", ID_BUTTON_0, 560, 200, 140, 36,

20 0, 0x0, 0 },

21 { BUTTON_CreateIndirect, "Button", ID_BUTTON_1, 560, 292, 140, 36,

22 0, 0x0, 0 },

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

在代码清单 38‑6中我们定义了6个ID：1个框架窗口ID、2个文本控件ID、1个多行文本控件和2个按钮控件ID，这些控件都用来做中文显示。

(4) 对话框回调函数

代码清单 38‑7 对话框回调函数

1 /*\*

2 \* @brief 对话框回调函数

3 \* @note 无

4 \* @param pMsg：消息指针

5 \* @retval 无

6 \*/

7 static void \_cbDialog(WM_MESSAGE \* pMsg)

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

18 FRAMEWIN_SetText(hItem, Framewin_text);

19 FRAMEWIN_SetFont(hItem, &FONT_SIYUANHEITI_36_4BPP);

20 /\* 初始化TEXT0 \*/

21 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_0);

22 TEXT_SetText(hItem, text);

23 TEXT_SetFont(hItem, &FONT_XINSONGTI_18_4BPP);

24 /\* 初始化TEXT1 \*/

25 hItem = WM_GetDialogItem(pMsg->hWin, ID_TEXT_1);

26 TEXT_SetText(hItem, text);

27 TEXT_SetFont(hItem, &FONT_SIYUANHEITI_36_4BPP);

28 /\* 初始化MULTIEDIT0 \*/

29 hItem = WM_GetDialogItem(pMsg->hWin, ID_MULTIEDIT_0);

30 MULTIEDIT_SetReadOnly(hItem, 1);

31 MULTIEDIT_SetBufferSize(hItem, 200);

32 MULTIEDIT_SetWrapWord(hItem);

33 MULTIEDIT_SetText(hItem, MULTIEDIT_text);

34 MULTIEDIT_SetFont(hItem, &FONT_SIYUANHEITI_36_4BPP);

35 MULTIEDIT_SetTextColor(hItem, MULTIEDIT_CI_READONLY, GUI_GREEN)

36 ;

37 MULTIEDIT_SetBkColor(hItem, MULTIEDIT_CI_READONLY, GUI_BLACK);

38 MULTIEDIT_ShowCursor(hItem, 0);

39 /\* 初始化Button0 \*/

40 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_0);

41 BUTTON_SetFont(hItem, &FONT_SIYUANHEITI_36_4BPP);

42 BUTTON_SetText(hItem, BUTTON_text[0]);

43 /\* 初始化Button1 \*/

44 hItem = WM_GetDialogItem(pMsg->hWin, ID_BUTTON_1);

45 BUTTON_SetFont(hItem, &FONT_SIYUANHEITI_36_4BPP);

46 BUTTON_SetText(hItem, BUTTON_text[1]);

47 break;

48 case WM_NOTIFY_PARENT:

49 Id = WM_GetId(pMsg->hWinSrc);

50 NCode = pMsg->Data.v;

51 switch (Id) {

52 case ID_MULTIEDIT_0: // Notifications sent by 'Multiedit'

53 switch (NCode) {

54 case WM_NOTIFICATION_CLICKED:

55 break;

56 case WM_NOTIFICATION_RELEASED:

57 break;

58 case WM_NOTIFICATION_VALUE_CHANGED:

59 break;

60 }

61 break;

62 case ID_BUTTON_0: // Notifications sent by 'Button'

63 switch (NCode) {

64 case WM_NOTIFICATION_CLICKED:

65 break;

66 case WM_NOTIFICATION_RELEASED:

67 break;

68 }

69 break;

70 case ID_BUTTON_1: // Notifications sent by 'Button'

71 switch (NCode) {

72 case WM_NOTIFICATION_CLICKED:

73 break;

74 case WM_NOTIFICATION_RELEASED:

75 break;

76 }

77 break;

78 }

79 break;

80 default:

81 WM_DefaultProc(pMsg);

82 break;

83 }

84 }

1. WM_INIT_DIALOG消息

在上述代码中，设置了对话框也就是框架窗口的标题栏高度为32像素，字体高度32像素，并在标题栏中显示“STemWIN@EmbedFire STM32F429”。

代码清单 38‑7中TEXT0控件使用新宋体18号字体，其余控件均使用思源黑体36号字体带2bpp抗锯齿。接着就都是各个控件的常规初始化配置，这些在前面的章节已有讲解，在此不再赘述了。

2. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。

(5) MainTask函数

代码清单 38‑8 MainTask函数（MainTask.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 /\* 启用UTF-8编码 \*/

10 GUI_UC_SetEncodeUTF8();

11 /\* 创建字体 \*/

12 Create_SIF_Font();

13 /\* 创建窗口 \*/

14 CreateFramewin();

15 while (1) {

16 GUI_Delay(100);

17 }

18 }

字体创建完成之后如果少了一步操作，那么整个中文字库还是不能用。如代码清单 38‑8的GUI主任务函数MainTask所示，在创建我们需要的中文字体之前必须首先使用GUI_UC_SetEncodeUTF8函数开启emwin的UTF-8编码，这样才能保证中文字符的正常显示。

实验现象
^^^^

SIF字体显示实验的现象如图 38‑10所示，可以看到两种不同字体的显示效果。

|SIF011|

图 38‑10 SIF格式字体显示实验现象

.. |SIF002| image:: media\SIF002.png
   :width: 2.71654in
   :height: 2.51181in
.. |SIF003| image:: media\SIF003.png
   :width: 4.09449in
   :height: 3.6063in
.. |SIF004| image:: media\SIF004.png
   :width: 3.18504in
   :height: 3.47638in
.. |SIF005| image:: media\SIF005.png
   :width: 4.14567in
   :height: 2.33465in
.. |SIF006| image:: media\SIF006.png
   :width: 5.14173in
   :height: 1.02756in
.. |SIF007| image:: media\SIF007.png
   :width: 3.08268in
   :height: 3.48425in
.. |SIF008| image:: media\SIF008.png
   :width: 4.33858in
   :height: 3.38583in
.. |SIF009| image:: media\SIF009.png
   :width: 5.42913in
   :height: 0.98819in
.. |SIF010| image:: media\SIF010.png
   :width: 2.33071in
   :height: 2.05118in
.. |SIF011| image:: media\SIF011.png
   :width: 5.76806in
   :height: 3.46083in
